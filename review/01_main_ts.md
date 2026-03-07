# main.ts 源码分析

## 文件概述

**路径**: `src/main.ts`  
**功能**: Neuroglancer 默认查看器的主入口点  
**许可证**: Apache License 2.0

## 代码内容

```typescript
import { setupDefaultViewer } from "#src/ui/default_viewer_setup.js";
import "#src/util/google_tag_manager.js";

setupDefaultViewer();
```

## 功能说明

1. **入口文件**: 这是 Neuroglancer 应用程序的主入口点
2. **默认查看器设置**: 调用 `setupDefaultViewer()` 函数初始化默认的查看器实例
3. **Google Tag Manager**: 导入 Google Tag Manager 用于分析追踪

## 与其他文件的联系

- **依赖**: `#src/ui/default_viewer_setup.js` - 负责创建和配置默认查看器
- **依赖**: `#src/util/google_tag_manager.js` - Google 分析追踪工具

## 知识点

- TypeScript 模块系统
- JavaScript 动态导入
- Web 应用初始化流程
