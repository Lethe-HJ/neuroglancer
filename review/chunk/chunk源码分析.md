## 1. 系统架构

主线程（UI线程）：负责渲染和用户交互

WebWorker线程：负责数据下载、解码和处理

RPC通信：两个线程之间通过RPC进行通信

### 疑问1. chunk数据是如何从worker中转移到主线程中的? 使用了零拷贝的方法么? 内存共享? 类似所有权转移?

解释: 使用Transferable Objects实现零拷贝

```ts
serialize(msg: any, transfers: any[]) {
  super.serialize(msg, transfers);
  let {vertexPositions, indices, vertexNormals, vertexAttributes} = this.data!;
  msg['vertexPositions'] = vertexPositions;
  msg['indices'] = indices;
  msg['vertexNormals'] = vertexNormals;
  msg['vertexAttributes'] = vertexAttributes;
  const transferSet = new Set<ArrayBuffer>();
  transferSet.add(vertexPositions!.buffer);
  transferSet.add(indices!.buffer);
  transferSet.add(vertexNormals!.buffer);
  for (const data of vertexAttributes!) {
    transferSet.add(data.buffer);
  }
  transfers.push(...transferSet);
  this.data = null; // 传输后立即释放Worker端的引用
}
```

```ts
invoke(name: string, x: any, transfers?: any[]) {
  x.functionName = name;
  if (DEBUG_MESSAGES) {
    console.trace('Sending message', x);
  }
  this.target.postMessage(x, transfers);
}
```

通过`postMessage(data, transfers)`的第二个参数实现Transferable传输, 这个传输是直接内存共享的方式, 零拷贝, 速度快, 而且自带所有权转移, 传输后, 原来的worker已经访问到这个ArrayBuffer, 可以避免数据混乱问题。

关键特点：
零拷贝传输：通过transfers数组传递ArrayBuffer，实现所有权转移
内存所有权转移：传输后Worker端立即将数据设为null，避免双重引用
高效性：大型ArrayBuffer不需要复制，只是转移控制权


## 2. Chunk状态定义

```typescript
export enum ChunkState {
  GPU_MEMORY = 0,           // 存储在GPU内存中（同时也在系统内存中）
  SYSTEM_MEMORY = 1,        // 仅存储在主线程系统内存中
  SYSTEM_MEMORY_WORKER = 2, // 存储在Worker线程系统内存中
  DOWNLOADING = 3,          // 正在下载中
  QUEUED = 4,              // 排队等待下载
  NEW = 5,                 // 刚刚创建
  FAILED = 6,              // 下载失败
  EXPIRED = 7,             // 已过期
}
```

### 疑问1 存储在gpu内存中是什么意思? 是以纹理的形式存在于gpu内存中么

解释: chunk数据是以纹理的形式存在于gpu内存中

GPU内存存储的具体形式
以纹理和缓冲区形式存储
GPU内存中的数据主要以两种形式存在：

WebGL缓冲区（VBO/EBO）：

```ts
function copyMeshDataToGpu(gl: GL, chunk: FragmentChunk|MultiscaleFragmentChunk) {
  chunk.vertexBuffer =
      Buffer.fromData(gl, chunk.meshData.vertexPositions, gl.ARRAY_BUFFER, gl.STATIC_DRAW);
  chunk.indexBuffer =
      Buffer.fromData(gl, chunk.meshData.indices, gl.ELEMENT_ARRAY_BUFFER, gl.STATIC_DRAW);
  chunk.normalBuffer =
      Buffer.fromData(gl, chunk.meshData.vertexNormals, gl.ARRAY_BUFFER, gl.STATIC_DRAW);
}
```

WebGL纹理：
```ts
copyToGPU(gl: GL, attributeFormats: TextureFormat[]) {
  const getBufferTexture = (data: Float32Array, format: TextureFormat) => {
    let texture = gl.createTexture();
    gl.bindTexture(WebGL2RenderingContext.TEXTURE_2D, texture);
    setOneDimensionalTextureData(gl, format, data);
    return texture;
  };
  this.vertexTexture = getBufferTexture(this.vertexPositions, vertexPositionTextureFormat);
  this.normalTexture = getBufferTexture(this.vertexNormals, vertexNormalTextureFormat);
  this.vertexAttributeTextures =
      this.vertexAttributes.map((data, i) => getBufferTexture(data, attributeFormats[i]));
  gl.bindTexture(WebGL2RenderingContext.TEXTURE_2D, null);
}
```


### 疑问2 为什么会同时在cpu和cpu中都存在? 

解释:
+ GPU内存用于渲染，CPU内存用于数据处理和计算
+ 某些操作（如碰撞检测、数据修改）需要CPU访问
+ 作为备份，GPU内存不足时可以快速恢复

### 疑问3 主线程的系统内存中 因为受到浏览器限制, 单个类型数组可使用的的最大内存是多少?, worker中单个类型数组的可使用的最大内存是多少?

解释:

