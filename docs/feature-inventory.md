# Transitions.dev 全量功能清单（Feature Inventory）

> **基准仓库**：[https://github.com/Jakubantalik/transitions.dev](https://github.com/Jakubantalik/transitions.dev)  
> **基准 Commit SHA**：`add5b467b13be16664d10fd888330ce2f3288b07`  
> **说明**：本清单在 Step 1 静态源码审计阶段建立，所有本地验证状态将在 Step 2（本地运行）与 Step 3（高保真校验）中逐项更新；无源码或依赖私有后端/付费授权的功能严格标注为 `Unverified`。

---

## 1. 顶层 HTML 页面清单（共 34 页）

| 页面文件 | 大小 | 所属模块 | 依赖范围 | 本地可运行预期 | Step 2 实测状态 |
| --- | ---: | --- | --- | --- | --- |
| `index.html` | 733 KB | 主页（Hero、Composer UI 生成动画、精选动效网格、Cmd+K） | 静态 + `pro-client.js` + `community.js` | 公开展示与动效交互完整可用；登录/会员数/AI Composer 依赖后端 | **已通过（HTTP 200，0 JS 报错）**；已验证 Products 下拉、⌘K 搜索、Dark/Light 切换、卡片交互；Cmd+K 缺 `number-subtle-change*.jpg`（上游缺陷，待 Step 3 对照官网） |
| `library.html` | 700 KB | 动效库总览页（44 个动效卡片、分类筛选、交互预览） | 静态 + `pro-client.js` + `cmdk-search.js` | 全部 44 个卡片可交互预览（33 免费 + 11 Pro 混淆预览） | **已通过（HTTP 200，0 JS 报错）**；实测 44 张卡片（33 Free + 11 Pro）、All/Pro/Effects 筛选及 Free/Pro 预览交互正常；Cmd+K 同上缺图 |
| `detail.html` | 791 KB | 单动效交互详情页（`?t=<slug>` / `?doc=introduction`，CSS/React 代码面板与 Remix 入口） | 静态 + `pro-client.js` | 33 免费动效完整可用；11 Pro 动效仅预览，代码面板需付费鉴权 | **已通过（HTTP 200，0 JS 报错）**；已实测 `?t=card-resize`（Preview/CSS/React/Copy 正常）、`?t=confetti-burst`（Pro 预览可运行，代码区正确显示付费墙锁，未绕过）、`?doc=introduction`（文档页正常） |
| `prototypes.html` | 319 KB | 交互式调参实验台（35 组 `--pN-*` 参数滑块/输入框） | 纯静态 | 完全离线本地可运行 | **已通过（HTTP 200，0 JS 报错）**；35 个原型卡片与 Transition controls 调参（如 Duration `300→450`）实测生效 |
| `transitions/index.html` 及 44 个子目录 | 静态生成 | SEO 可抓取单动效静态页（`/transitions/<slug>/`） | 纯静态 | 完全离线本地可运行 | **已通过（HTTP 200，0 JS 报错）**；已抽检 `/transitions/` 与 `/transitions/card-resize/`（含 4 块 CSS/React 代码） |
| `pro.html` | 326 KB | Transitions Pro 介绍与定价页（Number pop-in 价格切算） | 静态 + `pro-client.js` | 静态展示与价格动效可用；PPP 折扣与 Stripe 结账需私有 API | **已通过公开部分（HTTP 200，0 JS 报错）**；Monthly/Annual/Lifetime 切换价格动效正常；结账/PPP 为 `Unverified` |
| `skill.html` | 211 KB | Agent Skill 介绍页（安装指令 + `example.html` 对比嵌入） | 静态 + `pro-client.js` | 核心展示与嵌入对比本地可用 | **已通过（HTTP 200，0 JS 报错）**；6 个内嵌 `example.html` iframe 正常渲染 |
| `skill-refine.html` | 165 KB | Skill + Refine 介绍变体页 | 静态 + `pro-client.js` | 核心展示本地可用 | 未单独抽检（结构同 `skill.html`） |
| `refine.html` | 200 KB | Refine 工具产品介绍页（含演示视频 `refine-web*.mp4`） | 静态 + `pro-client.js` | 静态与本地视频可用 | **已通过（HTTP 200，0 JS 报错）**；2 个本地 `.mp4` 均 200 加载 |
| `agent.html` | 237 KB | Motion Agent 产品介绍页（含演示视频 `agent-demo*.mp4`） | 静态 + `pro-client.js` | 静态与本地视频可用 | **已通过（HTTP 200，0 JS 报错）**；本地演示 `.mp4` 均 200 加载 |
| `mcp.html` | 216 KB | Transitions MCP 介绍页 | 静态 + `pro-client.js` + API | 静态文档可用；MCP 远程连接 `api.transitions.dev/ui/mcp` 为 Unverified | 远程 MCP 端点 `Unverified` |
| `example.html` | 123 KB | Modal 多页对比演示（Generic AI vs Transitions.dev Skill） | 纯静态 | 完全本地可运行 | **已通过（HTTP 200，0 JS 报错）**；13 个交互按钮与状态切换正常 |
| `example-dropdown.html` | 42 KB | Share Dropdown 对比演示 | 纯静态 | 完全本地可运行 | 待 Step 3 必要时抽检 |
| `demo-example-skill.html` | 58 KB | 按钮/请求/通知 UI Skill 对比演示 | 纯静态 | 完全本地可运行 | 待 Step 3 必要时抽检 |
| `demo-agent.html` | 111 KB | Agent Workspace 交互演示 | 纯静态 | 完全本地可运行 | 待 Step 3 必要时抽检 |
| `demo2.html` | 195 KB | Stage-only 卡片展示演示 | 纯静态 | 完全本地可运行 | 待 Step 3 必要时抽检 |
| `article.html` | 44 KB | 动效原理文章（Duration / Scale / Movement 交互示例） | 纯静态 | 完全本地可运行 | **已通过（HTTP 200，0 报错，0 失败请求）** |
| `collection.html` | 134 KB | Collection 专题演示页 | 纯静态 | 完全本地可运行 | 待 Step 3 必要时抽检 |
| `number.html` | 26 KB | `assets/number.js` 三模式数字动效独立演示页 | 纯静态 | 完全本地可运行 | **已通过（HTTP 200，0 JS 报错）**；Spin/Pop/Subtle 模式与触发控制正常 |
| `skills/transitions-ui-generation/demo.html` | 独立演示 | UI Generation 线框生成态独立演示页 | 纯静态 | 完全本地可运行 | **已通过（HTTP 200，0 报错，0 失败请求）** |
| `refine-example.html` | 39 KB | Refine 注入示例页（引用 `localhost:7331/inject.js`） | 静态 + 本地 Relay (7331) | 页面静态部分可用；Refine 面板需启动 `transitions-refine` | 需 Relay (`Unverified` 离线) |
| `website-demo.html` | 366 KB | Refine 站点演示页（引用 `localhost:7331/inject.js`） | 静态 + 本地 Relay (7331) | 页面静态部分可用；实时注入需启动 `transitions-refine` | 需 Relay (`Unverified` 离线) |
| `community.html` | 123 KB | Community 社区组件流 | 静态 + `community.js` + 私有 API + `esm.sh` | 仅静态壳可用；实时 feed / 成员数 / 点赞依赖私有 API | `Unverified`（无私有后端） |
| `builder.html` | 135 KB | AI UI Builder 入口页 | 静态 + `community.js` + 私有 API | UI 壳可加载；AI 生成与最近项目依赖私有 API | `Unverified`（无私有后端） |
| `studio.html` | 147 KB | Component Studio 在线编辑/Remix | 静态 + `community.js` + `controls.js` + `esm.sh` | 内置 `?lib=<slug>` 本地库预览部分可用；保存/AI 生成需私有 API | 部分 `Unverified` |
| `profile.html` | 118 KB | 社区个人主页 | 静态 + `community.js` + 私有 API | 依赖私有 API | `Unverified`（无私有后端） |
| `projects.html` | 117 KB | 用户项目列表 | 静态 + `community.js` + 私有 API | 依赖私有 API | `Unverified`（无私有后端） |
| `account.html` | 279 KB | 账户、订阅、团队席位、Token 额度页 | 静态 + `pro-client.js` + 私有 API | 依赖私有 API 与登录会话 | `Unverified`（需授权） |
| `activate.html` | 30 KB | CLI Device Flow 登录激活页 | 静态 + `pro-client.js` + 私有 API | 依赖私有 API | `Unverified`（需授权） |
| `mcp-connect.html` | 24 KB | MCP OAuth 连接页 | 静态 + `pro-client.js` + 私有 API | 依赖私有 API | `Unverified`（需授权） |
| `agent-signup.html` | 9 KB | Motion Agent 免费注册页 | 静态 + `pro-client.js` + 私有 API | 依赖私有 API | `Unverified`（需授权） |
| `success.html` | 191 KB | Stripe 支付成功落地页 | 静态 + `pro-client.js` + 私有 API | 依赖私有 API / Stripe Session | `Unverified`（付费流程） |
| `admin.html` | 132 KB | 内部 Transitions CRM 管理台 | 静态 + 私有 API | 内部管理员工具，无权限 | `Unverified`（内部专有） |
| `terms.html` | 37 KB | 条款与许可协议 | 纯静态 | 完全本地可运行 | 待 Step 2 验证 |
| `privacy.html` | 32 KB | 隐私政策 | 纯静态 | 完全本地可运行 | 待 Step 2 验证 |
| `imprint.html` | 15 KB | 法律声明（Imprint） | 纯静态 | 完全本地可运行 | 待 Step 2 验证 |

---

## 2. 动效库清单（44 个主目录动效 + 3 个 Skill 扩展动效）

### 2.1 主目录 44 个动效（来源：`scripts/transitions-data.json`）

| # | Slug | 名称 | 授权层级 | 简介 | 源码可用性 |
| ---: | --- | --- | --- | --- | --- |
| 1 | `card-resize` | Card resize | **Free** | Smooth card resize transition | 完整公开（HTML/CSS/React/Skill/CLI） |
| 2 | `number-pop-in` | Number pop-in | **Free** | Digits swap on a 3D drum with a bounce | 完整公开 |
| 3 | `notification-badge` | Notification badge | **Free** | Diagonal slide with spring pop-in | 完整公开 |
| 4 | `text-states-swap` | Text states swap | **Free** | Text swap transition with blur | 完整公开 |
| 5 | `confetti-burst` | Confetti burst | **Pro** | Physics confetti that lands on the button | 仅混淆预览；可读源码 `Unverified` |
| 6 | `menu-dropdown` | Menu dropdown | **Free** | Origin-aware open / close transition | 完整公开 |
| 7 | `modal-open-close` | Modal open/close | **Free** | Modal transition with scale | 完整公开 |
| 8 | `panel-reveal` | Panel reveal | **Free** | Panel open / close transition | 完整公开 |
| 9 | `gooey-plus-menu` | Gooey plus menu | **Pro** | Liquid split into a fan of actions | 仅混淆预览；可读源码 `Unverified` |
| 10 | `page-side-by-side` | Page side-by-side | **Free** | Forward / back page transition | 完整公开 |
| 11 | `card-stack-hover` | Card stack hover | **Pro** | Stack fans out with a spring on hover | 仅混淆预览；可读源码 `Unverified` |
| 12 | `icon-swap` | Icon swap | **Free** | Scale and blur icon swap | 完整公开 |
| 13 | `success-check` | Success check | **Free** | Success check with blur and rotate | 完整公开 |
| 14 | `avatar-group-hover` | Avatar group hover | **Free** | Distance-falloff lift with bouncy return | 完整公开 |
| 15 | `error-state-shake` | Error state shake | **Free** | Cubic-bezier shake on error | 完整公开 |
| 16 | `organic-shimmer` | Organic shimmer | **Pro** | Wavy shimmer with phase-locked edge glow | 仅混淆预览；可读源码 `Unverified` |
| 17 | `input-clear-with-dissolve` | Input clear with dissolve | **Free** | Clear with per-word dissolve | 完整公开 |
| 18 | `skeleton-loader-and-reveal` | Skeleton loader and reveal | **Free** | Pulse to content cross-fade | 完整公开 |
| 19 | `texts-reveal` | Texts reveal | **Free** | Two lines rise with offset stagger | 完整公开 |
| 20 | `tabs-sliding` | Tabs sliding | **Free** | Pill indicator follows the active tab | 完整公开 |
| 21 | `drag-drop-with-physics` | Drag & drop with physics | **Pro** | Zone morphs into the image | 仅混淆预览；可读源码 `Unverified` |
| 22 | `image-open-tilt` | Image open tilt | **Pro** | iPadOS-style zoom with 3D tilt and bend | 仅混淆预览；可读源码 `Unverified` |
| 23 | `shimmer-text` | Shimmer text | **Free** | Masked gradient sweep across text | 完整公开 |
| 24 | `tooltip-open-close` | Tooltip open/close | **Free** | Delayed in, travels, instant out | 完整公开 |
| 25 | `3d-tilt` | 3D tilt | **Free** | 3D pointer tilt with cursor glare | 完整公开 |
| 26 | `dropdown-menu-morph` | Dropdown menu morph | **Free** | Button morphs into a menu surface | 完整公开 |
| 27 | `accordion` | Accordion | **Free** | Grid-rows height with chevron morph | 完整公开 |
| 28 | `toast-open-close` | Toast open/close | **Free** | Rises in with fade, blur and scale | 完整公开 |
| 29 | `like-button` | Like button | **Free** | Heart fills and bursts particles | 完整公开 |
| 30 | `learn-more-hover` | Learn more hover | **Free** | Chevron shifts and opens on hover | 完整公开 |
| 31 | `checkbox-check` | Checkbox check | **Free** | Check draws on with a stroke path | 完整公开 |
| 32 | `spinner-to-check-morph` | Spinner to check morph | **Pro** | Spinner pops into a drawn check | 仅混淆预览；可读源码 `Unverified` |
| 33 | `spinning-counter` | Spinning counter | **Free** | Digits roll on a 3D drum to the value | 完整公开 |
| 34 | `number-subtle-change` | Number subtle change | **Free** | Changed digits cross-fade in place | 完整公开 |
| 35 | `toggle` | Toggle | **Free** | Thumb slides with a double bounce | 完整公开 |
| 36 | `pro-gradient-text` | Pro gradient text | **Pro** | Colour washes orbit the letters on a loop | 仅混淆预览；可读源码 `Unverified` |
| 37 | `delete-with-smoky-dissolve` | Delete with smoky dissolve | **Pro** | Image shreds and falls into smoke | 仅混淆预览；可读源码 `Unverified` |
| 38 | `thinking-states` | Thinking states | **Free** | Status line shimmers, then swaps to the next | 完整公开 |
| 39 | `reasoning-stream` | Reasoning stream | **Free** | Agent reasoning scrolls by two lines | 完整公开 |
| 40 | `streaming-text` | Streaming text | **Free** | Words resolve through a soft cross-blur | 完整公开 |
| 41 | `matrix-dot-loader` | Matrix dot loader | **Free** | 16-dot matrix pulses in four patterns | 完整公开 |
| 42 | `banner-stacking` | Banner stacking | **Free** | Banners stack like toasts, three deep | 完整公开 |
| 43 | `image-generation-placeholder` | Image generation placeholder | **Pro** | Dot noise wakes, then resolves into the image | 仅混淆预览；可读源码 `Unverified` |
| 44 | `get-pro-button` | Get Pro button | **Pro** | Pro gradient glows in from the pill's rim | 仅混淆预览；可读源码 `Unverified` |

### 2.2 Skill 扩展动效（3 个，位于 `skills/transitions-dev/`）

| 文件 | 名称 | 说明 |
| --- | --- | --- |
| `33-text-morph.md` | Text morph | 保留新旧文本公共前后缀，仅对变化片段执行宽度缓动与 cross-blur |
| `34-text-swap-soft.md` | Text swap soft | 原地无位移同时 cross-blur 替换数值（适合图表周期/币种切换） |
| `35-donut-chart.md` | Donut chart | 等距圆角圆环图段形变动效 |

---

## 3. 全站公共微交互与独立能力清单

| 模块 | 源码位置 | 核心交互与动效 |
| --- | --- | --- |
| 品牌 Mark 悬停动效 | `assets/brand-mark.js` | 6 箭头 SVG 路径拆分 + 遮罩臂 120° 对称旋回，带 `prefers-reduced-motion` 保护 |
| 顶栏 Products 导航 | `assets/nav-products.css`, `assets/nav-products.js` | 168ms 可中断 SVG Chevron 路径形变 + 下拉面板显隐 + 移动端抽屉 |
| 全局 Cmd+K 搜索 | `assets/cmdk-search.css`, `assets/cmdk-search.js` | 快捷键唤起、分类 Pill 过滤、缩略图网格、Community 懒加载预览（社区内容需后端） |
| 首页 Composer 生成态动画 | `assets/ui-generation/ui-generation.js`, `assets/community/composer-generate.js` | 测量真实元素结构绘制点阵线框，1.6s 动画在 0.93s 处触发 475ms cross-blur 揭示，任意按键/点击即取消 |
| 主题切换（Light / Dark） | 各页面内联样式与图标 `assets/theme-*.svg` | 明暗视觉变量切换及对应视频/图标切换 |
| UI Generation 独立演示 | `skills/transitions-ui-generation/demo*.html` | `ui` / `text` / `small-text` / `image` / `auto` 五种 AI 生成态揭示动画 |
| Community 内置扩展库清单 | `assets/community/libraries.json` | 7 个主题库元信息（`beam`, `orbs`, `gooey`, `voice`, `bots`, `metal`, `image`） |

---

## 4. 工具链与生态子包清单（MIT License）

| 子包目录 | npm 包名与版本 | 功能定位 | 运行前提 |
| --- | --- | --- | --- |
| `cli/` | `transitions-dev` `0.4.0` | 终端拉取 33 个免费动效（离线）或登录后拉取 Pro 配方/Skill | Node `>=18`；Pro 需账号 |
| `cli-legacy/` | `transitions-pro` `0.3.0` | 已废弃旧包名，命令转发至 `transitions-dev` | Node `>=18` |
| `agent/` | `transitions-agent` `0.11.2` | 代码库动效扫描（0–100 评分）、规则审计、托管 AI Fix 与 PR 工作流；`agent/worker/` 含 Cloudflare Worker 服务端代码 | Node `>=18`；托管 Fix 需 License Key 与 `api.transitions.dev` |
| `refine/` | `transitions-refine` `0.3.34` | 向运行中页面注入实时时间轴 + Refine 建议面板，经本地 Relay 对接编码 Agent 写回源码 | Node `>=18`；本地端口 7331 |
| `build/` | `build/extract.mjs` | 从 `index.html` 提取 `PROTO_TEMPLATES` 与 `--pN-*` 重新生成 `skills/transitions-dev/` | Node `>=18` |
| `scripts/` | `build-transition-pages.py` 等 | 根据 `transitions-data.json` 生成 `/transitions/<slug>/index.html` 与 `sitemap.xml` | Python 3 / Node `>=18` |
