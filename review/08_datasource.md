# datasource 源码分析

## 文件概述

**路径**: `src/datasource/`  
**功能**: 数据源系统 - 支持多种神经影像数据格式  
**许可证**: Apache License 2.0

## 支持的数据源类型

Neuroglancer 支持多种数据存储格式和协议：

| 数据源 | 路径 | 描述 |
|--------|------|------|
| precomputed | `datasource/precomputed/` | Google 预计算格式 |
| boss | `datasource/boss/` | Boss 数据库 |
| dvid | `datasource/dvid/` | DVID 分布式存储 |
| n5 | `datasource/n5/` | N5 格式 |
| zarr | `datasource/zarr/` | Zarr 格式 |
| graphene | `datasource/graphene/` | Graphene 格式 |
| brainmaps | `datasource/brainmaps/` | Google Brain Maps |
| nifti | `datasource/nifti/` | NIfTI 医学影像格式 |
| deepzoom | `datasource/deepzoom/` | Deep Zoom 切片 |
| render | `datasource/render/` | Render 服务 |
| vtk | `datasource/vtk/` | VTK 格式 |
| obj | `datasource/obj/` | OBJ 3D 模型 |
| python | `datasource/python/` | Python 数据源 |
| local | `datasource/local.ts` | 本地文件 |

## 核心接口

### DataSourceRegistry

```typescript
interface DataSourceRegistry {
  // 注册新数据源
  register: (protocol: string, source: DataSource) => void;
  // 获取数据源
  get: (spec: DataSourceSpecification) => DataSource;
}
```

### DataSourceSpecification

```typescript
interface DataSourceSpecification {
  protocol: string;    // 协议 (如 "precomputed", "boss")
  url: string;       // 数据 URL
  options: object;   // 额外选项
}
```

## 主要功能

1. **协议处理**: 统一的协议接口
2. **数据加载**: 从各种来源加载体数据
3. **元数据解析**: 解析数据格式和坐标空间
4. **凭证管理**: 支持多种认证方式

## 与其他文件的联系

- **依赖**: `chunk_manager/` - 数据块加载
- **依赖**: `kvstore/` - 键值存储
- **依赖**: `credentials_provider/` - 凭证管理
- **被引用**: `layer/` - 图层数据源

## 知识点

- **数据格式**: N5, Zarr, DVID 等格式规范
- **HTTP/REST**: API 调用和数据传输
- **认证**: OAuth、API Key 等
- **坐标系统**: 多维数据坐标映射
