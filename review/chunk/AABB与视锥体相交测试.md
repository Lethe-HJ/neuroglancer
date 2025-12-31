## 疑问1：使用AABB与视锥体相交测试

### AABB（Axis-Aligned Bounding Box）与视锥体相交测试原理

**AABB定义：**

```typescript
interface AABB {
  xLower: number;
  yLower: number;
  zLower: number; // 最小点
  xUpper: number;
  yUpper: number;
  zUpper: number; // 最大点
}
```

**视锥体表示：**
视锥体由6个平面组成（左、右、上、下、近、远），每个平面用方程 `ax + by + cz + d = 0` 表示。

**相交测试算法：**

```typescript
function isAABBVisible(
  xLower: number,
  yLower: number,
  zLower: number,
  xUpper: number,
  yUpper: number,
  zUpper: number,
  clippingPlanes: Float32Array,
): boolean {
  // clippingPlanes包含6个平面，每个平面4个系数[a,b,c,d]
  for (let i = 0; i < 6; i++) {
    const planeOffset = i * 4;
    const a = clippingPlanes[planeOffset];
    const b = clippingPlanes[planeOffset + 1];
    const c = clippingPlanes[planeOffset + 2];
    const d = clippingPlanes[planeOffset + 3];

    // 计算AABB的8个顶点中在法向量方向上的投影最大的点（ positive vertex ）
    const px = a > 0 ? xUpper : xLower;
    const py = b > 0 ? yUpper : yLower;
    const pz = c > 0 ? zUpper : zLower;

    // 如果positive vertex都在平面外侧，则整个AABB在视锥体外
    if (a * px + b * py + c * pz + d < 0) {
      return false; // AABB完全在视锥体外
    }
  }
  return true; // AABB与视锥体相交或包含在内
}
```

视锥体的6个平面

![视锥体](https://docs.unity3d.com/cn/2021.3/uploads/Main/ViewFrustum.png)

每个平面用方程表示: ax + by + cz + d = 0
其中 (a,b,c) 是平面法向量，d是距离参数

### 关键算法 - Positive Vertex测试：

对于给定平面，AABB的8个顶点中**在法向量方向上的投影最大**的那个顶点。

```text
平面方程: ax + by + cz + d = 0
法向量: n = (a, b, c)
p1 p2点为在相应平面法向量方向上的投影最大的点（ positive vertex ）

             近平面
                |
                │
                │─────>n1      远平面
                │                │     ●────────●
                │                │     │        │
  ●────────●    │         n2<────│     │        │
  │        │    │                │     ●────────●
  │        │    │                      p2
  ●────────●    │
           p1   │
```

Positive Vertex的选择规则：

```typescript
const px = a > 0 ? xUpper : xLower; // 如果法向量x分量>0，选择x最大值，否则选x最小值
const py = b > 0 ? yUpper : yLower; // 如果法向量y分量>0，选择y最大值，否则选y最小值
const pz = c > 0 ? zUpper : zLower; // 如果法向量z分量>0，选择z最大值，否则选z最小值
```

距离计算和可见性判断

```typescript
// 计算positive vertex到平面的距离
if (a * px + b * py + c * pz + d < 0) {
  return false; // AABB完全在视锥体外
}
```

上面代码用到的结论 `n.p + d < 0` 则 该点p位于平面的反面(正面为法向量正方向的那一面)
n.p 表示 向量p在法向量n上的投影 该投影 + d < 0 则说明该点在平面反面
其中 n.p 即为 a * px + b * py + c * pz
