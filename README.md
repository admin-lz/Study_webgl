# WebGL2 从三角形写起

这目录里每个 `.html` 都是一份可直接双击打开的示例。  
浏览器打开即可，不需要打包。需要较新的 Chrome / Edge / Firefox。

核心就两句话：

- JS 只负责 **把数据交给 GPU**，然后 **下令 draw**
- 真正画像素的是 GPU 固定管线

建议按下面顺序看，不要跳。

## 学习顺序

| 顺序 | 文件 | 这一步加了什么 |
|------|------|----------------|
| 0 | [webgl-pipeline.html](webgl-pipeline.html) | 管线说明 + 最小三角形对照代码 |
| 1 | [triangle.html](triangle.html) | 最小可运行程序：3 个顶点 → 1 个三角形 |
| 2 | [VertexColor.html](VertexColor.html) | 顶点色：数组改成 `x,y,r,g,b`，VS 把颜色传给 FS |
| 3 | [uniform.html](uniform.html) | 每帧变化：`uniform` + `requestAnimationFrame` |
| 4 | [mvp-depth.html](mvp-depth.html) | MVP 矩阵 + 深度测试（可开关对照） |
| 5 | [texture.html](texture.html) | 贴图 + 3D 相机：`sampler2D`、`u_mvp` |
| 6 | [sphere.html](sphere.html) | 索引网格 + Blinn-Phong 光照 |
| 7 | [pick-cpu.html](pick-cpu.html) | CPU 拾取：鼠标反投影成射线，JS 里打球 / AABB |
| 8 | [pick-gpu.html](pick-gpu.html) | GPU 拾取：离屏 ID 色 + `readPixels` |
| 8b | [pick-gpu-instance.html](pick-gpu-instance.html) | 上万实例：`drawElementsInstanced` + `gl_InstanceID` 当 id |
| 9 | [collision.html](collision.html) | 碰撞：球–球 / AABB–AABB / 球–AABB，可分离可弹 |
| 10 | [shadow-map.html](shadow-map.html) | 阴影：灯光深度图 + 比较，bias / PCF |
| 11 | [pcss.html](pcss.html) | 软阴影：blocker 搜索 + 半影宽度可变 PCF |
| 12 | [wind-field.html](wind-field.html) | 多套 program / VAO，叠在一起 |
| 13 | [blend.html](blend.html) | 混合与透明：混合方程、排序、`depthMask`，加色为什么不用排序 |
| 14 | [mesh.html](mesh.html) | 索引 / 绕序 / 背面剔除：`drawElements` vs `drawArrays`、`gl_FrontFacing` |
| 15 | [postprocess.html](postprocess.html) | 离屏渲染 FBO + 后处理：全屏 pass、ping-pong、可分离高斯 |
| — | [intersect.html](intersect.html) | 三角形相交：平面判交 → 交线 → 2D 裁剪 → 1D 区间求交 |
| — | [interview-map.html](interview-map.html) | 面试对照表：每个主题讲到哪一层 + 会被追问什么 |
| — | [three-tsl-lighting.html](three-tsl-lighting.html) | Three.js WebGPU + TSL，对照用 |
| — | [index.html](index.html) | WebGPU 路径追踪，和上面的 WebGL 管线是另一条路 |

## 进阶专题

主链走完之后，下面这些按主题分组，每页只讲一个点，都能单独打开。

