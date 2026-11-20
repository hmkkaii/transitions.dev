# Transitions.dev 高保真视觉对比与异常专项报告（Step 3）

> **上游仓库**：[https://github.com/Jakubantalik/transitions.dev](https://github.com/Jakubantalik/transitions.dev)  
> **基准 Commit SHA**：`add5b467b13be16664d10fd888330ce2f3288b07`  
> **对比环境**：本地 `http://127.0.0.1:8765/` vs 官网 `https://transitions.dev/`  
> **浏览器与视口条件**：同一 Chromium 浏览器实例，桌面端 `1440×900`（`scale=1`，Light 主题 + 首页 Dark 主题复测）、移动端 `390×844`（`scale=1`，Light 主题）  
> **验收原则**：所有结论均来自双端真实访问与测量，未伪造后端响应、未绕过付费限制、未修改任何上游源码。

---

## 1. 实测页面覆盖范围与总览（共 11 个核心路由 × 2 端 × 2 视口）

| # | 页面路由 | 桌面端 1440×900（本地 vs 官网） | 移动端 390×844（本地 vs 官网） | 视觉结论 |
| ---: | --- | --- | --- | --- |
| 1 | `/`（首页，含 Light & Dark） | 本地 `2843.23px` (44 `<article>`) vs 官网 `3637.23px` (50 `<article>`)，差 **`-794px`**；本地额外渲染 `beam-dev.js` 调试浮窗 | 本地 `3569.03px` vs 官网 `4347.03px`，差 **`-778px`** | **部分一致**（静态 Hero / 字体 / 轮播卡片 1:1；缺社区 API 轮播且本地挂载 `beam-dev.js`） |
| 2 | `/library.html` | 双端完全一致：`5010.83px`，顶栏 `68px`，44 张卡片，4 列网格 (`338×344px`, `x=20/374/728/1082`, `y=250`) | 双端完全一致：`17231.03px`，顶栏 `68px`，44 张卡片，1 列堆叠 (`350×344px`, `x=20`) | **1:1 完全一致**（0.00px 偏差） |
| 3 | `/detail.html?t=card-resize`（Free 详情） | 双端完全一致：`1424.63px`，顶栏 `68px`，48 `<article>`，正文 `1467` 字符 | 双端完全一致：`1288.03px`，顶栏 `68px`，正文 `1631` 字符 | **1:1 完全一致** |
| 4 | `/detail.html?t=confetti-burst`（Pro 预览） | 双端完全一致：`1296.63px`，顶栏 `68px`，舞台 `936×460px`，Pro 锁态一致 | 双端完全一致：`1240.03px`，顶栏 `68px`，Pro 锁态一致 | **1:1 完全一致**（公开预览与付费墙一致；源码 `Unverified`） |
| 5 | `/prototypes.html` | 双端完全一致：`4816px`，控制顶栏 `53px` | 双端完全一致：`13671px`，控制顶栏 `53px` | **1:1 完全一致** |
| 6 | `/detail.html?doc=introduction`（文档页） | 双端完全一致：`1062.63px`，顶栏 `68px`，正文 `2182` 字符 | 双端完全一致：`1390.83px`，顶栏 `68px`，正文 `2730` 字符 | **1:1 完全一致** |
| 7 | `/skill.html` | 双端完全一致：`3348.03px`，顶栏 `68px`（仅演示卡片内随机姓名/邮箱不同） | 双端完全一致：`3934.83px`，顶栏 `68px` | **1:1 完全一致** |
| 8 | `/pro.html` | 双端完全一致：`3365.27px`，顶栏 `68px`，正文 `4599` 字符（当前地区 `/geo` 返回 `TW/ppp:null`，双端均无 PPP 横幅） | 双端完全一致：`5458.22px`，顶栏 `68px` | **静态展示 1:1 一致**（结账/PPP 后端 `Unverified`） |
| 9 | `/example.html` | 双端完全一致：`900px`（独立演示无主导航，仅随机联系人文本不同） | 双端完全一致：`1076px` | **1:1 完全一致** |
| 10 | `/number.html` | 双端完全一致：`1096.5px`，顶栏 `68px`，正文 `1242` 字符 | 双端完全一致：`3081.21px`，顶栏 `68px` | **1:1 完全一致** |
| 11 | `/transitions/card-resize/`（静态 SEO 页） | 双端完全一致：`2620.65px`，标题块 `17.5px`，正文 `2516` 字符 | 双端完全一致：`2704.65px`，正文 `2516` 字符 | **1:1 完全一致** |

---

## 2. 字体、配色、布局、间距与响应式细节校验

| 视觉维度 | 本地 `127.0.0.1:8765` | 官网 `transitions.dev` | 校验结论 |
| --- | --- | --- | --- |
| **字体资源** | `fonts.css`、`inter-latin.woff2`、`Saans-Medium.woff2`、`roboto-mono-latin.woff2` 全部 HTTP `200` | 完全一致，全部 HTTP `200` | **1:1 一致** |
| **顶栏导航高度与结构** | 标准页 `68px`；`1440×900` 显示 `Products / Community New / Docs / Pricing / Search ⌘K / Get Pro / GitHub / More ⋮`；`390×844` 折叠为汉堡菜单 `Open menu` | 完全一致 | **1:1 一致**（`More ⋮` 内 `Join for free` 见差异项 Diff-03） |
| **卡片尺寸与栅格** | Library 桌面 4 列 `338×344px`（列左距 `20 / 374 / 728 / 1082px`，水平间距 `16px`，首行 `y=250px`）；移动端 1 列 `350×344px`（`x=20px`，垂直步进 `360px`，间距 `16px`）；首页轮播卡片桌面 `338×344px`（`y=899px`）、移动端 `287×344px`（`y=921px`） | 所有坐标与尺寸精确到 0.01px 完全一致 | **1:1 一致** |
| **明暗主题（Dark Mode）** | `More → Appearance → Dark` 切换 `#111111`/`#141414` 深色表面、Logo 反色、边框与卡片暗色 Token 完整生效 | 完全一致 | **1:1 一致** |
| **上游版本同步度** | 本地克隆基线 `add5b46`（44 张 Library 卡片） | 线上同样为 44 张 Library 卡片，无静态文案或样式漂移 | **静态源码完全同步** |

---

## 3. 实测差异与专项异常调查（含分类、证据与建议）

### Diff-01：首页缺少 "Community components people build with the Builder." 轮播区块
- **严重程度**：中（影响首页完整页面高度与视觉区块完整度，但不影响核心动效与 Library）
- **归类**：**私有后端边界（`Unverified` — 非本地静态源码缺陷）**
- **实测证据**：
  - 官网 `https://transitions.dev/` 请求 `https://api.transitions.dev/community/feed?sort=popular&offset=0` 返回 HTTP `200`，渲染社区轮播（总 `<article>` 数 `50`，桌面高度 `3637.23px`、移动高度 `4347.03px`）。
  - 本地 `http://127.0.0.1:8765/` 因 `assets/community/community.js:11-13` 将本地域名映射至 `http://localhost:8787`，请求返回 `status: 0` (`ERR_CONNECTION_REFUSED`)，渲染 0 张社区卡片（总 `<article>` 数 `44`，桌面高度 `2843.23px` [`-794px`]、移动高度 `3569.03px` [`-778px`]）。
- **复现步骤**：桌面 `1440×900` 分别打开 `http://127.0.0.1:8765/` 与 `https://transitions.dev/` 并滚动至 Library 轮播下方。
- **建议**：严格遵守工作原则，**不自行伪造社区接口或替换私有后端**；在文档与 README 中如实标注为私有后端依赖限制（`Unverified`）。

---

### Diff-02：本地 `localhost` / `127.0.0.1` 自动注入 `beam-dev.js` 调试面板，遮挡右上角顶栏
- **严重程度**：中（直接影响“为后续原创演示视频提供可录制环境”的干净画面，官网访客完全看不到该控件）
- **归类**：**本地环境触发的上游开发态行为（真实本地视觉差异，Step 4 可选定向修复候选）**
- **实测证据**：
  - 源码位置：`assets/community/feed.js#L68-L72`：
    ```js
    // The beam's tuning panel: localhost, or ?dev in the URL. Never for visitors.
    if (/^(localhost|127\.0\.0\.1)$/.test(location.hostname) || /[?&]dev\b/.test(location.search)) {
      var dev = document.createElement("script");
      dev.src = "assets/community/beam-dev.js" + ASSET_V;
      document.body.appendChild(dev);
    }
    ```
  - 访问 `http://127.0.0.1:8765/` 时页面自动加载 `beam-dev.js?v=160`，弹出 `Border beam tuning (dev only)` 调试抽屉 / 右下角 `Beam dev` 浮标（展开时遮挡右上角导航图标）；访问 `https://transitions.dev/` 时不会加载该脚本，画面干净。
- **复现步骤**：打开 `http://127.0.0.1:8765/` 或 `http://127.0.0.1:8765/builder.html`，页面右上/右下角立即出现调试面板/浮标。
- **建议**：如需用于干净录屏并使本地首页视觉更贴近官网访客视角，可在 Step 4 仅将 `feed.js:68` 调整为仅通过显式 URL 参数 `?dev` 开启（保留全部调参能力，不破坏任何动效）；若坚持零改动上游文件，则录屏前手动点击收起/关闭面板或在文档中说明。

---

### Diff-03：私有 API（`localhost:8787` vs `api.transitions.dev`）导致的细微菜单与统计差异
- **严重程度**：低
- **归类**：**私有后端边界（`Unverified` — 预期差异）**
- **实测证据**（官网实测返回 vs 本地 `status: 0 / ERR_CONNECTION_REFUSED`）：
  1. `GET /me`：官网 `200`（`{"authenticated":false}`），解析完成后 `More ⋮` 菜单显示 `Join for free` 且主题默认 `Appearance · System`；本地失败导致 `pro-client.js` 中 `state.resolved` 保持 `false`，`More ⋮` 菜单不显示 `Join for free`，默认显示 `Appearance · Light`。
  2. `GET /auth/providers`：官网 `200`（`{"github":false,"chatgpt":false,"chatgpt_plan":false}`），控制登录弹窗 OAuth 按钮；本地失败。
  3. `GET /stats/people`：官网 `200`（`{"people":4239}`），驱动注册弹窗/社区会员数 `Spinning counter`；本地失败。
  4. `GET /geo`：官网 `200`（`{"country":"TW","ppp":null}`），驱动 `/pro.html` 地区 PPP 折扣（当前测试出口无 PPP 折扣，故双端 `/pro.html` 视觉一致）；本地失败。
- **复现步骤**：在两站打开右上角 `More ⋮` 菜单，或在 `/pro.html` 点击 `Start free` 观察网络请求。
- **建议**：不伪造 API，保留官方 `pro-client.js` 原貌，相关账户/登录/统计/PPP 功能标注 `Unverified`。

---

### Diff-04（专项调查）：`number-subtle-change` 明暗缩略图 HTTP 404
- **严重程度**：低–中（Cmd+K 搜索弹窗内出现裂图图标）
- **归类**：**确认的上游原始缺陷（本地与官网 100% 相同，非本地环境引入）**
- **实测证据**：
  - 本地磁盘 `assets/community/thumbs/` 共 86 张图（44 个动效 × 明暗双图应为 88 张），唯独缺少 `number-subtle-change.jpg` 与 `number-subtle-change-dark.jpg`；`assets/cmdk-search.js#L46` 引用了 `"number-subtle-change"`。
  - **官网 `https://transitions.dev/` 实测复现**：在官网 `/` 与 `/library.html` 分别以 Light 与 Dark 模式打开 `Search (⌘K)`：
    - `GET https://transitions.dev/assets/community/thumbs/number-subtle-change.jpg?v=2` → **HTTP `404`**
    - `GET https://transitions.dev/assets/community/thumbs/number-subtle-change-dark.jpg?v=2` → **HTTP `404`**（官网弹窗同样显示破损图片图标）。
- **复现步骤**：打开官网或本地 `/library.html` → 按 `⌘K` 打开搜索框 → 查看网络面板或滚动至 `Number subtle change` 条目（切换深浅模式各测一次）。
- **建议**：由于这是**上游仓库本身在官网就存在的原始缺陷**，且工作原则要求“优先保留官方源代码、不要自行伪造资源”，**不建议随意伪造图片**。若您希望在 Step 4 修复上游裂图，可从本地真实渲染的 `Number subtle change` 卡片（明/暗模式）截取真实缩略图补齐，或保持原样与官网 1:1 并向官方提 Issue/PR。

---

### Diff-05（专项调查）：`/favicon.ico` 与 `/favicon.svg` HTTP 404
- **严重程度**：低（仅个别独立演示页触发浏览器默认 favicon 探测 404，不影响页面显示）
- **归类**：**确认的上游原始遗漏（本地与官网 100% 相同，非本地环境引入）**
- **实测证据**：
  - 双端直接请求 `/favicon.ico` 与 `/favicon.svg` 均返回 **HTTP `404`**；而 `/assets/favicon-light-mode.png?v=4` 与 `/assets/favicon-darkmode.png?v=4` 在双端均为 **HTTP `200`**。
  - 源码 `<head>` 核查：`/article.html` 与 `/skills/transitions-ui-generation/demo.html` 在双端均未声明 `<link rel="icon">`，因此浏览器自动探测 `/favicon.ico` 报 404；而 `/number.html` 已正确声明 `assets/favicon-light-mode.png?v=4` 与 `assets/favicon-darkmode.png?v=4`。
- **建议**：属于上游一致现象，非必须修复项；若追求控制台/服务端零 404 噪音，Step 4 可仅在缺少 `<link rel="icon">` 的静态页补充指向现有 `assets/favicon-*.png` 的声明（不新增伪造文件）。

---

### Diff-06：第三方接口与随机示例数据（信息项，无缺陷）
- **严重程度**：无（Info）
- **实测证据**：
  1. `/skill.html` 与 `/example.html` 的演示卡片联系人姓名/邮箱每次加载随机生成（如 `Ava Smith` vs `Ava Wilson`），属演示脚本正常行为。
  2. `https://api.github.com/repos/Jakubantalik/transitions.dev` 在匿名请求下返回 HTTP `403`（GitHub 频率限制），双端均通过 `img.shields.io` 回退显示 `6.4k`。

---

## 4. 视觉高保真 1:1 总结判定

| 范围 | 是否达到 1:1 | 明确差距说明 |
| --- | :---: | --- |
| **Library 全部 44 张卡片、Detail、Prototypes、Docs、Skill、Example、Number、Transitions 静态页** | **是（1:1）** | 桌面 `1440×900` 与移动 `390×844` 高度差 `0.00px`，字体/栅格/卡片尺寸/圆角/边框/暗色主题完全一致 |
| **整站完整复现（含首页社区流、账户与 Pro 源码）** | **否（受架构边界与 1 处 localhost 调试控件限制）** | 差距明确为 3 项：<br>1. 私有后端 `api.transitions.dev` 缺失导致首页缺少社区轮播（`-794px` 桌面 / `-778px` 移动）与 `/me` `/stats/people` `/geo` 实时数据（不可绕过，属 `Unverified`）；<br>2. 11 个 Pro 动效的可读 CSS/React 源码锁在私有仓库与付费墙后（不可绕过，属 `Unverified`）；<br>3. `assets/community/feed.js:68` 在 `localhost`/`127.0.0.1` 自动挂载 `beam-dev.js` 调试面板，本地首页右上/右下角多出官网不存在的浮窗（可在 Step 4 决定是否按 `?dev` 收敛）。 |