具体限制：
1. GPU内存：500MB (5e8字节)，最多10万个chunk
2. 系统内存：1GB (1e9字节)，最多100万个chunk
3. 单个TypedArray限制：受浏览器实现影响，通常约2GB-4GB
4. Worker内存：与主线程共享相同的限制

实际约束因素：
1. 浏览器进程内存限制
2. 设备物理内存
3. GPU显存大小
4. 操作系统虚拟内存管理

1个chunk对应一个逻辑数据单元, 1个chunk中可能有多个类型数组

```ts
{
  gpuMemory: new CapacitySpecification({defaultItemLimit: 1e5, defaultSizeLimit: 5e8}),
  systemMemory: new CapacitySpecification({defaultItemLimit: 1e6, defaultSizeLimit: 1e9}),
  download: new CapacitySpecification({defaultItemLimit: 100, defaultSizeLimit: Number.POSITIVE_INFINITY}),
  compute: new CapacitySpecification({defaultItemLimit: 128, defaultSizeLimit: 5e8}),
}
```


3. 疑问3: 对于chunk的渲染方式是怎样的, 是全部一起刷还是指定区域渲染, 其他区域维持原样?

解释:

结论: 目前ng的代码中, 对于单个层来说 是视锥体内的区域全部一起渲染, 而不是对于更新的chunk所在的区域进行单独指定区域渲染, 虽然这个做法webgl支持且可能对性能提升较大, 但是实现复杂. 简单来说就是视锥体内的chunk需要逐层全部渲染, 而不是指定区域做增量渲染

渲染策略：基于视锥体的选择性渲染

```ts
isReady(renderContext: PerspectiveViewReadyRenderContext, attachment: VisibleLayerInfo<PerspectivePanel, ThreeDimensionalRenderLayerAttachmentState>) {
  // ...
  let hasAllChunks = true;
  forEachVisibleSegment(displayState.segmentationGroupState.value, (objectId) => {
    if (!hasAllChunks) return;
    const key = getObjectKey(objectId);
    const manifestChunk = chunks.get(key);
    if (manifestChunk === undefined) {
      hasAllChunks = false;
      return;
    }
    const {manifest} = manifestChunk;
    getMultiscaleChunksToDraw(
        manifest, modelViewProjection, clippingPlanes, detailCutoff, projectionParameters.width,
        projectionParameters.height, (lod, chunkIndex) => {
          hasAllChunks = hasAllChunks && hasFragmentChunk(fragmentChunks, key, lod, chunkIndex);
          return hasAllChunks;
        }, () => {});
  });
  return hasAllChunks;
}
```

分层渲染策略

```ts
updateRendering() {
  // ...
  this.renderingStale = false;
  this.updateVisibleLayers.flush();
  this.updateVisibleSources();

  let {gl, offscreenFramebuffer} = this;
  offscreenFramebuffer.bind(width, height);
  gl.disable(gl.SCISSOR_TEST);

  gl.clearColor(0, 0, 0, 0);
  gl.colorMask(true, true, true, true);
  gl.clear(WebGL2RenderingContext.COLOR_BUFFER_BIT);
  let renderLayerNum = 0;
  const wireFrame = this.wireFrame.value;
  const renderContext = {sliceView: this, projectionParameters, wireFrame};
  for (let renderLayer of this.visibleLayerList) {
    const histogramCount = wireFrame ? 0 : renderLayer.getDataHistogramCount();
    let framebuffer = this.getOffscreenFramebufferWithHistograms(histogramCount);
    framebuffer.bind(width, height);
    // 只清理需要的缓冲区
    for (let i = 0; i < histogramCount; ++i) {
      gl.clearBufferfv(WebGL2RenderingContext.COLOR, 1 + i, kZeroVec4);
    }
    gl.enable(WebGL2RenderingContext.DEPTH_TEST);
    gl.depthFunc(WebGL2RenderingContext.LESS);
    gl.clearDepth(1);
    gl.clear(WebGL2RenderingContext.DEPTH_BUFFER_BIT);
    renderLayer.setGLBlendMode(gl, renderLayerNum);
    renderLayer.draw(renderContext);  // 只渲染当前图层
    ++renderLayerNum;
  }
  gl.disable(WebGL2RenderingContext.BLEND);
  gl.disable(WebGL2RenderingContext.DEPTH_TEST);
  offscreenFramebuffer.unbind();
}
```

渲染策略特点：
按图层增量渲染：每个图层单独渲染到framebuffer
视锥体裁剪：只渲染可见区域内的chunk
LOD机制：根据距离选择合适的细节级别
优雅降级：非GPU_MEMORY状态的chunk被跳过，等待状态提升
异步更新：chunk状态变化时触发重绘


## chunk状态流转

```text

NEW → QUEUED → DOWNLOADING → SYSTEM_MEMORY_WORKER → SYSTEM_MEMORY → GPU_MEMORY
 ↓       ↓         ↓              ↓                    ↓              ↓
EXPIRED EXPIRED   FAILED        QUEUED              QUEUED        SYSTEM_MEMORY
```

