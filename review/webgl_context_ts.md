# webgl/context.ts 源码分析

## 文件概述

**路径**: `src/webgl/context.ts`  
**功能**: WebGL 上下文初始化和扩展管理  
**许可证**: Apache License 2.0

## 核心内容

### 1. GL 接口扩展

```typescript
export interface GL extends WebGL2RenderingContext {
  memoize: Memoize<any, RefCounted>;      // 内存缓存
  maxTextureSize: number;                  // 最大纹理尺寸
  maxTextureImageUnits: number;            // 最大纹理单元数
  max3dTextureSize: number;               // 3D 纹理最大尺寸
  tempTextureUnit: number;                 // 临时纹理单元
}
```

### 2. WebGL 初始化

```typescript
function initializeWebGL(canvas: HTMLCanvasElement): GL
```

**初始化步骤**:
1. 创建 WebGL2 上下文
2. 获取 GPU 能力参数
3. 启用必需扩展
4. 配置可选扩展

### 3. 必需的扩展

| 扩展名 | 用途 |
|--------|------|
| EXT_color_buffer_float | 浮点颜色缓冲区 |

### 4. 可选扩展

| 扩展名 | 用途 |
|--------|------|
| EXT_float_blend | 浮点混合 |

## 主要功能

1. **Canvas 配置**: antialias: false, stencil: true
2. **GPU 能力检测**: 获取最大纹理尺寸
3. **扩展管理**: 必需/可选扩展
4. **调试支持**: preserveDrawingBuffer

## 知识点

- **WebGL2**: WebGL 2.0 特性
- **GPU 限制**: 纹理尺寸、纹理单元数
- **扩展机制**: WebGL 扩展系统
