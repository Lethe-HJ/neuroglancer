# navigation_state.ts 源码分析

## 文件概述

**路径**: `src/navigation_state.ts`  
**大小**: 70KB, 2300+ 行  
**功能**: 导航状态管理 - 处理查看器的位置、方向、缩放等状态  
**许可证**: Apache License 2.0

## 核心概念

### 1. NavigationState (导航状态)

管理查看器的核心导航状态，包括：
- **位置 (Position)**: 当前查看点的坐标
- **方向 (Orientation)**: 相机朝向
- **缩放 (Zoom)**: 视图缩放级别
- **横截面 (CrossSection)**: 切片视图的位置和方向

### 2. 导航链接类型

```typescript
enum NavigationLinkType {
  LINKED = 0,      // 同步链接
  RELATIVE = 1,    // 相对链接
  UNLINKED = 2,    // 独立
}
```

支持多个视图之间的导航同步。

### 3. PlaybackManager (播放管理器)

- 动画播放控制
- 沿路径自动导航
- 时间序列数据浏览

## 主要功能

1. **位置管理**: 跟踪和更新相机位置
2. **方向控制**: 3D 旋转和 2D 平面方向
3. **缩放控制**: 缩放级别管理
4. **视图同步**: 多视图联动控制
5. **动画播放**: 自动导航动画

## 与其他文件的联系

- **依赖**: `coordinate_transform.ts` - 坐标系统
- **依赖**: `trackable_value.ts` - 可追踪值
- **依赖**: `util/geom.js` - 几何运算 (mat4, vec3, quat)
- **被引用**: `viewer.ts` - 查看器核心
- **被引用**: `data_panel_layout.ts` - 面板布局
- **被引用**: `rendered_data_panel.ts` - 数据面板渲染

## 知识点

- **3D 图形学**: 相机模型、视图矩阵、投影矩阵
- **四元数**: 3D 旋转表示
- **观察者模式**: 状态变化通知
- **数学库**: gl-matrix 风格的几何运算
