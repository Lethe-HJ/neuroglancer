# Python __init__.py 源码分析

## 文件概述

**路径**: `python/neuroglancer/__init__.py`  
**功能**: Neuroglancer Python 包的公共 API 导出  
**许可证**: Apache License 2.0

## 导出的主要模块和类

### 核心模块

| 模块 | 功能 |
|------|------|
| `server` | 内置服务器管理 |
| `segment_colors` | 分割颜色处理 |
| `skeleton` | 骨架数据结构 |

### 核心类

| 类名 | 功能 |
|------|------|
| `Viewer` | Python 端的查看器控制器 |
| `UnsynchronizedViewer` | 非同步查看器 |
| `LocalVolume` | 本地体数据卷 |
| `EquivalenceMap` | 等价映射（分割合并） |
| `ScreenshotSaver` | 截图保存 |

### 工具类

| 类名 | 功能 |
|------|------|
| `CoordinateSpace` | 坐标空间定义 |
| `ImageLayer` | 图像图层 |
| `SegmentationLayer` | 分割图层 |
| `SingleMeshLayer` | 单网格图层 |
| `PointAnnotation` | 点注释 |

### 工具函数

```python
# 服务器控制
set_server_bind_address()      # 设置服务器绑定地址
set_static_content_source()    # 设置静态内容源
set_dev_server_content_source() # 开发服务器源
stop()                         # 停止服务器
is_server_running()            # 检查服务器状态

# URL 状态
parse_url()                    # 解析 URL
to_url()                       # 转换为 URL
to_json_dump()                 # JSON 导出

# 凭证管理
set_boss_token()               # 设置 BOSS token
```

## 与其他文件的联系

- **依赖**: `viewer.py` - 查看器实现
- **依赖**: `local_volume.py` - 本地卷数据
- **依赖**: `viewer_state.py` - 状态管理
- **依赖**: `server.py` - 服务器
- **依赖**: `equivalence_map.py` - 等价映射

## 知识点

- **Python 包结构**: __init__.py 的导出模式
- **类型提示**: 完整的类型注解
- **FFI**: Python 与 JavaScript 的互操作
- **HTTP 服务器**: 内置 tornado 服务器
