# webgl/buffer.ts 源码分析

## 文件概述

**路径**: `src/webgl/buffer.ts`  
**功能**: GPU 缓冲区管理 - 顶点缓冲区、索引缓冲区管理  
**许可证**: Apache License 2.0

## 核心类

### GLBuffer 类

```typescript
class GLBuffer implements Disposable {
  buffer: WebGLBuffer | null;
  
  constructor(
    public gl: WebGL2RenderingContext,
    public bufferType: BufferType = WebGL2RenderingContext.ARRAY_BUFFER
  )
}
```

## 缓冲区类型

| 类型 | WebGL 常量 | 用途 |
|------|------------|------|
| ARRAY_BUFFER | 0x8892 | 顶点数据 |
| ELEMENT_ARRAY_BUFFER | 0x8893 | 索引数据 |

## 主要方法

### 1. 绑定

```typescript
bind()
// 绑定缓冲区到当前上下文
```

### 2. 属性绑定

```typescript
// 浮点属性
bindToVertexAttrib(
  location,           // 属性位置
  componentsPerVertexAttribute,  // 每个顶点的分量数
  attributeType?,     // 数据类型
  normalized?,        // 是否归一化
  stride?,           // 跨距
  offset?            // 偏移
)

// 整数属性
bindToVertexAttribI(...)
```

### 3. 数据传输

```typescript
setData(data: ArrayBufferView, usage?: WebGLBufferUsage)
// 上传数据到 GPU
```

## 缓冲区用法

| 用法 | WebGL 常量 | 用途 |
|------|------------|------|
| STATIC_DRAW | 0x88E4 | 很少更新 |
| DYNAMIC_DRAW | 0x88E8 | 频繁更新 |
| STREAM_DRAW | 0x88E0 | 偶尔更新 |

## 知识点

- **VBO**: 顶点缓冲对象
- **EBO**: 索引缓冲对象
- **数据上传**: CPU → GPU
- **属性指针**: 顶点属性配置
