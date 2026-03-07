# coordinate_transform.ts 源码分析

## 文件概述

**路径**: `src/coordinate_transform.ts`  
**大小**: 63KB, 1900+ 行  
**功能**: 坐标系统管理 - 处理多维坐标空间和坐标变换  
**许可证**: Apache License 2.0

## 核心概念

### 1. CoordinateSpace (坐标空间)

```typescript
interface CoordinateSpace {
  readonly valid: boolean;           // 是否完全初始化
  readonly rank: number;              // 维度数量
  readonly names: string[];           // 每个维度的名称
  readonly ids: DimensionId[];       // 维度ID
  readonly units: string[];           // 物理单位
  readonly scales: number[];          // 尺度因子
  readonly timestamps: number[];      // 最后修改时间
}
```

### 2. DimensionId (维度标识)

- 每个坐标空间维度都有一个唯一标识符
- 支持任意维度的数据（2D、3D、4D等）

### 3. 坐标变换

- 支持从体素坐标到物理坐标的转换
- 处理不同单位之间的转换（nm, μm, mm 等）
- 支持坐标系的缩放和平移

## 主要功能

1. **坐标空间创建**: `makeCoordinateSpace()`
2. **坐标变换**: 在不同坐标系之间转换
3. **单位处理**: 支持 SI 单位制 (nm, μm, mm, m 等)
4. **边界计算**: 计算坐标边界和中心点

## 与其他文件的联系

- **依赖**: `trackable_value.ts` - 可追踪的值系统
- **依赖**: `util/geom.js` - 几何数学库 (mat4, vec3, quat)
- **依赖**: `util/json.js` - JSON 解析和验证
- **依赖**: `util/si_units.js` - SI 单位处理
- **被引用**: `navigation_state.ts` - 导航状态管理
- **被引用**: `viewer.ts` - 查看器核心
- **被引用**: `render_coordinate_transform.ts` - 渲染坐标变换

## 知识点

- **线性代数**: 矩阵变换、四元数、向量运算
- **物理单位**: SI 单位制、单位和尺度转换
- **类型系统**: TypeScript 泛型和接口
- **响应式编程**: WatchableValue 模式
- **坐标系统**: 世界坐标、体素坐标、屏幕坐标的转换
