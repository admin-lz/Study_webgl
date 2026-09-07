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
| — | [three-tsl-lighting.html](three-tsl-lighting.html) | Three.js WebGPU + TSL，对照用 |
| — | [index.html](index.html) | WebGPU 路径追踪，和上面的 WebGL 管线是另一条路 |

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
| 索引网格 | 再做一个 `ELEMENT_ARRAY_BUFFER`；改成 `drawElements` | `sphere.html` / `wind-field.html` |
| 深度 / 透明 | `enable(DEPTH_TEST)`；透明再 `enable(BLEND)` | `mvp-depth.html` |
| 多套物体 | 多套 program + VAO；每帧 `useProgram` → 绑 VAO → 设 uniform → draw | `wind-field.html`（地形 / 箭头 / 流线） |
| CPU 拾取 | 鼠标 → NDC → `(P·V)⁻¹` 成射线；JS 里 `hitSphere` / `hitAABB` | `pick-cpu.html` |
| GPU 拾取 | 离屏 FBO 平涂 ID 色；`readPixels` 1px 还原 id | `pick-gpu.html` |
| 实例化拾取 | 一次 `drawElementsInstanced`；`gl_InstanceID` 拆成 RGB | `pick-gpu-instance.html` |
| 碰撞检测 | 球–球中心距；AABB 最短轴；球到盒子最近点；MTV 推开 | `collision.html` |
| 阴影图 | 灯光正交 VP 写深度；片元变到灯光空间再比较 | `shadow-map.html` |
| PCSS | 搜 blocker → 估 penumbra → 变半径 PCF | `pcss.html` |

`wind-field.html` 只是把上面叠在一起：同一个 MVP，三套 shader，网格用 `drawElements`，流线每帧 `bufferSubData` 更新 VBO。

## 怎么打开

本地直接双击 html，或在本目录起一个静态服务：

```bash
npx --yes serve .
```

然后浏览器打开对应页面。`texture.html` / `wind-field.html` 若加载本地图片失败，用静态服务比 `file://` 稳。