详细状态流转过程

1. 初始阶段

NEW: chunk刚被创建，还未进入管理系统

QUEUED: 加入下载队列，等待网络资源

2. 下载阶段

DOWNLOADING: 正在从网络下载数据

FAILED: 下载失败，可能重新排队或标记为过期

3. 内存管理阶段

SYSTEM_MEMORY_WORKER: 下载完成，数据存储在Worker线程内存中

SYSTEM_MEMORY: 数据传输到主线程内存中

GPU_MEMORY: 数据上传到GPU内存，可用于渲染

4. 清理阶段

EXPIRED: 不再需要的chunk被标记为过期并清理

## 优先级

```ts
export enum ChunkPriorityTier {
  FIRST_TIER = 0,
  FIRST_ORDERED_TIER = 0,
  VISIBLE = 0,        // 当前可见，需要立即渲染
  PREFETCH = 1,       // 预期很快可见
  LAST_ORDERED_TIER = 1,
  RECENT = 2,         // 既不可见也不需要预取
  LAST_TIER = 2
}
```

## 视锥体

视锥体内的chunk的可见性通过以下代码确定

```ts
function forEachVolumetricChunkWithinFrustrum<RLayer extends MultiscaleVolumetricDataRenderLayer>(
    clippingPlanes: Float32Array, transformedSource: TransformedSource<RLayer>,
    callback: (positionInChunks: vec3, clippingPlanes: Float32Array) => void,
    predicate: (
        xLower: number, yLower: number, zLower: number, xUpper: number, yUpper: number,
        zUpper: number, clippingPlanes: Float32Array) => boolean) {
  //  使用AABB与视锥体相交测试
  // 遍历所有可能的chunk位置
  // 调用predicate函数判断chunk是否在视锥体内
}
```

[使用AABB与视锥体相交测试详细解释](./AABB与视锥体相交测试.md)

Chunk空间划分和位置遍历

```typescript
// 3D空间被划分为规则的chunk网格
interface ChunkGrid {
  chunkSize: vec3;           // 每个chunk的尺寸
  gridOrigin: vec3;          // 网格起始点
  gridBounds: {             // 网格边界
    lower: vec3;
    upper: vec3;
  };
}
```

```typescript
function forEachVolumetricChunkWithinFrustrum<RLayer extends MultiscaleVolumetricDataRenderLayer>(
    clippingPlanes: Float32Array, 
    transformedSource: TransformedSource<RLayer>,
    callback: (positionInChunks: vec3, clippingPlanes: Float32Array) => void,
    predicate: (xLower: number, yLower: number, zLower: number, 
                xUpper: number, yUpper: number, zUpper: number, 
                clippingPlanes: Float32Array) => boolean) {
  
  const {chunkSize, gridBounds} = transformedSource.chunkLayout;
  const {lower, upper} = gridBounds;
  
  // 三重嵌套循环遍历所有chunk位置
  for (let x = lower[0]; x <= upper[0]; x++) {
    for (let y = lower[1]; y <= upper[1]; y++) {
      for (let z = lower[2]; z <= upper[2]; z++) {
        // 计算当前chunk的世界坐标边界
        const chunkWorldPos = vec3.fromValues(x, y, z);
        const xLower = x * chunkSize[0];
        const yLower = y * chunkSize[1]; 
        const zLower = z * chunkSize[2];
        const xUpper = xLower + chunkSize[0];
        const yUpper = yLower + chunkSize[1];
        const zUpper = zLower + chunkSize[2];
        
        // 使用predicate函数测试可见性
        if (predicate(xLower, yLower, zLower, xUpper, yUpper, zUpper, clippingPlanes)) {
          callback(chunkWorldPos, clippingPlanes);
        }
      }
    }
  }
}
```

**遍历优化：**
实际实现中会有多种优化：
1. **层次遍历**：先测试大的区域，再细分
2. **早期退出**：如果父区域不可见，跳过所有子区域
3. **空间索引**：使用八叉树或其他空间数据结构加速
4. **层次优化**: 使用lod来优化

视锥体内Chunk的优先级分配

```ts
export function getPriorityTier(visibility: number): ChunkPriorityTier {
  return visibility === Number.POSITIVE_INFINITY ? ChunkPriorityTier.VISIBLE :
                                                   ChunkPriorityTier.PREFETCH;
}
```

疑问: 这一段是什么意思 为什么不满足条件就是预期很快可见

视锥体内Chunk的状态流程
对于视锥体内的chunk：
发现阶段: 通过视锥体裁剪算法识别需要的chunk
请求阶段: 将chunk标记为VISIBLE优先级，状态为QUEUED
下载阶段: 高优先级chunk优先下载，状态变为DOWNLOADING
处理阶段: 下载完成后在Worker中处理，状态为SYSTEM_MEMORY_WORKER
传输阶段: 数据传输到主线程，状态为SYSTEM_MEMORY
渲染准备: 上传到GPU内存，状态为GPU_MEMORY，可用于渲染
