# viewer.ts 源码分析

## 文件概述

**路径**: `src/viewer.ts`  
**大小**: 39KB, 1000+ 行  
**功能**: Neuroglancer 主查看器 - 核心应用程序类  
**许可证**: Apache License 2.0

## 核心类: Viewer

Viewer 是 Neuroglancer 的主应用程序类，负责协调所有功能模块。

### 主要组件

1. **LayerManager** - 图层管理器
   - 添加、删除、排序图层
   - 管理图层可见性

2. **NavigationState** - 导航状态
   - 相机位置、方向、缩放

3. **DisplayContext** - 显示上下文
   - WebGL 渲染上下文管理
   - 动画循环

4. **SidePanelManager** - 侧边栏管理器
   - 图层面板
   - 设置面板
   - 统计面板

5. **InputEventBindings** - 输入事件绑定
   - 鼠标、键盘、触摸事件处理

### UI 配置选项

```typescript
const VIEWER_UI_CONFIG_OPTIONS = [
  "showTopBar",
  "showUIControls", 
  "showPanelBorders",
  "pickRadius",
  // ... 更多选项
];
```

## 主要功能

1. **图层管理**
   - 支持多种图层类型：图像、分害割、网格、骨架、注释等
   - 图层属性编辑

2. **数据加载**
   - 从多种数据源加载数据
   - 数据预取和缓存

3. **交互控制**
   - 鼠标拖拽旋转/平移
   - 键盘快捷键
   - 触摸手势

4. **状态保存/恢复**
   - URL 状态序列化
   - 状态分享

5. **截图和导出**
   - 截图功能
   - 数据导出

## 与其他文件的联系

- **依赖**: `coordinate_transform.ts` - 坐标系统
- **依赖**: `navigation_state.ts` - 导航状态
- **依赖**: `layer/` - 图层系统
- **依赖**: `display_context.ts` - 显示上下文
- **依赖**: `ui/` - 用户界面组件
- **依赖**: `datasource/` - 数据源

## 知识点

- **WebGL**: WebGL 渲染管线
- **事件处理**: 复杂的事件绑定系统
- **状态管理**: 可序列化的应用状态
- **UI 框架**: 自定义 UI 组件系统
- **数据流**: 响应式数据流架构
