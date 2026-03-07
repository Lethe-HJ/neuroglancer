# webgl 源码分析

## 文件概述

**路径**: `src/webgl/`  
**功能**: WebGL 渲染底层 - 着色器、纹理、缓冲区管理  
**许可证**: Apache License 2.0

## 核心模块

### 1. 着色器系统 (shader)

| 文件 | 大小 | 功能 |
|------|------|------|
| shader.ts | 18KB | 着色器编译和管理 |
| shader_lib.ts | 16KB | 着色器库函数 |
| dynamic_shader.ts | 8KB | 动态着色器生成 |
| shader_ui_controls.ts | 47KB | UI 控件的着色器支持 |

### 2. 纹理系统 (texture)

| 文件 | 大小 | 功能 |
|------|------|------|
| texture.ts | 5KB | 纹理基础 |
| texture_access.ts | 17KB | 纹理数据访问 |
| colormaps.ts | 2KB | 颜色映射表 |

### 3. 图元绘制

| 文件 | 功能 |
|------|------|
| lines.ts | 线条绘制 |
| circles.ts | 圆形绘制 |
| ellipse.ts | 椭圆绘制 |
| spheres.ts | 球体绘制 |
| quad.ts | 四边形 |

### 4. 缓冲区管理

| 文件 | 功能 |
|------|------|
| buffer.ts | GPU 缓冲区 |
| rectangle_grid_buffer.ts | 矩形网格缓冲区 |

## 核心概念

### ShaderProgram (着色器程序)

```typescript
class ShaderProgram {
  // 顶点着色器
  // 片元着色器
  // 统一变量管理
  // 属性绑定
}
```

### Texture (纹理)

- 1D/2D/3D 纹理支持
- 浮点纹理
- 纹理数据上传

## 主要功能

1. **着色器编译**: 动态编译 GLSL 着色器
2. **纹理管理**: 加载和管理 GPU 纹理
3. **缓冲区管理**: VBO 管理
4. **渲染状态**: WebGL 状态机封装

## 与其他文件的联系

- **依赖**: `display_context.ts` - 显示上下文
- **依赖**: `renderlayer.ts` - 渲染图层
- **被引用**: `sliceview/` - 切片渲染
- **被引用**: `mesh/` - 网格渲染

## 知识点

- **WebGL**: WebGL 1.0/2.0 API
- **GLSL**: 着色器语言
- **GPU 架构**: 纹理、缓冲区、着色器管线
- **性能优化**: 批处理、状态切换优化
