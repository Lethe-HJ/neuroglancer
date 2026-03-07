# util 源码分析

## 文件概述

**路径**: `src/util/`  
**功能**: 工具函数库 - 数学、DOM、IO、类型转换等  
**许可证**: Apache License 2.0

## 工具模块分类

### 1. 数学库

| 文件 | 功能 |
|------|------|
| `geom.ts` | 几何 (vec3, mat4, quat) |
| `matrix.ts` | 矩阵运算 |
| `vector.ts` | 向量运算 |
| `lerp.ts` | 线性插值 |

### 2. 数据处理

| 文件 | 功能 |
|------|------|
| `array.ts` | 数组操作 |
| `json.ts` | JSON 解析/验证 |
| `disjoint_sets.ts` | 不交集数据结构 |

### 3. DOM 操作

| 文件 | 功能 |
|------|------|
| `dom.ts` | DOM 工具 |
| `disposable.ts` | 引用计数/资源管理 |
| `signal.ts` | 信号/事件系统 |

### 4. 类型转换

| 文件 | 功能 |
|------|------|
| `color.ts` | 颜色处理 |
| `si_units.ts` | SI 单位 |
| `data_type.ts` | 数据类型 |

### 5. 其他工具

| 文件 | 功能 |
|------|------|
| `abort.ts` | 取消操作 |
| `http_request.ts` | HTTP 请求 |
| `keyboard_bindings.ts` | 键盘绑定 |
| `mouse_bindings.ts` | 鼠标绑定 |

## 核心工具

### Signal (信号系统)

```typescript
class NullarySignal {
  add(handler: () => void): void;
  remove(handler: () => void): void;
  dispatch(): void;
}
```

### Disposable (资源管理)

```typescript
class RefCounted {
  registerDisposer(disposer: () => void): void;
  dispose(): void;
}
```

## 知识点

- **内存管理**: 引用计数模式
- **事件系统**: 信号/插槽模式
- **数学库**: gl-matrix 风格
- **类型安全**: 运行时类型验证
