# webgl/offscreen.ts 源码分析

## 文件概述

**路径**: `src/webgl/offscreen.ts`  
**大小**: 11KB  
**功能**: 离屏渲染 - Framebuffer 和渲染目标管理  
**许可证**: Apache License 2.0

## 核心概念

### 1. Framebuffer

离屏渲染使用的帧缓冲区对象：

```typescript
class OffscreenRenderTarget {
  framebuffer: WebGLFramebuffer;
  colorTexture: WebGLTexture;
  depthRenderbuffer: WebGLRenderbuffer;
}
```

### 2. 渲染目标类型

| 类型 | 用途 |
|------|------|
| 颜色纹理 | 输出颜色 |
| 深度缓冲 | 深度测试 |
| 模板缓冲 | 模板测试 |

## 主要功能

1. **创建 Framebuffer**: 创建离屏渲染目标
2. **绑定/解绑**: 切换渲染目标
3. **读取像素**: 从 Framebuffer 读取
4. **清理资源**: 释放 GPU 资源

## 应用场景

- **截图**: 渲染到纹理后读取像素
- **后期处理**: 多通道渲染
- **阴影图**: 阴影计算
- ** Picking**: 颜色编码选择

## 知识点

- **FBO**: Framebuffer Object
- **MRT**: Multiple Render Targets
- **渲染到纹理**: Render to Texture
- **像素读取**: glReadPixels
