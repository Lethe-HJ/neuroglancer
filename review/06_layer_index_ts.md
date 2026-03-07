# layer/index.ts 源码分析

## 文件概述

**路径**: `src/layer/index.ts`  
**大小**: 74KB, 2500+ 行  
**功能**: 图层系统核心 - 管理所有类型的图层  
**许可证**: Apache License 2.0

## 核心概念

### 1. UserLayer (用户图层)

所有用户可见图层的基类，支持：
- 图层添加/删除
- 属性编辑
- 可见性控制
- 透明度调整

### 2. LayerManager (图层管理器)

```typescript
class LayerManager extends RefCounted {
  // 管理所有图层
  // 协调图层之间的交互
  // 处理图层变化事件
}
```

### 3. LayerDataSource (图层数据源)

将数据源与图层关联：
- 坐标空间定义
- 数据子源管理
- 数据加载协调

### 4. 图层类型

| 类型 | 路径 | 功能 |
|------|------|------|
| ImageLayer | `layer/image/` | 图像显示 |
| SegmentationLayer | `layer/segmentation/` | 分割区域显示 |
| AnnotationLayer | `layer/annotation/` | 注释/标注 |
| SingleMeshLayer | `layer/single_mesh/` | 网格模型 |
| SkeletonLayer | (内嵌) | 神经骨架 |

## 主要功能

1. **图层注册**: 新图层类型的注册机制
2. **数据绑定**: 将数据源绑定到可视化图层
3. **状态管理**: 跟踪图层选择、可见性、透明度等状态
4. **事件系统**: 图层变化时通知相关组件

## 与其他文件的联系

- **依赖**: `chunk_manager/` - 数据块管理
- **依赖**: `datasource/` - 数据源
- **依赖**: `renderlayer.ts` - 渲染图层
- **依赖**: `navigation_state.ts` - 导航状态
- **被引用**: `viewer.ts` - 查看器

## 知识点

- **设计模式**: 观察者模式、工厂模式
- **数据流**: 响应式数据绑定
- **类型系统**: TypeScript 泛型
- **性能优化**: 数据预取、懒加载
