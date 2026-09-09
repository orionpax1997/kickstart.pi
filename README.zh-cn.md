# kickstart.pi

一个注释详尽、开箱即用的 [Pi](https://github.com/earendil-works/pi-mono) 配置起点，让你真正理解它在做什么。

> 灵感来源：[kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim) 与 opencode 版本（[kickstart.opencode](https://github.com/orionpax1997/kickstart.opencode)）。

[English](README.md) | [简体中文](README.zh-cn.md)

---

## 目录

- [设计理念](#设计理念)
- [快速开始](#快速开始)
- [项目结构](#项目结构)
- [如何让它为你所用](#如何让它为你所用)
- [这不是什么](#这不是什么)
- [内置](#内置)
- [美化](#美化)
- [节省 token](#节省-token)
- [Agentic 工作流](#agentic-工作流)
- [探索代码库](#探索代码库)
- [远程控制](#远程控制)
- [附加功能](#附加功能)
- [AGENTS.md](#agentsmd)

---

## 设计理念

大部分"AI 配置起点"仓库给你的是一个成品。你拿到了能力，但不知道每一步在做什么、为什么这么做。

**`kickstart.pi` 反过来**：

- 每个文件都短小且注释详尽
- 每个决定都解释原因，不只是展示结果
- 它是起点，不是框架
- 你应当添加自己的设置、删掉你用不到的
- 不内置 skill、不内置 agent —— 你需要时再添加

---

## 快速开始

**让 pi 帮你安装**（推荐）

把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation.md
```

**手动安装**

> ⚠️ 如果 `~/.pi/agent/` 已有配置，请先备份。

```bash
git clone https://github.com/orionpax1997/kickstart.pi ~/.pi/agent
pi
```

之后在任何项目目录下启动 pi：

```bash
cd /path/to/project
pi
```

---

## 项目结构

```
kickstart.pi/
│
├── LICENSE
├── README.md            ← 你正在阅读的英文版
├── README.zh-cn.md      ← 你正在阅读的这份
└── docs/
    ├── installation.md        ← 安装 / 备份 / 升级
    ├── installation-rtk.md    ← 可选：token 节省的 bash 重写器（全局）
    ├── installation-caveman.md   ← 可选：token 节省的 prose 重写器（项目级）
    ├── installation-matt-pocock-skills.md   ← 可选：mattpocock/skills，写给真实工程师的 skill 集（项目级）
    ├── installation-superpowers.md  ← 可选：agentic workflow skills（项目级）
    ├── installation-openspec.md   ← 可选：spec-driven 开发工作流（项目级）
    ├── installation-codegraph.md   ← 可选：预索引的代码知识图谱（项目级）
    ├── installation-codebase-memory-mcp.md   ← 可选：SQLite 知识图谱（项目级）
    ├── installation-agent-browser.md   ← 可选：CDP 浏览器自动化（项目级）
    ├── installation-subagents.md   ← 可选：Claude Code 风格 sub-agent（全局）
    ├── installation-permission-system.md   ← 可选：工具、bash、MCP、skill 的确定性 allow / ask / deny 权限闸门（全局）
    ├── installation-remote-pi.md   ← 可选：本地 agent 网格 + 手机 App（全局）
    ├── installation-pi-web.md   ← 可选：pi 会话的本地浏览器 UI（全局）
    ├── installation-open-tui.md   ← 可选：动画 logo 头 + Starship 状态栏 + 圆角编辑器（全局）
    ├── installation-themes-bundle.md   ← 可选：十六套终端调色板（全局）
    ├── installation-rounded-tools.md   ← 可选：内置工具的圆角边框（全局）
    └── installation-tui-commands.md   ← 可选：把 TUI 工具（lazygit、nvim、htop …）变成斜杠命令（全局）
```

就这些。**没有 settings.json、没有 skill、没有 agent、没有扩展**。`kickstart.pi` 刻意保持精简 —— 你的模型、主题、其它工具，都由你自己在 `~/.pi/agent/` 中配置。唯一必须装的扩展是 [`docs/installation.md`](docs/installation.md) Step 3 的三个 MCP 服务器（`context7`、`searchcode`、`exa`）。

---

## 如何让它为你所用

1. **通读 `README.md`** —— 理解设计理念与可用工具。
2. `kickstart.pi` 故意不附带任何配置。在首次启动时，pi 会引导你设置 provider、model、theme。
3. **全局装 token 节省工具** —— [rtk](docs/installation-rtk.md) 是唯一推荐全局装的。它会跨项目压缩冗长的 bash 输出。
4. **按需装项目级工具** —— [codegraph](docs/installation-codegraph.md) / [codebase-memory-mcp](docs/installation-codebase-memory-mcp.md) 看代码，[mattpocock/skills](docs/installation-matt-pocock-skills.md) 或 [superpowers](docs/installation-superpowers.md) 提供 skill，[OpenSpec](docs/installation-openspec.md) 走 spec-driven 开发，[caveman](docs/installation-caveman.md) 压缩 prose，[agent-browser](docs/installation-agent-browser.md) 控制浏览器。每个都只对你 `cd` 进去的项目生效。
5. **或者装全局的会话级工具** —— [pi-subagents-lite](docs/installation-subagents-lite.md) 以极小 token 开销派生 sub-agent；[@gotgenes/pi-permission-system](docs/installation-permission-system.md) 给所有工具、bash、MCP、skill 调用加上统一的权限闸门；[pi-tui-commands](docs/installation-tui-commands.md) 把常用的 TUI 工具变成斜杠命令，运行时先挂起 pi、退出后再恢复。要从浏览器或手机操控 pi，跳到 [远程控制](#远程控制) 装 [pi-web](docs/installation-pi-web.md) 或 [remote-pi](docs/installation-remote-pi.md)。一次安装覆盖全部项目。
6. **修改全局 `AGENTS.md`** —— 加你的语言偏好、工作风格、跨项目都适用的 MCP 用法提示。
7. **在需要 `AGENTS.md` 的项目根加一份** —— 写项目结构、技术栈、编码规范。
8. **在 `.pi/prompts/` 里写自己的 prompt 模板** —— 把重复流程做成 `/your-command` 斜杠命令。

用不到的，删掉。它是起点，不是框架。

---

## 这不是什么

- 不是多 agent 编排系统
- 不是可投产的 AI 流水线
- 不是不需要读就能用的东西

---

## 内置

[`docs/installation.md`](docs/installation.md) 的安装流程把 `kickstart.pi` 开箱即用所需的东西都装好了：

> **Pi 的原生设计是"不需要 MCP"。** Pi 以 extension 和 skill 为核心，直接加载到自己的进程里跑——MCP 不在它的核心架构里。
>
> **那为什么这里还要装一个 MCP 桥？** 因为整个 agent 生态（Context7、SearchCode、Exa，以及大部分第三方工具）都用 MCP 通信。`pi-mcp-adapter` + 几个 MCP 服务器，就是 kickstart.pi 跟那个生态接轨的方式，本身不站队。

**MCP 桥**（Step 3 的前置依赖）：

- **pi-mcp-adapter** — 让 pi 能与 MCP 服务器通信的 adapter 包

**三个必装的 MCP 服务器**（Step 3）：

- **context7** — 库和框架文档查询
- **searchcode** — 公开仓库代码搜索
- **exa** — 网页搜索（会话启动时 eager 加载）

**不附带 `settings.json`** —— pi 首次启动时会引导你选 provider / model / theme。如果已有备份，可以用 `cp ~/.pi/agent.bak/settings.json ~/.pi/agent/` 恢复。

这三个 MCP 是全局 `AGENTS.md` 引用的目标 —— 不装的话，那些指令无从查询。

---

## 美化

### pi-open-tui

[pi-open-tui](https://pi.dev/packages/pi-open-tui) 是 pi 的 TUI 美化扩展——把 `pi-haiku`、`pi-claude-code-tui`、`pi-zentui` 三家之长打包在一起：顶部 16 帧彩色动画 Pi logo、两行 [Starship](https://starship.rs/) 风格的底部状态栏（当前目录、git 分支与状态、运行时版本、上下文条、model、token 计数、cost）、带 accent rail 的圆角编辑器、实时计时器，以及每次任务结束后的 telemetry 通知（TPS / TTFT / 停顿 / cost）。在 pi 里跑 `/open-tui` 可逐项开关头 / 状态栏 / 圆角编辑器、切换 Nerd Font 与 ASCII 图标，并配置 telemetry 段。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-open-tui.md
```

装好后用 `/open-tui` 微调显示项与 telemetry。

### pi-themes-bundle

[@firstpick/pi-themes-bundle](https://pi.dev/packages/@firstpick/pi-themes-bundle) 给 pi 的主题选择器新增十六套终端调色板——Catppuccin、Dracula、Tokyo Night、Gruvbox、Nord、Rosé Pine、One Dark、Solarized、Everforest 各有明暗两版，外加 `matrix` 与 `crimson-noir`。在 `/settings` 里挑选，或在 `~/.pi/agent/settings.json` 里设 `theme`。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-themes-bundle.md
```

装好后用 `/settings` 选调色板，或直接在 `~/.pi/agent/settings.json` 里设 `theme`（例如 `"theme": "tokyo-night"`）。

### pi-rounded-tools

[pi-rounded-tools](https://github.com/orionpax1997/pi-rounded-tools) 是个极简的微调——把 pi 内置工具（`read`、`write`、`edit`、`bash`、`grep`、`find`、`ls`）的边框从方角 `┌┐└┘` 换成圆角 `╭╮╰╯`。没有状态栏壳，也不另加调色逻辑；边框颜色直接跟随主题的 `border` token（运行中变黄、失败变红、成功用主题默认色）。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-rounded-tools.md
```

装好后重启 pi（或 `/reload`），所有内置工具的边框就会换成圆角。

---

## 节省 token

### rtk

[rtk](https://github.com/rtk-ai/rtk) 透明地把冗长的 shell 命令（`git status`、`pnpm list`、`vitest`、`cargo test` 等）改写成节省 token 的紧凑形式。每次 pi 会话都被自动拦截改写 —— 工作流不变，token 变少。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-rtk.md
```

### caveman

[caveman](https://github.com/juliusbrussee/caveman) 把 pi 的自然语言回复压缩约 65–75%，同时保留技术准确性。六个强度等级，由 `/caveman` 或 "caveman mode" / "less tokens" 等关键词触发。

建议**项目级别安装**。先 `cd` 到项目目录，再把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-caveman.md
```

---

## Agentic 工作流

### mattpocock/skills

[mattpocock/skills](https://github.com/mattpocock/skills) 是 Matt Pocock 整理的工程 skill 集——头脑风暴式的需求访谈、TDD、bug 诊断、代码审查、架构调研、issue 分流等等。按任务自动加载；部分 skill 还注册成 `/skill:<name>` 斜杠命令。定位是体量小、好改造、易组合。

建议**项目级别安装**。先 `cd` 到项目目录，再把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-matt-pocock-skills.md
```

### superpowers

[superpowers](https://github.com/obra/superpowers) 提供一套精选 skill，覆盖头脑风暴、调试、TDD、规划、代码审查等场景。按任务自动加载——装好之后无需手动调用。

建议**项目级别安装**。先 `cd` 到项目目录，再把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-superpowers.md
```

### OpenSpec

[OpenSpec](https://github.com/Fission-AI/OpenSpec) 是 spec 驱动的开发工具，用于生成和管理项目规范。

建议**项目级别安装**。先 `cd` 到项目目录，再把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-openspec.md
```

---

## 探索代码库

### codegraph

[codegraph](https://github.com/colbymchenry/codegraph) 把代码库预先索引成知识图谱。agent 一次查询就能拿到精确上下文——调用链路、影响范围、相关符号——而不是逐个文件爬取。

建议**项目级别安装**。先 `cd` 到项目目录，再把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-codegraph.md
```

### codebase-memory-mcp

[codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) 是独立的静态二进制，把代码库索引成持久化的 SQLite 知识图谱（支持 158 种语言、14 个 MCP 工具、毫秒级查询）。和 codegraph 类似，它让 agent 一次调用就能拿到结构化上下文。

建议**项目级别安装**。先 `cd` 到项目目录，再把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-codebase-memory-mcp.md
```

---

## 远程控制

在另一块屏幕上操作 pi —— 浏览器标签页或手机。两个工具都是独立进程，读取 / 改写 pi 落盘的同一份状态（`~/.pi/agent/sessions`、配置、配对设备）。pi 本身感知不到它们，关掉以后 pi 不会有任何变化。

### pi-web

[pi-web](https://github.com/agegr/pi-web) 是 pi 的本地 Web UI。跑一行 `pi-web` 就在 <http://127.0.0.1:30141> 打开一个浏览器工作台——左侧会话列表、中间实时对话、右侧文件树和源码 / 图片 / PDF 预览，顶栏还有模型配置与 skill 开关。它直接读 pi 已经写好的 `~/.pi/agent/sessions/*.jsonl`，所以可以恢复任意一次历史会话，或从早期的某条消息分叉出一条新的探索路线。**不需要**装任何 pi 扩展——pi-web 是个独立的 Node CLI，默认只绑 `127.0.0.1`，可选通过 `PI_WEB_PASSWORD` 启用 Basic Auth。

**全局级别**安装——它是个 CLI，读的是你机器级的 pi agent 目录。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-pi-web.md
```

装好后跑 `pi-web`，浏览器打开 <http://127.0.0.1:30141>。关掉这个进程 pi 完全不受影响——pi-web 本身不存任何状态。

### remote-pi

[remote-pi](https://pi.dev/packages/remote-pi) 在 pi 之上额外提供两项能力，由一条 `/remote-pi` 斜杠命令统一开关：**本地 agent 网格**（在同一目录下开多个 pi 终端，它们通过 Unix 域套接字 broker 互相发现，并由 LLM 调用两个新工具 —— `agent_send` 与 `agent_request`），以及**手机 App**，通过扫码配对的 WebSocket relay 在手机上给 pi 发 prompt / 语音 / 图片，并切换 model 与 thinking 等级。agent 网格完全本地，不走网络；只有手机 relay 触网，并且 payload 端到端加密。

> remote-pi 和 [pi-subagents](#subagents) 是两件事：sub-agent 是**同一个进程内**由主 agent 派生的；remote-pi 的 peer 是**各自独立的 pi 进程**，主动加入同一网格后直接对话。
>
> remote-pi 和 [pi-web](#pi-web) 也彼此独立：pi-web 读的是 pi **已经写下来**的会话，在浏览器里回放；remote-pi 的手机 App 是把 prompt **实时推进**正在运行的 pi 会话。两者互补，不是替代。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-remote-pi.md
```

装好后 `/remote-pi` 跑一次性配置向导，`/remote-pi pair` 扫码绑定 [Remote Pi App](https://remote-pi.jacobmoura.work/)，`/remote-pi status` 查看当前接入的 peer。

---

## 附加功能

### subagents

[pi-subagents-lite](https://pi.dev/packages/pi-subagents-lite) 为 pi 带来 schema 优先、token 开销极低的 sub-agent——可在独立会话中派生专门的 agent，每个 agent 都有自己的工具、扩展与模型，支持前台 / 后台运行。只有三个工具、没有冗长描述；中途介入与续跑、通过 `.pi/agents/*.md`（项目级）或全局定义 agent 类型、按模型并发上限、带成本统计的实时 widget、卡死 agent 的看门狗，全部在 `/agents` 里管理。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-subagents-lite.md
```

装好后用 `/agents` 在 pi 里管理、查看、介入 sub-agent，也可以不经 LLM 手动派生。

### pi-permission-system

[@gotgenes/pi-permission-system](https://pi.dev/packages/@gotgenes/pi-permission-system) 是一道确定性权限闸门,坐落在 agent 与每次工具、bash、MCP、skill 调用之间。三种状态（`allow` / `deny` / `ask`）加四层权限面（`path` → `external_directory` → 工具级规则 → `bash` 规则）覆盖了编码 agent 几乎所有的破坏面——没预先放行的,统一弹 UI 确认框。sub-agent 的 `ask` 提示会自动转发回父会话,让 sub-agent 操作也走同一套规则。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-permission-system.md
```

装好后编辑 `~/.pi/agent/extensions/pi-permission-system/config.json` 写你的策略——先用安装文档里那套安全默认（deny `.env`、bash 默认 ask、cwd 外目录 ask）,再按需收紧。

### agent-browser

[agent-browser](https://github.com/vercel-labs/agent-browser) 通过 CDP（Chrome DevTools Protocol）让 agent 控制浏览器，比传统无头浏览器方案更省 token。

建议**项目级别安装**。先 `cd` 到项目目录，再把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-agent-browser.md
```

### pi-tui-commands

[pi-tui-commands](https://pi.dev/packages/pi-tui-commands) 把任何已经在 `PATH` 里的 TUI 工具注册成 pi 的斜杠命令：`/lazygit`、`/nvim`、`/htop`、`/k9s` …… `/tuicmd` 打开一个可搜索的开关列表（开启时会先 `which` 检查二进制是否存在,所以不会注册到没装的工具）；`/tuicmd add lg lazygit` 还可以注册自定义别名；执行时通过 `ctx.ui.custom()` **先把 pi 的 TUI 挂起、工具退出后再原样恢复** —— 对话、prompt 历史、滚动位置都保留,不需要 alt-tab 切来切去。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-tui-commands.md
```

装好后 `/tuicmd` 打开开关列表，`Enter` 切换 ON/OFF，`/` 模糊搜索。启用集合与自定义命令持久化在 `~/.pi/agent/tui-commands.json`，重启不丢。

### pi-mono-context

[pi-mono-context](https://pi.dev/packages/pi-mono-context) 为 pi 加上 Claude Code 风格的 `/context` 命令，在会话里就地打印当前上下文窗口占用——彩色已用/空闲网格、分类估算（系统提示、工具、消息、剩余空间）、会话统计（轮数、消息数、缓存读写、花费）以及按扩展划分的 token 占用。报告仅供显示：每次 LLM 调用前都会被 hook 过滤掉，查上下文永远不会反过来吃上下文。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-context.md
```

装好后 `/context` 看窗口都花在哪了。

### pi-mono-usage

[pi-mono-usage](https://pi.dev/packages/pi-mono-usage) 为 pi 加上 `/usage` 命令，解析本地 pi 会话文件并渲染一个内联 dashboard：按 provider / model 的 token 花费与成本、成本驱动模式、工具级统计、GitHub 风格的活动热力图（带连击天数）、以及环境足迹估算。全部在本地从 `~/.pi/agent/sessions/` 算出来——数据不出机器。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.pi/refs/heads/main/docs/installation-usage.md
```

装好后 `/usage` 打开 dashboard——`Tab` 切换 Today / This Week / This Month / All Time，`v` 切换五个视图。

---

## AGENTS.md

AGENTS.md 是 pi 在每个会话里加载的全局指令文件。保持简短——只写真正跨项目都适用的内容。

pi 还会从 cwd 向上自动发现 `AGENTS.md` / `CLAUDE.md`，所以项目根的 `AGENTS.md` 会在该项目中覆盖全局。

**全局文件建议包含**：

- **语言偏好** — 如 `Reply in Chinese.`
- **MCP 使用提示** — 如 `Use context7 to look up library and framework documentation.`
- **个人编码偏好** — 如 `Never use any type. Prefer explicit types.`
- **做事风格** — 如 `Keep responses concise. No need to explain obvious steps.`

**项目级 `AGENTS.md`** 放在项目根目录，描述项目结构、技术栈、开发规范等。在该项目中会覆盖全局设置。
