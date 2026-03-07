# webgl/texture.ts 源码分析

## 文件概述

**路径**: `src/webgl/texture.ts`  
**功能**: WebGL 纹理管理 - 纹理创建、参数设置、调整大小  
**许可证**: Apache License 2.0

## 核心函数

### 1. 纹理参数设置

```typescript
// 2D 纹理参数
setRawTextureParameters(gl: WebGL2RenderingContext)
// - NEAREST 过滤
// - CLAMP_TO_EDGE 环绕

// 3D 纹理参数  
setRawTexture3DParameters(gl: WebGL2RenderingContext)
// - 额外设置 R 环绕
```

### 2. 纹理创建/调整

```typescript
resizeTexture(
  gl: GL,
  texture: WebGLTexture | null,
  width: number,
  height: number,
  internalFormat?: number,
  format?: number,
  dataType?: number
)
```

## 纹理参数详解

### 过滤模式

| 参数 | 值 | 用途 |
|------|-----|------|
| TEXTURE_MIN_FILTER | NEAREST | 缩小过滤 |
| TEXTURE_MAG_FILTER | NEAREST | 放大过滤 |

### 环绕模式

| 参数 | 值 | 用途 |
|------|-----|------|
| TEXTURE_WRAP_S | CLAMP_TO_EDGE | S 方向环绕 |
| TEXTURE_WRAP_T | CLAMP_TO_EDGE | T 方向环绕 |
| TEXTURE_WRAP_R | CLAMP_TO_EDGE | R 方向环绕 |

## 支持的纹理类型

- **TEXTURE_2D**: 2D 纹理
- **TEXTURE_3D**: 3D 纹理 (体数据)

## 数据格式

| 格式 | 用途 |
|------|------|
| RGBA8 | 8位 RGBA |
| RGBA | 传统 RGBA |
| UNSIGNED_BYTE | 无符号字节 |

## 与其他文件的联系

- **依赖**: `context.ts` - GL 上下文
- **被引用**: `texture_access.ts` - 纹理访问
- **被引用**: `sliceview/` - 切片渲染

## 知识点

- **纹理过滤**: NEAREST vs LINEAR
- **纹理环绕**: CLAMP_TO_EDGE 避免边界问题
- **3D 纹理**: 体数据渲染
- **性能优化**: 纹理重用