| 主题 | 文件 | 这一页在讲什么 |
|------|------|----------------|
| 纹理 | [texture-filter.html](texture-filter.html) | 过滤方式与 mipmap：`TEXTURE_MIN_FILTER` 六种取值、多级渐远、各向异性、不完整纹理为什么全黑 |
| 纹理 | [cubemap.html](cubemap.html) | 立方体贴图：`samplerCube`、方向查找、`reflect` 做环境映射 |
| 纹理 | [normal-map.html](normal-map.html) | 切线空间 TBN：法线贴图存在切线空间的原因、`dFdx/dFdy` 现算 TBN |
| 光照 | [pbr.html](pbr.html) | Cook-Torrance：D/G/F 三项、金属度–粗糙度工作流、能量守恒、ACES + gamma |
| 光照 | [light-types.html](light-types.html) | 平行 / 点 / 聚光 / 半球光、四种衰减模型、为什么 range 决定剔除效率 |
| 数学 | [clip-space.html](clip-space.html) | 裁剪空间与透视除法：为什么剔除在除法之前、深度为什么非线性 |
| 数学 | [quaternion.html](quaternion.html) | 四元数：为什么不用欧拉角、nlerp vs slerp、符号翻转与万向节锁 |
| 数学 | [frustum-cull.html](frustum-cull.html) | 视锥剔除：从 VP 矩阵抽六个平面（Gribb-Hartmann）、点 / 球 / AABB 三种测试 |
| 数学 | [skinning.html](skinning.html) | 骨骼动画：绑定姿势、逆绑定矩阵、线性混合蒙皮与糖果纸效应 |
| WebGL2 | [ubo.html](ubo.html) | UBO：std140 对齐规则、多 program 共享、手工打包与"对不齐就花屏" |
| WebGL2 | [mrt.html](mrt.html) | MRT + 延迟着色：一趟写 G-buffer、位置精度（RGBA8 / RGBA16F）的取舍 |
| WebGL2 | [texture-array.html](texture-array.html) | 纹理数组 vs 图集：独立 mipmap 链 vs 打包，图集的 mipmap 出血与 padding |
| WebGL2 | [transform-feedback.html](transform-feedback.html) | 变换反馈：把 VS 输出写回 buffer、ping-pong 迭代状态、CPU 回读的同步代价 |
| 效果 | [raymarch.html](raymarch.html) | SDF 光线步进：距离场、软阴影、AO 全部在片元里算 |
| 效果 | [instancing.html](instancing.html) | 实例化：`drawElementsInstanced` + `vertexAttribDivisor` / 数据纹理三种取数 |
| 效果 | [particles.html](particles.html) | GPU 粒子：无状态粒子、软粒子、加色 vs 透明的排序与 overdraw |
| 效果 | [bloom.html](bloom.html) | 后处理链：SSAO（半球采样 + 噪点 + 模糊）+ Bloom（阈值 + 可分离高斯 + 多级） |
| 效果 | [terrain.html](terrain.html) | 噪声地形：值/梯度噪声、fBm、域扭曲、解析法线、分块 LOD 与 T 型接点缝合 |

## GPU 每帧怎么走

```
顶点缓冲 VBO
  → 顶点着色器     每个顶点一次，必须写 gl_Position（裁剪空间 -1~1）
  → 图元组装       3 个顶点收成一个 TRIANGLES
  → 光栅化         三角形盖住哪些像素，就生成哪些片元
  → 片元着色器     每个片元一次，写出颜色
  → 深度 / 混合
  → 画布像素
```

三角形阶段还没有相机、没有纹理。顶点直接写在裁剪空间里，所以看不见矩阵。

## JS 必须按这个顺序写

后面加颜色、旋转、贴图、相机，都是在第 4～7 步上插东西，**顺序不变**。

1. 拿 `canvas.getContext("webgl2")`
2. 写两段 GLSL：顶点着色器 + 片元着色器
3. `createShader` → `compileShader` → `createProgram` → `attach` → `link`
4. CPU 上准备 `Float32Array` 顶点
5. `createBuffer` + `bufferData` 把数组上传到 GPU（VBO）
6. WebGL2 用 `VAO` 记住「哪个 attribute 怎么从 buffer 取数」
7. `clear` + `drawArrays(TRIANGLES, 0, 3)`

最小代码就在 [webgl-pipeline.html](webgl-pipeline.html) 第 3 节，和页面右侧正在跑的是同一套。

## 往后每加一项，写哪段代码

