# Transitions.dev

Transitions.dev is an interactive collection of reusable CSS transitions. Each card on the index page demonstrates a different interaction pattern and ships a copy-ready, portable CSS snippet for each.

Live site: https://transitions.dev/

## Transitions

| # | Name | What it shows |
|---|------|---------------|
| 1 | **Card resize** | Smooth card resize transition. |
| 2 | **Number pop-in** | Digit flip with blur and stagger. |
| 3 | **Notification badge** | Diagonal slide with spring pop-in. |
| 4 | **Text states swap** | Text swap transition with blur. |
| 5 | **Menu dropdown** | Origin-aware open / close transition. |
| 6 | **Modal open / close** | Modal transition with scale. |
| 7 | **Panel reveal** | Panel open / close transition. |
| 8 | **Page side-by-side** | Forward / back page transition. |
| 9 | **Icon swap** | Scale and blur icon swap. |
| 10 | **Success check** | Confirmation icon with fade, rotate, blur, Y-bob, and SVG path draw. |
| 11 | **Avatar group hover** | Hovered avatar springs up while neighbours follow with a falloff. |
| 12 | **Error state shake** | Input shake on validation error with auto-revert to neutral. |

Each card has a copy button that emits a self-contained CSS snippet: semantic CSS custom properties on `:root`, the transition rules namespaced under `t-*` classes, and a `@media (prefers-reduced-motion: reduce)` guard — so you can paste the snippet into any project and apply it to any component without pulling in demo-specific markup or sizing.

## Install with the CLI (npm)

Pull any transition into your project from the terminal — no manual copy:

```bash
npx transitions-dev add card-resize      # free, no account needed
npx transitions-dev add --free           # every free transition at once
npx transitions-dev list                 # see everything available
```

Pro transitions unlock after a passwordless browser sign-in (device flow — no API key in your terminal):

```bash
npx transitions-dev login                # opens the browser to confirm a code
npx transitions-dev add confetti-burst    # pulls the Pro CSS + React
```

CLI source: [`cli/`](./cli) (published as the public `transitions-dev` package; `transitions-pro` is the old name and forwards to it — it holds no premium source; Pro recipes are fetched from the authenticated API).

## Use as an agent skill

The same transitions are packaged as an installable agent skill so AI coding tools (Cursor, Claude Code, Codex, …) can apply them directly inside your project.

```bash
npx skills add Jakubantalik/transitions.dev
```

Skill source lives in [`skills/transitions-dev/`](./skills/transitions-dev) — `SKILL.md`, eighteen per-transition reference files (`01-card-resize.md` … `18-texts-reveal.md`), and `_root.css` (the universal install block on its own).

The same command also installs [`skills/transitions-ui-generation/`](./skills/transitions-ui-generation): it makes any UI appear as if an agent is generating it. A dotted wireframe is drawn from the element's own structure, then the real UI is revealed. It has two modes, `skeleton` and `skeleton-texts` (with an agent log, the motion CSS being written and a step line), and one dependency-free runtime for any framework. Preview it with [`demo.html`](./skills/transitions-ui-generation/demo.html).

