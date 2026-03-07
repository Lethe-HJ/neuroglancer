# webgl 文件夹总结

## 概述

`src/webgl/` 目录包含 Neuroglancer 的底层 WebGL 渲染系统，提供图形硬件访问接口。

## 核心模块

| 模块 | 文件 | 功能 |
|------|------|------|
| **着色器系统** | shader.ts | 编译、管理着色器程序 |
| | shader_lib.ts | GLSL 库函数 |
| | dynamic_shader.ts | 动态着色器生成 |
| | shader_ui_controls.ts | UI 控件着色器 |
| **上下文** | context.ts | WebGL 初始化 |
| **纹理** | texture.ts | 纹理管理 |
| | texture_access.ts | 纹理数据访问 |
| | colormaps.ts | 颜色映射表 |
| **缓冲区** | buffer.ts | GPU 缓冲区 |
| | rectangle_grid_buffer.ts | 网格缓冲区 |
| **图元** | lines.ts | 线条 |
| | circles.ts | 圆形 |
| | ellipse.ts | 椭圆 |
| | spheres.ts | 球体 |
| | quad.ts | 四边形 |
| **特效** | offscreen.ts | 离屏渲染 |
| | trivial_shaders.ts | 基础着色器 |

## 架构分层

```
┌─────────────────────────────────┐
│     高层渲染 (sliceview/mesh/)   │
├─────────────────────────────────┤
│     着色器系统 (shader/)         │
├─────────────────────────────────┤
│     纹理/缓冲区 (texture/buffer/) │
├─────────────────────────────────┤
│     WebGL 上下文 (context/)      │
└─────────────────────────────────┘
```

## WebGL 版本

- **WebGL 2.0** (WebGL2RenderingContext)
- GLSL ES 3.00

## 关键特性

1. **着色器模块化**: 可组合的着色器代码
2. **引用计数**: 自动资源管理
3. **纹理单元管理**: 智能纹理分配
4. **扩展支持**: 浮点纹理、浮点混合
5. **调试工具**: 着色器错误解析

## 与其他模块的关系

- **依赖**: 
  - `util/disposable.ts` - 资源管理
  - `util/geom.ts` - 几何计算

- **被依赖**:
  - `sliceview/` - 切片渲染
  - `mesh/` - 网格渲染
  - `volume_rendering/` - 体渲染

## 知识点

- **GPU 渲染管线**: 顶点 → 光栅化 → 片元
- **着色器**: Vertex/Fragment Shader
- **纹理**: 2D/3D 纹理
- **缓冲区**: VBO/EBO
- **GLSL**: OpenGL 着色语言
