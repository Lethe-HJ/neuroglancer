# python/neuroglancer 源码分析

## 概述

Python 端提供 Neuroglancer 的编程接口，允许在 Python 环境中控制查看器。

## 核心模块

### 1. viewer_state.py (59KB)

查看器状态管理 - Python 端的核心状态定义

**主要功能**:
- 坐标空间定义 (CoordinateSpace, DimensionScale, CoordinateArray)
- 图层状态管理
- 工具状态 (Tool, PlacePointTool, PlaceLineTool 等)
- 分割状态 (SegmentationLayer, SegmentIdMapEntry)

**核心类**:
```python
# 坐标空间
CoordinateSpace        # 坐标空间定义
DimensionScale         # 维度尺度
CoordinateArray        # 坐标数组

# 工具
Tool                   # 工具基类
PlacePointTool         # 点放置工具
PlaceLineTool          # 线放置工具
PlaceBoundingBoxTool   # 框放置工具

# 图层
Layer                  # 图层基类
ImageLayer             # 图像图层
SegmentationLayer      # 分割图层
```

### 2. viewer.py / viewer_base.py

查看器实现 - Python 端的查看器控制器

**主要功能**:
- 启动内置服务器
- 状态同步
- 截图功能

### 3. server.py (22KB)

内置 HTTP 服务器

**主要功能**:
- Tornado 服务器
- 静态文件服务
- WebSocket 状态同步
- 凭证管理

### 4. local_volume.py (14KB)

本地数据卷 - 从本地文件加载数据

**支持格式**:
- Numpy 数组
- N5 格式
- Zarr 格式

### 5. coordinate_space.py (10KB)

坐标空间 Python 端实现

### 6. equivalence_map.py (7KB)

等价映射 - 管理分割的合并关系

### 7. skeleton.py

神经骨架数据结构

## 与 JavaScript 的交互

Python 端通过以下方式与前端交互：
1. **HTTP/WebSocket**: 状态同步
2. **JSON**: 状态序列化
3. **内置服务器**: 托管静态文件

## 知识点

- **Python 类型提示**: 完整的类型注解
- **Tornado**: 异步 Web 框架
- **NumPy**: 数组操作
- **JSON-RPC**: 状态同步协议