Besides components (`ui`, the default), it has variants for big type and photos: `text` (every glyph of a headline traced along its real outline in dots, then fading in), `small-text` (a dotted line through each line), `image` (a sketch of the photo's tones, a halftone of its own pixels, colour dots, then the photo) and `auto` (all of them in one block). Preview them with [`demo-text.html`](./skills/transitions-ui-generation/demo-text.html) and [`demo-editorial.html`](./skills/transitions-ui-generation/demo-editorial.html).

The skill is generated from `index.html` so the snippets always match what the showcase site demonstrates. Re-run after editing the source site:

```bash
npm run build
```

`build/extract.mjs` parses `PROTO_TEMPLATES` and the `:root { --pX-* }` block out of `index.html`, then re-renders every file under `skills/transitions-dev/` from the templates in `build/templates/`.

## Refine tool

**Refine** is a live, agent-driven companion to the skill. One command drops a timeline + Refine panel onto your running app — no `npm install`, no source edits of your own — and every "Refine" click asks your coding agent to align the selected CSS/Motion transition to the transitions.dev motion tokens (or replace it with a transition from the library). Suggestions appear in a panel that slides in from the right; you pick which to apply as live, reversible overrides.

```bash
# inject the panel + start the local relay (deterministic suggestions work immediately)
npx transitions-refine live

# same, but also installs/wires the Cursor CLI so the relay answers with the LLM, persistently
npx transitions-refine live --llm

# remove the injected <script> tag again
npx transitions-refine stop
```

Without `--llm`, you can instead run `/refine live` in your editor to back the panel with the in-IDE agent. Source lives in [`refine/`](./refine) and ships as the public npm package `transitions-refine`. See [`refine/README.md`](./refine/README.md) for the full flow and env knobs.

## Files

- `index.html` — main showcase page with all eighteen transitions and per-card "copy CSS" buttons.
- `prototypes.html` — interactive playground for each transition with live tuning controls (durations, distances, easings).
- `skill.html` — landing page for the agent skill (install instructions + side-by-side compare embed).
- `example.html` — modal demo with a side-by-side "generic AI output" vs "with Transitions.dev skill" toggle, embedded inside `skill.html` as an iframe.
- `skills/transitions-dev/` — published skill payload (consumed by `npx skills add`).
- `refine/` — the **Refine** tool: a CLI that injects a live timeline + Refine panel and runs a local relay (published to npm as `transitions-refine`).
- `build/extract.mjs` + `build/templates/` — regenerator that keeps the skill in lockstep with `index.html`.
- `assets/` — icons, favicons, and the social-share OG image.
- `site.webmanifest`, `robots.txt`, `sitemap.xml` — PWA/SEO metadata.

## Run locally

```bash
python3 -m http.server 8765
```

Then open http://127.0.0.1:8765/.

## License

You (and any coding agent working for you) may use the transitions and skills in unlimited personal and commercial projects, modify them, and ship them to your users. The only restriction: don't redistribute the library itself as a competing transitions library or kit. The tooling (CLI, agent, Refine) is MIT. See [LICENSE](LICENSE) and the [full terms](https://transitions.dev/terms.html).

---

## Personal Research Branch Notes（个人研究分支说明）

> 本节为个人研究分支 `research/transitions-dev-study` 追加的本地复现、动效审计与合规说明；上方第 1–106 行完整保留原作者 Jakub Antalik 的原始 `README.md`，上游 `main` 分支保持零改动。

### 项目来源与版权归属
- **上游原仓库**：[https://github.com/Jakubantalik/transitions.dev](https://github.com/Jakubantalik/transitions.dev)
- **官方网站**：[https://transitions.dev/](https://transitions.dev/)
- **原作者**：Jakub Antalik（[@Jakubantalik](https://x.com/jakubantalik)）
- **基线 Commit SHA**：`add5b467b13be16664d10fd888330ce2f3288b07`
- **许可与不可再分发边界**：本项目严格遵守根目录 [LICENSE](LICENSE)。动效与 Skills 不得重新包装为竞争性动效库再分发；官网设计、品牌、Logo、宣传视频与商业字体 `assets/fonts/Saans-Medium.woff2/.woff` 保留原作者所有权利（All Rights Reserved）。

### 本地安装与运行（零构建依赖）
本站点为纯静态原生 HTML / CSS / Vanilla JS 项目（无需安装 React / Next.js / Vite 或第三方 npm 运行时依赖），仅需 Python 3：

```bash
python3 -m http.server 8765 --bind 127.0.0.1
```

启动后访问：
- **干净访客展示 / 录屏模式**：`http://127.0.0.1:8765/`（本分支在 `assets/community/feed.js` 中将开发调试面板 `beam-dev.js` 收敛为显式 `?dev` 触发，默认画面与官网访客一致）
- **开发调试模式（保留原始 Border Beam 调参面板）**：`http://127.0.0.1:8765/?dev`
- **常用研究与演示入口**：`/library.html`、`/detail.html?t=card-resize`、`/prototypes.html`、`/skill.html`、`/example.html`、`/number.html`、`/transitions/`、`/skills/transitions-ui-generation/demo.html`

### 已知限制与未验证项（Unverified）
1. **私有后端服务（`api.transitions.dev` / `localhost:8787`）**：登录会话 (`/me`)、地区 PPP 折扣 (`/geo`)、实时会员数 (`/stats/people`)、首页社区组件流 (`/community/feed`)、Builder/Studio 持久化、Stripe 结账、在线 AI Fix 与远程 MCP 均依赖闭源私有后端，本地运行时预期出现 `localhost:8787` 连接拒绝报错，标注为 `Unverified`。
2. **11 个 Pro 动效源码**：公开仓库仅包含与官网一致的混淆交互预览，可读 CSS/React 源码与 Pro Skill 位于私有仓库并受付费墙保护，标注为 `Unverified`（未做任何绕过或反混淆）。
3. **上游原始 404（本地与官网一致）**：Cmd+K 搜索框引用的 `assets/community/thumbs/number-subtle-change.jpg` / `-dark.jpg` 及部分独立演示页的 `/favicon.ico` 在官网同样返回 HTTP 404，本分支未伪造资源。

### 研究文档索引
- [docs/source-audit.md](docs/source-audit.md) — 源码架构、依赖与授权边界审计
- [docs/feature-inventory.md](docs/feature-inventory.md) — 全站 34 页、44 动效与工具链功能清单
- [docs/motion-audit.md](docs/motion-audit.md) — 全量 44 个动效参数、双端实测与 8 组核心对齐轨迹对比
- [docs/visual-diff.md](docs/visual-diff.md) — 桌面/移动端 11 路由视觉对比与专项异常分析
- [docs/github-publishing.md](docs/github-publishing.md) — Fork、独立分支管理、安全扫描与 GitHub 推送方案
- [docs/video-production-plan.md](docs/video-production-plan.md) — 本机 macOS 原生录屏、MP4 导出与原创视频制作规划
