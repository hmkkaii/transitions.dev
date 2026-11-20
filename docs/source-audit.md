# Transitions.dev 源码与授权审计报告（Step 1）

> **上游仓库**：[https://github.com/Jakubantalik/transitions.dev](https://github.com/Jakubantalik/transitions.dev)  
> **官方网站**：[https://transitions.dev/](https://transitions.dev/)  
> **原作者**：Jakub Antalik（[@Jakubantalik](https://x.com/jakubantalik) / Transitions.dev）  
> **作者演示视频**：[https://x.com/Jakubantalik/status/2119464965551931531](https://x.com/Jakubantalik/status/2119464965551931531)  
> **审计基准 Commit SHA**：`add5b467b13be16664d10fd888330ce2f3288b07`（分支 `main`）  
> **个人研究分支**：`research/transitions-dev-study`（未改动上游任何源码文件）

---

## 1. 仓库定位与运行方式（非框架型纯原生静态站）

经源码核查，`transitions.dev` **不是** React / Next.js / Vite 应用，而是一个**零构建依赖的原生 HTML + CSS + Vanilla JavaScript 静态站点**（通过 `CNAME` 部署于 GitHub Pages），并附带多个零依赖 Node ESM 工具子包与 Agent Skills：

| 项目维度 | 审计实测结论 | 依据文件 |
| --- | --- | --- |
| 站点前端框架 | **无前端框架**（纯静态 `.html` + 内联/外链 `.css` + 原生 ES/IIFE `.js`） | `index.html`, `library.html`, `detail.html`, `CNAME` |
| 官方本地启动方式 | `python3 -m http.server 8765` → 访问 `http://127.0.0.1:8765/` | `README.md` L96-L102 |
| 第三方 npm 依赖 | **0 个**（根目录与 4 个子包 `package.json` 均无 `dependencies` / `devDependencies`） | `package.json`, `cli/package.json`, `cli-legacy/package.json`, `agent/package.json`, `refine/package.json` |
| 构建与生成脚本 | Node (`>=18`) 运行 `build/extract.mjs` 同步 Skill；Python 3 运行 `scripts/build-transition-pages.py` 生成 44 个 SEO 静态详情页与 `sitemap.xml` | `package.json`, `build/extract.mjs`, `scripts/build-transition-pages.py` |
| 字体托管策略 | `Inter`（v20 / Variable 4.001）与 `Roboto Mono`（v31）**自托管**（SIL OFL 1.1），不请求 Google Fonts；标题使用 `Saans-Medium.woff2/.woff`（商业展示字体，受站点设计版权限制） | `assets/fonts/fonts.css`, `assets/fonts/Saans-Medium.woff2` |

> **本地工具链检查提示**：当前宿主机已内置 `/usr/bin/python3` 与 `git`，满足官方站点启动要求；系统 PATH 中暂未全局安装 `node` / `gh`，因此无需依赖 Node 即可完整运行静态站点，但在执行 `npm run build` 或子包 CLI 测试前需确认 Node (`>=18`) 环境，在 Step 5 推送前需确认 GitHub CLI 或 HTTPS/SSH 认证方式。

---

## 2. 目录结构与子系统职责

```text
transitions.dev/
├── *.html (34 个顶层页面)          # 官网全部公开页、演示页、产品页、账户/管理壳页与法律页
├── transitions/ (44 个子目录 + hub) # 由 scripts/build-transition-pages.py 生成的静态 SEO 详情页
├── assets/
│   ├── fonts/                     # 自托管 Inter / Roboto Mono (OFL) + Saans-Medium (All Rights Reserved)
│   ├── community/                 # Community/Builder/Studio 运行时、JSON 清单、缩略图、设计系统提示词
│   ├── ui-generation/             # UI Generation 动效运行时（首页 Composer 线框生成动画等）
│   ├── pro-client.js              # Pro 登录、会话、PPP 折扣、Stripe 结账与设备授权前端客户端
│   ├── cmdk-search.css/.js        # 全站 Cmd+K 全局搜索与分类检索面板
│   ├── nav-products.css/.js       # 顶栏导航、Products 下拉菜单与 SVG Chevron 形变动画
│   ├── brand-mark.js              # 品牌 Logo 悬停微交互（6 箭头旋转/遮罩）
│   ├── number.js                  # 零依赖数字动效引擎（spin / pop / subtle 三模式）
│   ├── *.mp4 / og-*.jpg / avatars/ / customers/ / testimonials/ / proto-images/ / tokens/
├── skills/
│   ├── transitions-dev/           # 36 个可移植 CSS 动效参考（01~36）、SKILL.md 与 _root.css
│   ├── transitions-polish/        # 动效 Token 审计与精细化调优规则（_refine-rules.md）
│   └── transitions-ui-generation/ # AI 生成态线框/字形/图片揭示运行时与独立演示页
├── cli/                           # `transitions-dev` v0.4.0 CLI（MIT，内置 33 个免费配方）
├── cli-legacy/                    # `transitions-pro` v0.3.0 废弃转发包（MIT）
├── agent/                         # `transitions-agent` v0.11.2 源码扫描器 + GitHub Action + Cloudflare Worker 源码（MIT）
├── refine/                        # `transitions-refine` v0.3.34 实时时间轴与 Refine 面板 + 本地 Relay（MIT）
├── build/                         # `extract.mjs` 与模板：从 `index.html` 提取 `PROTO_TEMPLATES` 与 `--pN-*` 生成 Skill
├── scripts/                       # `transitions-data.json`（44 条目）、静态页生成脚本、Community 构建脚本
├── .githooks/pre-commit           # 提交钩子：禁止将未混淆的 `pro<N>-*` 源码提交到公开仓库
└── .github/workflows/             # npm 发布流水线（cli / cli-legacy / agent / refine）
```

---

## 3. HTML / CSS / JavaScript 与动画组织方式

### 3.1 静态大文件单页自包含架构
核心展示页采用“单文件内联样式 + 内联多段脚本 + 少量共享外链资产”的组织模式：
- `index.html`（733 KB，16,203 行）：6 个 `<style>`、58 个 `<script>`、60 个独立 `@keyframes`、61 处 `prefers-reduced-motion` 保护、33 组 `--pN-*`（`p1..p35`）变量及 `PROTO_TEMPLATES` 代码模板表。
- `library.html`（700 KB，15,657 行）：4 个 `<style>`、53 个 `<script>`、59 个 `@keyframes`、58 处 `prefers-reduced-motion`。
- `detail.html`（791 KB，17,699 行）：客户端按 `?t=<slug>` 或 `?doc=introduction` 渲染单动效详情、实时调参台、CSS/React 代码面板；包含 59 个 `@keyframes`、63 处 `prefers-reduced-motion`。
- `prototypes.html`（319 KB，7,133 行）：35 组 `--pN-*` 变量与交互式调参实验台（无 `pro-client.js` 依赖，纯本地可运行）。

### 3.2 五维 Motion Token 体系（`skills/transitions-dev/_root.css`）
全站动画基于五类共享语义 Token 与组件级变量驱动，且每条免费动效在复制片段中下发字面量以便独立粘贴：
1. **Durations**：`--duration-stagger` (`40ms`)、`--duration-micro` (`80ms`)、`--duration-quick` (`150ms`)、`--duration-fast` (`250ms`)、`--duration-medium` (`350ms`)、`--duration-slow` (`400ms`)、`--duration-very-slow` (`500ms`)。
2. **Easings**：
   - `--ease-smooth-out`: `cubic-bezier(0.22, 1, 0.36, 1)`（主曲线：展开/收起、滑动、尺寸变化）
   - `--ease-bounce`: `cubic-bezier(0.34, 1.36, 0.64, 1)`（徽章弹出）
   - `--ease-bounce-strong`: `cubic-bezier(0.34, 3.85, 0.64, 1)`（头像回弹）
   - `--ease-in-out` / `--ease-out` / `--ease-linear`
3. **Distances**：`4px`（micro）、`6px`（small）、`8px`（base）、`12px`（medium）、`30px`（large）。
4. **Scales**：`0.96`（large/modal）、`0.97`（medium/dropdown open）、`0.98`（small/tooltip）、`0.99`（tiny/dropdown close）。
5. **Blur**：`2px`（small）、`3px`（medium）、`8px`（large）。

### 3.3 动画实现技术分层
- **纯 CSS Transition / Keyframes**：如 `shimmer-text`、`accordion`（`grid-template-rows`）、`card-resize`、`tooltip-open-close`、`modal-open-close` 等。
- **CSS 变量 + 轻 JS 状态机**：如 `menu-dropdown`（原点感知 + 开启/关闭不对称时长）、`error-state-shake`（分段贝塞尔 + 自动回退）、`tabs-sliding`（测量 active 指示器偏移）、`page-side-by-side`（前进/后退方向控制）、`banner-stacking`（三层堆叠深度与展开状态）。
- **独立 JS 动效引擎**：
  - `assets/number.js`（543 行）：根据字号动态插值模糊与位移的 3D 滚筒数字引擎（`spin` / `pop` / `subtle`）。
  - `assets/nav-products.js`：168ms 可中断 SVG 路径形变（`chevronMorph`）。
  - `assets/brand-mark.js`：SVG 6 箭头遮罩分组悬停动画。
  - `assets/ui-generation/ui-generation.js` + `assets/community/composer-generate.js`：测量真实 DOM 几何并绘制点阵线框，1.6s 过程于 0.93s 触发 cross-blur 揭示，任意交互立即中断取消。
- **Pro 预览卡片（混淆交付）**：11 个 Pro 动效的交互演示代码在 `index.html`、`library.html`、`detail.html` 中以**混淆后的 CSS/JS** 运行（由 `.githooks/pre-commit` 强制校验禁止泄露可读 `pro<N>-*` 标识符），确保本地浏览器可交互体验但不会泄露付费源码。

---

## 4. 能力分层：开源能力 vs 付费能力 vs 线上专有能力

| 能力类别 | 涵盖范围 | 本地可运行状态 | 数据/后端依赖 |
| --- | --- | --- | --- |
| **开源 / 免费动效与静态站点** | 33 个免费动效（完整 CSS/React 代码、实时预览、调参台）、3 个补充 Skill 动效（`text-morph`、`text-swap-soft`、`donut-chart`）、3 套 Agent Skills（`transitions-dev`、`transitions-polish`、`transitions-ui-generation`）、`prototypes.html`、`article.html`、`example*.html`、`number.html`、`transitions/` 静态页 | **完全本地运行** | 仅静态文件（零 API 依赖） |
| **MIT 开源工具链** | `cli/`（`transitions-dev`）、`cli-legacy/`、`agent/`（含本地扫描器与 Worker 架构源码）、`refine/`（本地实时时间轴注入与 Relay）、`build/`、`scripts/` | **本地可运行（需 Node >=18；免费功能离线可用）** | 免费命令离线；Pro/AI 修复命令需线上 API 或本地代理 |
| **付费 Pro 能力** | 11 个 Pro 动效的可读 CSS/React 源码与 Pro Skill（`confetti-burst`, `gooey-plus-menu`, `card-stack-hover`, `organic-shimmer`, `drag-drop-with-physics`, `image-open-tilt`, `spinner-to-check-morph`, `pro-gradient-text`, `delete-with-smoky-dissolve`, `image-generation-placeholder`, `get-pro-button`） | **前端混淆预览可本地交互；源码复制/下载锁在付费墙后（Unverified）** | 可读源码存于私有仓库 `transitions-pro`，经 `https://api.transitions.dev` 鉴权下发 |
| **线上专有后端与平台服务** | 账户登录（Magic Link / GitHub / ChatGPT OAuth / CLI Device Flow）、Stripe 结账与账单门户、PPP 地区折扣 `/geo`、Community 实时组件流与发布/点赞、Builder / Studio AI 生成与存储、MCP 托管端点（`api.transitions.dev/ui/mcp`）、CRM 管理后台（`admin.html`）、在线 Motion Agent AI Fix（`api.transitions.dev/v1/agent/*`）、`api.libraries.dev` | **前端壳页可打开，但 API 请求在本地指向 `http://localhost:8787` 预期失败（Unverified）** | 私有后端 `transitions-pro` Cloudflare Worker + D1 + KV + Stripe + Anthropic API；统计请求 `jakub.goatcounter.com`；Community React 预览沙箱依赖 `esm.sh` |

> **关键本地运行发现（`assets/pro-client.js` & `assets/community/community.js`）**：  
> 当站点运行于 `localhost` 或 `127.0.0.1` 时，两个客户端脚本会自动将 `API_BASE` 切换为 `http://localhost:8787`（供作者本地联调私有 Worker），因此在无私有后端的本地环境中，浏览器控制台与网络面板预期会出现针对 `localhost:8787`（如 `/me`、`/geo`、`/stats/people`）的连接拒绝错误。此为架构边界，**不得**通过伪造接口或绕过鉴权来掩盖。

---

## 5. 许可协议与不可再分发内容边界（LICENSE & Terms 审计）

仓库根 `LICENSE`（Copyright © 2026 Jakub Antalik / Transitions.dev）采用**三段式复合授权**：

1. **动效与 Skills（自定义免费商业可用许可，限制竞争性再分发）**
   - 覆盖范围：`transitions/`、`skills/` 及站点发布的全部动效代码片段（CSS / React / TypeScript）。
   - 允许：无限个人及商业项目使用、自由修改、随自有产品/网站交付给终端用户、在各类编码 Agent 中安装运行。
   - **禁止再分发**：**不得**将动效库本身（或其主要部分）重新打包、转售或发布为竞争性的动效库、Motion Pack 或 Animation Kit。
   - 模板/主题条款：仅在购买 Pro / Business / Lifetime 计划后方可在售卖或分发的模板/UI Kit 中按实际使用量嵌入，且必须保留 `transitions.dev` 来源注释。
2. **工具链（标准 MIT License）**
   - 覆盖范围：`agent/`、`cli/`、`cli-legacy/`、`refine/`、`build/`、`scripts/`。
   - 要求：保留原始版权声明与 MIT 许可文本。
3. **保留所有权利（All Rights Reserved — 严禁作为独立产品再分发）**
   - 覆盖范围：**Transitions.dev 网站整体设计、视觉品牌、Logo（Brand Mark）、宣传视频（`assets/*.mp4`）、OG 图、客户 Logo（`assets/customers/`）、用户评价头像（`assets/testimonials/`）以及商业字体 `assets/fonts/Saans-Medium.woff2/.woff`**。
   - 结论：本项目仅作个人研究、高保真本地复现验证及学习记录（保留上游 Fork 关系、原始 `LICENSE` 与完整 Attribution），绝不可重新包装或去除原作者归属对外发布。

---

## 6. 风险清单与合规控制

| 风险项 | 级别 | 说明与应对策略 |
| --- | --- | --- |
| 私有 API 缺失导致本地控制台报错 | 中 | `pro-client.js` / `community.js` 在 `localhost` 下请求 `localhost:8787`。Step 2/3 将严格区分“私有后端缺失导致的预期失败”与“静态资源/脚本真实缺陷”，不伪造登录态。 |
| Pro 动效仅提供混淆预览、无可读源码 | 中 | 11 个 Pro 动效在 `transitions-data.json` 中 `css` / `react` 为空，仅支持对比交互预览动画效果；付费源码解锁与导出功能标记为 `Unverified`，绝不反混淆或绕过付费墙。 |
| 外部资源依赖（`esm.sh` / GoatCounter / GitHub API） | 低 | Community 沙箱 iframe 引用 `esm.sh` 加载 React/Sucrase/Three；统计引用 `//gc.zgo.at/count.js`；部分页面拉取 GitHub stars 徽章。在离线或受限网络下可能产生请求失败，需在 Step 2 记录。 |
| `refine-example.html` 与 `website-demo.html` 引用 `localhost:7331` | 低 | 依赖 `transitions-refine` 本地 Relay（端口 7331），未启动 Refine 服务时 `http://localhost:7331/inject.js` 预期无法加载。 |
| 品牌资产与商业字体版权边界 | 高 | `Saans-Medium` 字体与官网视觉设计为 All Rights Reserved。个人研究分支必须通过 Fork 保留完整原始 `LICENSE`、`CNAME` 处理说明及原作者归属，不用于商业再分发。 |
| 敏感信息泄露检查 | 低 | `agent/worker/wrangler.toml` 含上游公开的 Cloudflare KV/D1 ID，仓库不含密钥；Step 5 推送前将全量扫描 `.env`、Token、API Key 及本地缓存文件。 |
