# Transitions.dev 原创演示视频录制环境与制作方案

> **参考视频（尚未拆解，暂不改写动效迎合）**：[https://x.com/Jakubantalik/status/2119464965551931531](https://x.com/Jakubantalik/status/2119464965551931531)  
> **本地录制源**：`http://127.0.0.1:8765/`（由官方 `python3 -m http.server 8765 --bind 127.0.0.1` 驱动，分支 `research/transitions-dev-study`）  
> **合规与真实性原则**：
> 1. **绝不用静态截图拼接假装真实动画视频**；所有素材必须来自本机真实高帧率录屏。
> 2. **绝不修改原始动效参数来迎合尚未拆解的参考视频**；保持官方原始缓动曲线、时长与物理质感。
> 3. 原创演示视频仅作学习/展示交流，明确注明上游项目 `transitions.dev` 及原作者 Jakub Antalik，不得包装为自有动效库产品。

---

## 1. 当前本机视频录制、导出 MP4 与剪辑能力审计（实测）

基于当前宿主机（**macOS 27.0.1**，**Built-in Liquid Retina XDR Display `3456 × 2234 Retina`**）的真实环境检测结果：

| 能力类别 | 工具 / 应用 | 本机状态 | 实测能力与限制 |
| --- | --- | :---: | --- |
| **系统内置原生录屏（CLI）** | `/usr/sbin/screencapture` | **已内置可用** | 支持 `-v` 视频录制、`-R<x,y,w,h>` 精确区域录制、`-l<windowid>` 窗口录制、`-V<秒数>` 定时录制，输出高帧率原生 `.mov`（无需第三方软件） |
| **系统内置原生录屏（GUI）** | `QuickTime Player.app` & macOS 截屏栏 (`⌘⇧5`) | **已内置可用** | 支持 60fps 选区/窗口录制、显示鼠标点击光环、基础首尾裁剪 (`⌘T`) 与多段拼接 |
| **系统内置 MP4 转码与裁剪** | `/usr/bin/avconvert` | **已内置可用** | 支持原生 AVFoundation 硬件加速导出 `.mp4` / `.m4v`（如 `Preset1920x1080`、`Preset3840x2160`、`PresetHEVCHighestQuality`），支持 `--start` / `--duration` 片段裁剪 |
| **原生脚本化合成** | `/usr/bin/swift` (AVFoundation) | **已内置可用** | 可调用系统 `AVMutableComposition` / `AVAssetExportSession` 进行基础无损拼接与导出 |
| **干净浏览器宿主** | `Google Chrome.app`、`Safari.app` | **已安装可用** | 支持独立无痕/录制 Profile，配合 Step 4 改造后的 `http://127.0.0.1:8765/` 输出零调试浮窗画面 |
| **内置隔离 Webview 浏览器** | Agent Browser Sandbox | **不支持录屏** | 不提供 `recordVideo` / CDP screencast 视频导出，仅可用于自动化校验，**不作为视频录制源** |
| **专业命令行后期** | `ffmpeg` / `ffprobe` | **未安装** | 缺少精确帧级剪切、变速（Speed Ramp）、画中画排版与 X/Twitter 最优 H.264 (`yuv420p`) 编码能力；建议后续按需安装 |
| **专业录屏与非编剪辑软件** | OBS Studio / Screen Studio / CapCut / iMovie / DaVinci Resolve / Final Cut Pro | **均未安装** | 若需丝滑镜头推拉缩放（Zoom-to-cursor）、光晕背景框、节拍卡点与配音配乐，建议安装其一（如 Screen Studio / OBS + CapCut / DaVinci Resolve） |

**当前环境能力结论**：
- **录制真实原生视频**：**已具备**（`screencapture -v` / `QuickTime Player` / `⌘⇧5` 直接录制干净 Chrome 窗口，Retina XDR 高清输出）。
- **基础裁剪与导出 MP4**：**已具备**（`avconvert --preset Preset1920x1080` 直接将原生 `.mov` 转为标准 `.mp4`）。
- **高级剪辑（卡点、缩放运镜、多轨合成、X 平台高码率优化）**：**暂未安装专业工具**，后续正式制作前可一键补充 `ffmpeg` 或非编软件（如 Screen Studio / OBS + CapCut / DaVinci Resolve）。

---

## 2. 可供录制的本地高保真页面清单

经过 Step 2–4 回归验证，以下本地页面均 **0 JS 报错**、无 `beam-dev.js` 调试浮窗遮挡，且交互轨迹与官网 1:1 一致：

| 录制场景 | 本地 URL | 画面特点与录制价值 | 录制注意事项 |
| --- | --- | --- | --- |
| **1. 主页干净展示（明/暗主题）** | `http://127.0.0.1:8765/` | Hero 排版、Composer 线框生成动画（`ui-generation`）、Border Beam、精选动效轮播、⌘K 搜索弹窗 | Step 4 已屏蔽默认 `beam-dev.js`；注：主页无社区 API 轮播（不滚动至不存在的区块即可） |
| **2. Library 总览与分类筛选** | `http://127.0.0.1:8765/library.html` | 44 张动效卡片 4 列栅格、All/Essential/AI Agents/Effects/Texts/Pro 切换、卡片直接交互 | 桌面端推荐 `1440×900`（标准 4 列布局）；Cmd+K 搜索内 `number-subtle-change` 缩略图 404（上游缺陷，录屏时避免停留该裂图） |
| **3. 单动效纯净舞台（33 Free + 11 Pro 预览）** | `http://127.0.0.1:8765/detail.html?t=<slug>` | 独立居中舞台（`936×460`）、无多卡片干扰、极适合特写录屏（如 `card-resize`、`number-pop-in`、`menu-dropdown`、`modal-open-close`、`tabs-sliding`、`like-button`、`error-state-shake`、`spinning-counter`、`confetti-burst` 等） | Free 页可展示 Preview → CSS → React 切换；Pro 页仅录公开 Preview，不展示付费墙绕过 |
| **4. 交互式实时调参台** | `http://127.0.0.1:8765/prototypes.html` | 35 组 `--pN-*` 实时时长/缓动/距离参数调节，适合演示“改参数如何改变动效手感” | 纯静态无任何后端报错 |
| **5. AI 动效对比演示** | `http://127.0.0.1:8765/example.html`<br>`http://127.0.0.1:8765/skill.html` | “Generic AI output” vs “With Transitions.dev skill” 同屏对比，非常适合作为痛点引入镜头 | 每次刷新联系人姓名会随机变化，连拍时避免中途刷新 |
| **6. 数字动效三模式专项** | `http://127.0.0.1:8765/number.html` | 独立展示 `Spin` / `Pop` / `Subtle` 三种数字滚筒与变化手感 | 适合微交互细节特写 |
| **7. UI Generation 线框生成演示** | `http://127.0.0.1:8765/skills/transitions-ui-generation/demo.html`<br>`.../demo-text.html`<br>`.../demo-editorial.html` | 点阵线框、字形轮廓描点、图片半色调揭示，极具视觉记忆点 | 纯静态 0 报错 |
| **8. 动效原理教学页** | `http://127.0.0.1:8765/article.html` | Duration / Scale / Movement 交互式对比 | 纯静态 0 报错 |
| **9. Border Beam 调参彩蛋（可选）** | `http://127.0.0.1:8765/?dev` | 仅当需要演示开发者工具面板时显式开启 | 日常展示切勿带 `?dev` |

---

## 3. 干净浏览器录制模式规范

为保证录制画面像素锐利、无浏览器杂质、无扩展图标、与官网访客视图一致，按以下规范启动独立录制 Chrome 窗口：

### 3.1 启动独立干净 Chrome（独立临时 Profile，无书签/扩展/登录态）
```bash
# 确保本地服务已启动
cd "/Users/kai/Documents/x 开源/transitions.dev"
python3 -m http.server 8765 --bind 127.0.0.1

# 在独立临时配置下以 App 模式（无地址栏与标签栏干扰）打开干净窗口，精确设定 1440x900
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --user-data-dir="/tmp/tdev-record-profile" \
  --app="http://127.0.0.1:8765/" \
  --window-size=1440,900 \
  --window-position=120,80 \
  --force-device-scale-factor=2 \
  --no-first-run \
  --no-default-browser-check
```
> 若需录制顶部完整浏览器窗框（或便捷切换路由），将 `--app=...` 改为 `--new-window "http://127.0.0.1:8765/"` 并关闭书签栏（`⌘⇧B`）。

### 3.2 录屏前检查清单（Pre-Flight Checklist）
- 开启 macOS **勿扰模式（Focus / Do Not Disturb）**，关闭弹窗通知与输入法浮动条。
- 缩放比例锁定 `100%`（`⌘0`），确认系统未开启“减弱动态效果”（`prefers-reduced-motion: no-preference`）。
- 明确每组镜头主题（Light 或 Dark），在 `More ⋮ → Appearance` 提前切好并刷新让首次入场动画归位。
- 避免开启 DevTools 面板占用窗口宽度，确保桌面端完整处于 `1440×900`（4 列网格断点）。

### 3.3 原生命令行录制与导出 MP4 实操命令（零第三方依赖）
```bash
# 1. 使用 macOS 原生 screencapture 录制固定区域 15 秒高清 .mov（与上方 Chrome 窗口坐标对齐）
screencapture -v -R120,80,1440,900 -V15 "/Users/kai/Documents/x 开源/video-production/raw/shot-01-hero.mov"

# 2. 使用 macOS 原生 avconvert 裁剪并导出标准 1080p MP4（兼容 X / 网页播放）
avconvert \
  --source "/Users/kai/Documents/x 开源/video-production/raw/shot-01-hero.mov" \
  --output "/Users/kai/Documents/x 开源/video-production/exports/shot-01-hero-1080p.mp4" \
  --preset Preset1920x1080 \
  --start 0.5 \
  --duration 12.0 \
  --replace
```

---

## 4. 视频素材与分镜文件目录规划

> **关键 Git 卫生规范**：大体积 `.mov` / `.mp4` 原始录屏与剪辑工程**严禁提交进 `transitions.dev` Git 仓库**。建议将视频制作目录建立在工作区同级目录 `/Users/kai/Documents/x 开源/video-production/`（独立于 Git 仓库之外），或在仓库内使用并加入 `.gitignore`。

```text
/Users/kai/Documents/x 开源/
├── transitions.dev/                 # Git 研究仓库（保持轻量化、零视频二进制污染）
│   └── docs/video-production-plan.md
└── video-production/                # 独立原创视频制作工作区（不入 Git）
    ├── reference/                   # 参考视频分析笔记（待后续拆解 X 视频结构/节奏/字幕）
    │   └── x-2119464965551931531-notes.md
    ├── storyboard/                  # 分镜脚本、文案、时间轴规划（Markdown / CSV）
    │   ├── shot-list.md             # 镜头表：编号、URL、视口、主题、触发操作、预期时长
    │   └── voiceover-captions.md    # 旁白 / 字幕与 Attribution 致谢文案
    ├── raw/                         # 原始无损录屏素材（.mov，按镜头编号命名）
    │   ├── 01-hero-ui-gen-light.mov
    │   ├── 02-library-grid-browse.mov
    │   ├── 03-free-card-resize.mov
    │   ├── 04-free-menu-modal-tabs.mov
    │   ├── 05-free-like-shake-counter.mov
    │   ├── 06-pro-confetti-preview.mov
    │   └── 07-skill-before-after.mov
    ├── trims/                       # 经 avconvert / ffmpeg 裁切对齐起止帧的精选片段（.mp4）
    ├── audio/                       # 原创配音 / 免版税 BGM / 微交互音效（SFX）
    ├── project/                     # 非编软件工程文件（如 CapCut / DaVinci / Screen Studio）
    └── exports/                     # 最终成片（按平台比例分流）
        ├── master-16x9-60fps.mp4    # 横屏母版（1920×1080 / 60fps，适配 X / YouTube / B站）
        └── vertical-9x16-60fps.mp4  # 竖屏版本（1080×1920，适配 Reels / Shorts / 抖音）
```

---

## 5. 后续视频制作分阶段路线（待参考视频分析后启动）

1. **阶段 A — 参考视频结构拆解**（待执行）：人工观看 [参考 X 视频](https://x.com/Jakubantalik/status/2119464965551931531)，拆解节奏、转场点、画面比例、信息层级与展示顺序，记录至 `video-production/reference/`。
2. **阶段 B — 分镜表定稿**：依据真实拆解结果挑选对应本地页面与动效，填写 `storyboard/shot-list.md`（不修改上游动效参数）。
3. **阶段 C — 原生高帧率录屏**：使用第 3 节干净 Chrome App 模式 + `screencapture -v` / QuickTime（或后续安装的 Screen Studio / OBS）按镜头逐条采集真实视频。
4. **阶段 D — 剪辑、字幕与导出**：使用 `avconvert` 或后续安装的 `ffmpeg` / 非编工具剪辑，并在片尾或简介明确标注原作者 Jakub Antalik 与 `https://transitions.dev/` 来源。
