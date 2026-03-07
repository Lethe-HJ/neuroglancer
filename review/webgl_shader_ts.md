# webgl/shader.ts 源码分析

## 文件概述

**路径**: `src/webgl/shader.ts`  
**大小**: 18KB  
**功能**: WebGL 着色器编译和管理系统  
**许可证**: Apache License 2.0

## 核心概念

### 1. ShaderProgram 类

着色器程序管理器，继承自 RefCounted：

```typescript
class ShaderProgram extends RefCounted {
  program: WebGLProgram;
  vertexShader: WebGLShader;
  fragmentShader: WebGLShader;
  attributes = new Map<string, AttributeIndex>();
  uniforms = new Map<string, WebGLUniformLocation | null>();
  textureUnits: Map<any, number>;
  // ...
}
```

### 2. ShaderBuilder 类

着色器构建器，用于动态构建着色器代码：

```typescript
class ShaderBuilder {
  private uniformsCode = "";
  private attributesCode = "";
  private vertexCode = new ShaderCode();
  private fragmentCode = new ShaderCode();
  // ...
}
```

### 3. 着色器类型

```typescript
enum ShaderType {
  VERTEX = WebGL2RenderingContext.VERTEX_SHADER,
  FRAGMENT = WebGL2RenderingContext.FRAGMENT_SHADER,
}
```

## 主要功能

### 1. 着色器编译

```typescript
getShader(gl, source, shaderType)
// 编译 GLSL 着色器代码
```

### 2. 错误处理

- `ShaderCompilationError`: 着色器编译错误
- `ShaderLinkError`: 着色器链接错误
- `parseShaderErrors()`: 解析错误日志

### 3. 统一变量管理

```typescript
addUniform(typeName, name, extent?)
uniform(name): WebGLUniformLocation
```

### 4. 属性管理

```typescript
addAttribute(typeName, name, location?)
attribute(name): number
```

### 5. 纹理单元分配

```typescript
allocateTextureUnit(symbol, count)
addTextureSampler(samplerType, name, symbol, extent?)
```

## 与其他文件的联系

- **依赖**: `context.ts` - WebGL 上下文
- **依赖**: `disposable.ts` - 引用计数
- **依赖**: `data_type.ts` - 数据类型
- **被引用**: 几乎所有渲染模块

## 知识点

- **WebGL2**: WebGL 2.0 API
- **GLSL ES 3.00**: 着色器语言
- **引用计数**: 内存管理
- **实例化绘制**: drawArraysInstanced
- **Transform Feedback**: GPU 输出
