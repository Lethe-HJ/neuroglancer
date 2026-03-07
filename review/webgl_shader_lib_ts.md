# webgl/shader_lib.ts 源码分析

## 文件概述

**路径**: `src/webgl/shader_lib.ts`  
**大小**: 16KB  
**功能**: GLSL 着色器库函数 - 常用着色器函数集合  
**许可证**: Apache License 2.0

## 核心 GLSL 函数库

### 1. 颜色转换

```glsl
// HSV 转 RGB
glsl_hsvToRgb
vec3 hsvToRgb(vec3 c)

// 线性插值
glsl_mixLinear
float mixLinear(float x, float y, float a)
```

### 2. 64位整数支持

```glsl
glsl_uint64
struct uint64_t { highp uvec2 value; }

glsl_unpackUint64leFromUint32  // 解包
glsl_equalUint64               // 相等比较
glsl_compareLessThanUint64     // 小于比较
glsl_subtractUint64            // 减法
glsl_addUint64                 // 加法
```

### 3. 数据类型处理

| 导出常量 | 用途 |
|----------|------|
| DATA_TYPE_BYTES | 数据类型字节数 |
| DATA_TYPE_SIGNED | 有符号数据类型 |

### 4. 着色器构建接口

```typescript
interface ShaderBuilder {
  // 添加着色器代码
  addVertexCode(code: ShaderCodePart)
  addFragmentCode(code: ShaderCodePart)
  
  // 设置主函数
  setVertexMain(code: string)
  setFragmentMain(code: string)
}
```

## 主要功能

1. **颜色空间转换**: HSV ↔ RGB
2. **整数运算**: 64位整数运算
3. **数据解包**: 32位到64位转换
4. **插值函数**: 线性插值

## 与其他文件的联系

- **依赖**: `data_type.ts` - 数据类型定义
- **依赖**: `shader.ts` - 着色器构建器
- **被引用**: 各种渲染模块

## 知识点

- **GLSL**: OpenGL 着色语言
- **颜色空间**: HSV 颜色模型
- **整数溢出**: 64位整数模拟
- **数据类型**: 有符号/无符号
