# Transitions.dev GitHub Fork 与发布方案（Step 5 准备）

> **上游原仓库**：[https://github.com/Jakubantalik/transitions.dev](https://github.com/Jakubantalik/transitions.dev)  
> **原作者**：Jakub Antalik（[@Jakubantalik](https://x.com/jakubantalik) / Transitions.dev）  
> **基准 Commit SHA**：`add5b467b13be16664d10fd888330ce2f3288b07`（上游 `main`，本地保持 0 改动）  
> **目标个人 GitHub 账号**：[https://github.com/17860288878k-debug](https://github.com/17860288878k-debug)  
> **目标 Fork 仓库**：[https://github.com/17860288878k-debug/transitions.dev](https://github.com/17860288878k-debug/transitions.dev)（`isFork: true`，上游 `Jakubantalik/transitions.dev`，GitHub Pages 未启用）  
> **个人研究分支**：`research/transitions-dev-study`  
> **提交者身份**：`kai <344023928+17860288878k-debug@users.noreply.github.com>`

---

## 1. 本机 GitHub 授权与环境实测状态

| 检查项 | 实测结果 | 状态 |
| --- | --- | :---: |
| GitHub CLI (`gh`) | 已安装官方校验版 `gh v2.105.0 (macOS_arm64)` 至 `~/.local/bin/gh` | 已就绪 |
| GitHub 登录与协议 | `gh auth status` 验证已登录 `17860288878k-debug`（HTTPS 协议、keyring 存储，无明文 Token） | 已验证 |
| Git 提交者身份（本地仓库级） | `user.name = kai`、`user.email = 344023928+17860288878k-debug@users.noreply.github.com` | 已确认配置 |
| 目标 Fork 仓库 | `https://github.com/17860288878k-debug/transitions.dev`（`isFork: true`，parent = `Jakubantalik/transitions.dev`，`PUBLIC`） | 已创建验证 |
| GitHub Pages 状态 | `GET /repos/17860288878k-debug/transitions.dev/pages` → HTTP `404`（未启用，不抢占 `CNAME=transitions.dev`） | 已确认关闭 |
| 本地 Remote 指向 | `origin` → `17860288878k-debug/transitions.dev.git`；`upstream` → `Jakubantalik/transitions.dev.git`；`main` 跟踪 `upstream/main` (`add5b46`) | 已验证 |

---

## 2. 安全、敏感信息与许可证合规检查结果

### 2.1 敏感信息扫描结果（已执行）
- **密钥/Token 模式扫描**（覆盖 `assets/community/feed.js` 与 `docs/*.md`）：扫描 `ghp_`、`github_pat_`、`sk-`、`AKIA`、`BEGIN PRIVATE KEY`、`Bearer`、`password=`、`api_key=` → **0 命中（Clean）**。
- **敏感文件扫描**（全仓库 `.env*`、`*.pem`、`*.key`、`*secret*`、`*token*.json`）：**0 命中（Clean）**。
- **上游公开配置文件说明**：`agent/worker/wrangler.toml` 包含上游自带的公开 Cloudflare KV/D1 ID，不含任何 API 密钥或个人凭据，且未做改动。

### 2.2 许可证与再分发合规检查结果（已执行）
- **原始 LICENSE 与版权声明**：根目录 [LICENSE](../LICENSE)（Copyright © 2026 Jakub Antalik / Transitions.dev）与各 Skill `LICENSE.txt` **100% 原样保留**。
- **原始 README 与 Git 历史**：`main` 分支保持上游完整 Commit 历史（基线 `add5b467b13be16664d10fd888330ce2f3288b07`）；研究改动全部隔离在 `research/transitions-dev-study` 分支；`README.md` 原文第 1–106 行原样保留，仅在文末追加清晰分隔的研究分支说明。
- **Pro 防泄露钩子核查（`.githooks/pre-commit` 规则）**：已扫描 `index.html` 与 `detail.html`，确认 **0 处**未混淆的 `pro<N>-*` 标识符泄露，Pro 付费墙保持原样。
- **禁止再分发内容核查**：
  - 未新增任何重新打包的动效套件、未反混淆 Pro 源码、未伪造私有后端接口、未替换原作者品牌/Logo/字体资产。
  - **重要提醒 — `CNAME` 与 GitHub Pages 冲突防范**：上游根目录包含 `CNAME`（内容为 `transitions.dev`）。Fork 到 `17860288878k-debug/transitions.dev` 后，**切勿为该 Fork 启用 GitHub Pages**，以免尝试绑定原作者域名 `transitions.dev` 或构成官网视觉设计（All Rights Reserved）的对外再发布。
  - **推荐仓库可见性**：鉴于上游 [LICENSE](../LICENSE#L97-L101) 声明“官网设计、品牌、Logo 及商业字体 `Saans-Medium` 保留所有权利”，若仅用于个人研究与本地演示录屏，**建议确认 Fork 关系，并由您决定设为 `Private`（个人研究最稳妥，注意 GitHub 公开仓库直接 Fork 无法设为 Private，如需私有仅作个人存档需用 Mirror/Import 并保留上游归属，但优先保留公开 Fork 血缘且不开 Pages、不冒充官网）** — 请在下方第 4 节最终确认您的偏好。

---

## 3. 标准 Fork 与独立分支推送方案（待您确认后执行）

### 方案 A（首选）：网页端一键 Fork + HTTPS/SSH 推送（无需安装 `gh`）

1. **在浏览器登录账号 `17860288878k-debug`**，打开上游仓库 [https://github.com/Jakubantalik/transitions.dev](https://github.com/Jakubantalik/transitions.dev)，点击右上角 **Fork**，创建 `https://github.com/17860288878k-debug/transitions.dev`（保留完整 Fork 上游血缘关系，且**不要开启 GitHub Pages**）。
2. **配置本机 Git 身份与双 Remote**（`upstream` 指向原作者，`origin` 指向您的 Fork）：
   ```bash
   cd "/Users/kai/Documents/x 开源/transitions.dev"

   # 配置提交者身份（仅对当前仓库生效，替换为您的邮箱）
   git config user.name "17860288878k-debug"
   git config user.email "<your-github-email>"

   # 启用上游防泄露 pre-commit 钩子
   git config core.hooksPath .githooks

   # 保留上游为 upstream，将 origin 指向您的 Fork
   git remote rename origin upstream
   git remote add origin https://github.com/17860288878k-debug/transitions.dev.git
   git remote -v
   ```
3. **仅在研究分支提交本次最小改动与审计文档（严禁直接提交到 `main`）**：
   ```bash
   # 确认当前处于研究分支且 main 仍停留在 add5b46
   git branch -vv
   git status

   # 仅暂存审计文档、README 追加说明与 feed.js 最小改动
   git add assets/community/feed.js README.md docs/
   git diff --cached --stat
   git commit -m "docs(research): add source/motion/visual audits and gate beam-dev behind ?dev"
   ```
4. **仅推送个人研究分支（不动 `main`）**：
   ```bash
   # 首次 HTTPS 推送时 macOS Keychain 会提示使用 GitHub 账号 + Fine-grained PAT 登录
   git push -u origin research/transitions-dev-study
   ```

### 方案 B（备选）：安装 GitHub CLI (`gh`) 后通过命令行 Fork 与推送

若您偏好命令行完成全部流程，需先安装并登录 `gh`：
```bash
# 1. 安装 gh（例如通过 Homebrew 或官方 macOS 二进制包）后登录指定账号
gh auth login --hostname github.com --git-protocol https --web
gh auth status   # 确认 Logged in to github.com account 17860288878k-debug

# 2. Fork 上游仓库（不自动覆盖本地 main）
cd "/Users/kai/Documents/x 开源/transitions.dev"
gh repo fork Jakubantalik/transitions.dev --remote=true --remote-name=fork

# 3. 提交并仅推送研究分支
git push -u fork research/transitions-dev-study
```

---

## 4. 推送前待您最终确认清单

在执行任何 `git commit` 或 `git push` 前，请您确认以下 4 项：
1. **GitHub 账号与认证方式**：是否已在 `https://github.com/17860288878k-debug` 手动点击 Fork，并准备通过 HTTPS PAT / `gh auth login` / SSH 完成本机认证？
2. **目标仓库形态与可见性**：
   - **选项 1（推荐，最符合“保留完整上游 Fork 关系”）**：公开 Fork `https://github.com/17860288878k-debug/transitions.dev`（GitHub 规则下公开仓的 Fork 默认为 Public），保持 `main` 与上游一致、不启用 GitHub Pages，仅推送 `research/transitions-dev-study` 分支；
   - **选项 2（私有个人存档）**：创建私有仓库 `17860288878k-debug/transitions.dev`（Private，注：GitHub 不允许将 Public 仓的 Fork 直接改为 Private，因此将失去 GitHub UI 上的“forked from”标签，但保留完整原始 Git 历史、`LICENSE` 与 `upstream` remote）。
3. **Git 提交身份**：提交使用的 `user.name` 与 `user.email`（例如 GitHub noreply 邮箱）。
4. **本次推送包含的文件范围**：
   - `assets/community/feed.js`（仅 1 行：`beam-dev.js` 收敛为 `?dev` 触发）
   - `README.md`（保留原文 1–106 行，文末追加研究说明）
   - `docs/source-audit.md`、`docs/feature-inventory.md`、`docs/motion-audit.md`、`docs/visual-diff.md`、`docs/github-publishing.md`、`docs/video-production-plan.md`
