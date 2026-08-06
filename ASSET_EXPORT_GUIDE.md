# 资产导出规范 (Blender / Blender MCP / OpenSCAD / CadQuery → GLB)

目标:导出的 GLB 丢进 `public/assets/models/...` 就能直接替换对应 primitive,
**不影响手感、碰撞、checkpoint、拾取、相机、比赛逻辑**(视觉与逻辑已彻底解耦)。

---

## 1. 通用规范(所有模型)

| 项 | 要求 |
|---|---|
| 单位 | **1 unit = 1 meter** |
| 坐标系 | **Y up**(Three.js 约定) |
| 格式 | **`.glb`**(二进制,内嵌材质/纹理) |
| Transform | 导出前 **Apply / Freeze**:位置归零、旋转归零、缩放 = 1 |
| 材质 | 简单 PBR 或基础材质;避免高开销着色 |
| 面数 | 低多边形优先(目标设备是 MacBook Air 集显) |
| 禁止 | 不要把碰撞体 / checkpoint / 空物体逻辑塞进模型 |
| 原点 | 见每类要求,务必清楚不偏 |

### Blender 导出步骤
1. 选中模型 → `Object > Apply > All Transforms`(冻结位移/旋转/缩放)。
2. 确认模型朝向符合下方"车头/门朝向"要求(Blender 里 -Y 常是"前",导出到 glTF 会转成 -Z——**以最终 glTF 的 -Z 为准**,建议导出后在游戏里核对)。
3. `File > Export > glTF 2.0 (.glb)`:
   - Format: **glTF Binary (.glb)**
   - Include: Selected Objects
   - Transform: **+Y Up** 勾选
   - 勾选 Apply Modifiers
4. 命名并放到对应目录(见下)。

### OpenSCAD / CadQuery
- 这类工具导出 **STL**(无材质)。流程:生成 STL → 用 Blender 或 `gltf-pipeline` / `obj2gltf` 转 `.glb` → 按下方原点/尺寸调整 → 导出。
- STL 没有材质,转 GLB 后会用默认灰模;需要颜色就在 Blender 里补一个基础材质。
- 单位:OpenSCAD 默认 mm,记得缩放到米(×0.001)或直接按米建模。

---

## 2. 车辆 `karts/player.glb`(已接入)
| 项 | 值 |
|---|---|
| 车头朝向 | **-Z** |
| 原点 | **车辆中心的地面投影点**(车底中心,y=0) |
| 建议尺寸 | 长 ~3.2m × 宽 ~1.4m × 高 ~1.2m |
| 轮子 | 可含在同一 GLB;GLB 到位后代码自动隐藏占位轮 |

> 运动逻辑来自 `Vehicle` 的速度矢量 / heading / `TUNING`,**与车模型网格无关**。换模型不改手感。

## 3. 道具 `items/*.glb`(已接入)
所有道具:原点 = **模型中心**,朝向随意(道具自转展示),拾取半径由代码 `PICKUP_R`
控制,**与模型大小无关**。机制与 baguette 完全一致(先 primitive,GLB 到位替换,失败 warn)。

| 文件名 | 对应道具 | 建议尺寸(最长边) |
|---|---|---|
| `baguette.glb` | PAUL 法棍 | ~1.5m |
| `mont_blanc.glb` | Angelina 蒙布朗 | ~0.9m |
| `macaron_pink.glb` | 覆盆子马卡龙 | ~0.8m |
| `macaron_green.glb` | 开心果马卡龙 | ~0.8m |
| `macaron_red.glb` | 草莓马卡龙 | ~0.8m |

> 马卡龙三色当前用三个独立文件名;若想共用一个几何后续换材质,可导出三份不同配色,或先放其一其余走 fallback。

### 当前道具规则
GLB 只替换视觉,不改变拾取半径、重生、HUD、粒子或效果数值。当前玩法规则由 `src/items.js`
和 `src/effects.js` 控制:

| 道具 | 定位 | 效果 | 重生 |
|---|---|---|---|
| PAUL Jambon-Beurre / `baguette.glb` | 直道奖励 | 3 秒轻量加速: `speedMul 1.18`, `accelMul 1.05`,金色 trail | 8 秒 |
| Angelina Mont Blanc / `mont_blanc.glb` | 轻惩罚 / 干扰 | 2 秒 Sugar Crash: `speedMul 0.78`, `accelMul 0.70`, `wobble 0.8`,紫色屏幕,眩晕星星 | 10 秒 |
| 三色 Macaron / `macaron_*.glb` | 路线收集 | 单个只点亮 HUD 进度;集齐覆盆子/开心果/草莓后触发 5 秒 Gourmet Mode: `speedMul 1.32`, `accelMul 1.08`,彩虹 trail/screen/hearts | 集齐触发后整组 8 秒 |

## 4. checkpoint 门 `checkpoints/gate.glb`(已接入)
| 项 | 值 |
|---|---|
| 原点 | **门中心底部**(y=0) |
| 建议尺寸 | 宽 ~8–12m,高 ~3–5m |
| 朝向 | GLB 保持局部 **-Z 朝前**;实际朝向/位置由代码放到各 checkpoint |
| 判定 | 仍用 checkpoint 数据(半径),**与模型碰撞无关** |

---

## 5. 放置路径速查
```
public/assets/models/karts/player.glb        → 玩家车身
public/assets/models/items/baguette.glb      → PAUL 法棍
public/assets/models/items/mont_blanc.glb    → Angelina 蒙布朗
public/assets/models/items/macaron_pink.glb  → 覆盆子马卡龙
public/assets/models/items/macaron_green.glb → 开心果马卡龙
public/assets/models/items/macaron_red.glb   → 草莓马卡龙
public/assets/models/checkpoints/gate.glb    → checkpoint 门
public/assets/models/props/                  → (预留)装饰
public/assets/models/track/                  → (预留)赛道视觉
```

## 6. 验证
- 不放任何 GLB → 全部 primitive,和现在完全一致(fallback)。
- 放入某个 GLB 并刷新 → 该对象视觉替换为 GLB,手感/碰撞/计时不变。
- 路径写错/文件损坏 → 控制台 `console.warn`,自动回退 primitive,不崩溃。
