# sliceview 源码分析

## 文件概述

**路径**: `src/sliceview/`  
**功能**: 切片视图渲染 - 体数据的 2D 切片显示  
**许可证**: Apache License 2.0

## 核心模块

### 1. base.ts (33KB)

切片视图核心 - 基础架构

**主要类**:
```typescript
class SliceView {
  // 切片视图管理
  // 坐标变换
  // 渲染调度
}
```

### 2. frontend.ts (39KB)

前端渲染逻辑

**主要功能**:
- 纹理管理
- 渲染调度
- 视口同步

### 3. backend.ts (21KB)

后端数据处理

### 4. panel.ts (18KB)

切片面板组件

## 数据解码器

| 目录 | 格式 |
|------|------|
| `png/` | PNG 图像 |
| `jxl/` | JPEG XL |
| `compresso/` | Compresso 压缩 |
| `compressed_segmentation/` | 分割压缩 |
| `volume/` | 卷数据 |

## 主要功能

1. **切片渲染**: 任意角度的 2D 切片
2. **多通道**: 多通道数据支持
3. **插值**: 线性/三次插值
4. **纹理上传**: GPU 纹理管理

## 与其他文件的联系

- **依赖**: `webgl/` - WebGL 渲染
- **依赖**: `chunk_manager/` - 数据块
- **依赖**: `navigation_state.ts` - 导航
- **被引用**: `layer/image/` - 图像图层

## 知识点

- **图形学**: 切片投影
- **插值算法**: 线性、三次样条
- **纹理**: 2D/3D 纹理
- **数据压缩**: 各种图像压缩格式