| 能力 | 你要写的东西 | 示例 |
|------|----------------|------|
| 顶点色 | 数组改成 `x,y,r,g,b`；VS `in / out` 传颜色；`vertexAttribPointer` 设 stride | `VertexColor.html` |
| 每帧变化 | VS 加 `uniform`；JS `getUniformLocation` + `uniform1f`；`requestAnimationFrame` | `uniform.html` |
| 贴图 | `createTexture` + `texImage2D`；FS `sampler2D` + `texture()` | `texture.html` |
| 3D / 相机 | 顶点改 `vec3`；JS 算 perspective × lookAt；`uniformMatrix4fv(u_mvp)` | `mvp-depth.html` |
| 索引网格 | 再做一个 `ELEMENT_ARRAY_BUFFER`；改成 `drawElements` | `mesh.html` / `sphere.html` / `wind-field.html` |
| 深度 / 透明 | `enable(DEPTH_TEST)`；透明再 `enable(BLEND)` + `depthMask(false)` + 排序 | `mvp-depth.html` / `blend.html` |
| 绕序 / 剔除 | `frontFace(CCW/CW)` + `enable(CULL_FACE)`；`gl_FrontFacing` 做双面材质 | `mesh.html` |
| 离屏渲染 | 建 FBO 挂颜色纹理 + 深度 renderbuffer；`checkFramebufferStatus` | `postprocess.html` |
| 后处理 | 全屏三角形用 `gl_VertexID` 生成；两张纹理 ping-pong 做可分离卷积 | `postprocess.html` |
| 多套物体 | 多套 program + VAO；每帧 `useProgram` → 绑 VAO → 设 uniform → draw | `wind-field.html`（地形 / 箭头 / 流线） |
| CPU 拾取 | 鼠标 → NDC → `(P·V)⁻¹` 成射线；JS 里 `hitSphere` / `hitAABB` | `pick-cpu.html` |
| GPU 拾取 | 离屏 FBO 平涂 ID 色；`readPixels` 1px 还原 id | `pick-gpu.html` |
| 实例化拾取 | 一次 `drawElementsInstanced`；`gl_InstanceID` 拆成 RGB | `pick-gpu-instance.html` |
| 碰撞检测 | 球–球中心距；AABB 最短轴；球到盒子最近点；MTV 推开 | `collision.html` |
| 阴影图 | 灯光正交 VP 写深度；片元变到灯光空间再比较 | `shadow-map.html` |
| PCSS | 搜 blocker → 估 penumbra → 变半径 PCF | `pcss.html` |
| 过滤 / mipmap | `MIN_FILTER` + `generateMipmap`；`textureLod` / `dFdx` 选层 | `texture-filter.html` |
| 立方体贴图 | `TEXTURE_CUBE_MAP` 六面 + `samplerCube`；按方向采样 | `cubemap.html` |
| 法线贴图 | 顶点传 TBN，片元 `TBN * (tex*2-1)`；或用 `dFdx/dFdy` 现算 | `normal-map.html` |
| PBR | 金属度/粗糙度 → F0；D·G·F 三项；线性空间算完再 gamma | `pbr.html` |
| 多光源 | 每种光一个衰减函数；点/聚光按距离 cull | `light-types.html` |
| 四元数 | 存轴角，转矩阵给 GPU；插值用 slerp（先修正符号） | `quaternion.html` |
| 视锥剔除 | 从 `P·V` 抽六平面；球心距 < -r 就丢；不要每帧重建缓冲 | `frustum-cull.html` |
| 蒙皮 | 每顶点 4 个骨骼权重；`Σ wᵢ · (boneᵢ · invBind) · p` | `skinning.html` |
| UBO | `layout(std140)` 的块；`bindBufferBase` 绑槽位；对齐按 16 字节算 | `ubo.html` |
| 延迟着色 | 一趟 MRT 写 albedo/normal/材质；光照单独一趟全屏 | `mrt.html` |
| 纹理数组 | `texStorage3D` + `texSubImage3D` 逐层上传；FS 里 `texture(sampler2DArray, vec3(uv, layer))` | `texture-array.html` |
| 变换反馈 | `transformFeedbackVaryings` + ping-pong buffer；回读要 fencing | `transform-feedback.html` |
| 光线步进 | 片元里 `map()` 求距离 → 沿射线步进；法线用梯度 | `raymarch.html` |
| 实例化 | 每实例一份属性 + `vertexAttribDivisor`，或数据纹理 + `gl_InstanceID` | `instancing.html` |
| GPU 粒子 | 位置 = f(seed, time)，不用 buffer 更新；billboard 在裁剪空间做 | `particles.html` |
| SSAO | 深度反投影 + 半球采样；随机转方向把噪点打成白噪声再模糊 | `bloom.html` |
| Bloom | 阈值提取 → 半分辨率可分离高斯 → 多级叠加 | `bloom.html` |
| 程序化地形 | 高度由噪声函数给出；法线对函数求导；LOD 用索引子集 + 边缝合 | `terrain.html` |

`wind-field.html` 只是把上面叠在一起：同一个 MVP，三套 shader，网格用 `drawElements`，流线每帧 `bufferSubData` 更新 VBO。

## 怎么打开

本地直接双击 html，或在本目录起一个静态服务：

```bash
npx --yes serve .
```

然后浏览器打开对应页面。`texture.html` / `wind-field.html` 若加载本地图片失败，用静态服务比 `file://` 稳。
