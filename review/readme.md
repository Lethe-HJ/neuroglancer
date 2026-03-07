# Neuroglancer 源码阅读文档

## 项目概述

Neuroglancer 是 Google 开发的一个基于 WebGL 的神经数据可视化工具。它能够显示体数据的任意（非轴对齐）横截面视图，支持神经影像数据的交互式查看。

## 技术栈

- **前端**: TypeScript + WebGL
- **后端**: Python
- **构建工具**: TypeScript, Webpack/Rspack
- **渲染**: WebGL

## 目录结构

```
neuroglancer/
├── src/                    # TypeScript 源码
│   ├── annotation/         # 注释功能
│   ├── chunk_manager/      # 数据块管理
│   ├── datasource/        # 数据源
│   ├── layer/             # 图层系统
│   ├── mesh/              # 网格渲染
│   ├── segmentation/      # 分割显示
│   ├── sliceview/        # 切片视图
│   ├── webgl/            # WebGL 渲染
│   ├── ui/               # 用户界面
│   ├── util/             # 工具函数
│   └── ...
├── python/                # Python 绑定
│   └── neuroglancer/     # Python 包
├── docs/                 # 文档
├── examples/             # 示例
└── tests/                # 测试
```

## 核心模块说明

### 1. 坐标系统 (coordinate_transform.ts)
- 管理多维坐标空间
- 支持任意维度的坐标变换
- 处理物理单位和尺度

### 2. 查看器 (viewer.ts)
- 主应用程序入口
- 管理图层、视口、交互
- 处理用户输入事件

### 3. 图层系统 (layer/)
- 支持多种数据类型：图像、分割、网格、骨架等
- 图层管理和平铺显示

### 4. 数据块管理 (chunk_manager/)
- 按需加载体数据
- 多级细节（LOD）支持
- 缓存管理

### 5. WebGL 渲染 (webgl/)
- 低级 WebGL 渲染管线
- 着色器管理
- 纹理处理

---

*本文档将持续更新，详细分析每个源码文件*

## 已生成文档列表

| 序号 | 文件 | 描述 |
|------|------|------|
| 01 | main_ts.md | 主入口点 |
| 02 | coordinate_transform_ts.md | 坐标系统 |
| 03 | navigation_state_ts.md | 导航状态 |
| 04 | viewer_ts.md | 主查看器 |
| 05 | python_init_py.md | Python API |
| 06 | layer_index_ts.md | 图层系统 |
| 07 | chunk_manager.md | 数据块管理 |
| 08 | datasource.md | 数据源 |
| 09 | webgl.md | WebGL 渲染 |
| 10 | ui.md | 用户界面 |
| 11 | util.md | 工具函数 |
| 12 | python_modules.md | Python 模块 |
| 13 | sliceview.md | 切片视图 |
| 14 | mesh.md | 网格渲染 |
| 15 | segmentation_display_state.md | 分割显示 |

## 尚未覆盖的模块

- annotation/ - 注释系统
- async_computation/ - 异步计算
- credentials_provider/ - 凭证管理
- gpu_hash/ - GPU 哈希
- kvstore/ - 键值存储
- perspective_view/ - 透视视图
- skeleton/ - 骨架
- volume_rendering/ - 体渲染
- widget/ - UI 控件
