---
title: CC Switch：一个工具管住所有AI助手
series: AI系列
episode: "17"
created: 2026-06-01
tags:
  - AI工具
  - ClaudeCode
  - 效率工具
---

# 【AI系列·第17篇】CC Switch：一个工具管住所有AI助手

文 / Parson资金研习社

---

之前几篇讲了 Claude Code 怎么装、DeepSeek V4 怎么接、Obsidian 知识库怎么搭。但有一个问题一直没正面回答：

**当你同时用好几个 AI 工具，配置怎么管？**

Claude Code 用 DeepSeek V4 当主力模型、OpenAI Codex 用 GPT-5.5、偶尔还要切回 Claude 官方 Opus 跑复杂任务——三个工具、三套 API Key、三份配置文件，每次切换都要改环境变量、重启终端、祈祷没改错。

这个问题，CC Switch 一个桌面工具解决了。

---

## 一、CC Switch 是什么

一句话：**它是一个管理所有 AI 编程助手配置的桌面应用，点两下就能切换模型和供应商。**

CC Switch 的 GitHub 仓库有 8.6 万颗星（截至 2026 年 5 月），MIT 开源协议，跨平台支持 Windows、macOS、Linux。它不是一个 AI 模型本身，而是 AI 工具的"遥控器"——你不需要记住任何命令行参数，所有配置在图形界面上点点就行。

**它管的不只是 Claude Code。** 目前支持 6 个 AI 编程 CLI 工具：

| 工具 | 开发商 | 定位 |
|------|--------|------|
| Claude Code | Anthropic | 全能编程助手 |
| Codex | OpenAI | 编程专用 Agent |
| Gemini CLI | Google | 谷歌的命令行 AI |
| OpenCode | 开源社区 | 开源编程助手 |
| OpenClaw | 开源社区 | 开源 Agent 框架 |
| Hermes | 开源社区 | 多模型 Agent |

一个 CC Switch，管六个工具的配置。不用在终端里敲 `export` 了，也不用记那些又长又容易拼错的环境变量名了。

打开 CC Switch，主界面长这样——左侧是供应商列表，顶部是工具切换标签（Claude / Codex / Gemini），每个供应商显示名称和 API 地址，当前正在使用的供应商会标上"当前使用"标签，右侧还能看到已用额度和剩余额度：

![[AI系列/参考图片/cc-switch/main-zh.png]]

---

## 二、解决什么问题

先说一个真实场景。

你装好了 Claude Code，接入了 DeepSeek V4，用了一个月觉得不错。有一天遇到一个特别复杂的架构设计问题，DeepSeek V4 搞不定，想切回 Claude 官方的 Opus 模型试试。

传统做法：

1. 打开终端
2. 重新设置一串环境变量（`ANTHROPIC_BASE_URL`、`ANTHROPIC_AUTH_TOKEN`、`ANTHROPIC_MODEL`……）
3. 重启 Claude Code
4. 测试一下切对了没
5. 用完了再改回去

这一套流程，熟手也要两三分钟，生手容易改错一个变量就报错。而且你还得记住：上次 DeepSeek 的 API Key 是什么、Base URL 填的哪个、Sonnet 和 Haiku 分别映射到哪个模型……

更麻烦的是"改不回去"的问题。很多人切到 Opus 用完之后，忘了改回 DeepSeek，第二天继续用的时候才发现——账单上多扣了好几美金。还有人把 `ANTHROPIC_BASE_URL` 的值拼错了一个字母，报了一堆看不懂的错误，折腾半小时才发现是 URL 多了个斜杠。这类"配置错误"在使用多个 AI 工具的人群里极其常见，不是能力问题，是手工操作天然的脆弱性——变量多了，人脑记不住，改来改去迟早出错。

**CC Switch 把这套流程变成了：点一下系统托盘图标 → 选一个配置 → 完成。**

不需要改任何文件，不需要重启终端，不需要记任何命令。所有的 API Key、Base URL、模型映射关系，都保存在 CC Switch 里，随时切换。

---

## 三、安装（3 分钟）

### macOS（推荐 Homebrew）

```bash
brew install --cask cc-switch
```

装完在启动台找到 CC Switch 打开就行。

### Windows

去 GitHub Releases 页面下载 `.msi` 安装包：

https://github.com/farion1231/cc-switch/releases

双击安装，一路下一步。

### Linux

Ubuntu / Debian：

```bash
# 下载 .deb 包后
sudo dpkg -i cc-switch_*.deb
```

也可以用 `.AppImage` 免安装版本。

---

## 四、首次配置全流程（以 DeepSeek + Claude 为例）

装好 CC Switch 之后，第一次打开会有一个引导页面，自动帮你初始化官方预设。以下是推荐的配置流程：

**第一步：添加 DeepSeek 供应商**

点击右上角「+」，进入添加供应商页面。可以看到预设供应商列表——Claude Official、DeepSeek、Zhipu GLM、MiniMax、Kimi 等，选一个就行，API 地址已经帮你填好了，只需要填 API Key：

![[AI系列/参考图片/cc-switch/add-provider-zh.png]]

选择 DeepSeek 预设后，填入你的 API Key，在模型映射里设置：

- Opus（主模型）：`deepseek-v4-pro[1m]`
- Sonnet（轻量模型）：`deepseek-v4-flash`
- Haiku（极速模型）：`deepseek-v4-flash`

保存后，CC Switch 会自动把这套配置写入 Claude Code 的环境变量——你不需要手动编辑任何文件。

**第二步：添加 Anthropic 官方供应商（备用）**

再点一次「+」→ 选择「Anthropic」预设 → 填入 Claude 官方 API Key → 保存。

现在你有两个供应商了。在系统托盘上点 CC Switch 图标，会看到两个选项，点哪个就用哪个。

**第三步：设置自动容错（可选）**

在 CC Switch 的「代理」设置里，开启 Failover 功能，把 DeepSeek 设为主、Anthropic 设为备。以后 DeepSeek 挂了，请求自动转到 Anthropic，你什么都不用做。

**第四步：开启轻量模式**

在设置里打开「轻量模式」，CC Switch 的主窗口会关闭，只留一个系统托盘小图标。日常使用完全够用，需要改配置时点图标打开就行。

整套流程大约 5 分钟，一次配好，长期受益。

---

## 五、核心功能详解

### 1. 供应商管理：一键切换模型来源

这是 CC Switch 最核心的功能。

打开 CC Switch，左侧是"供应商"列表。每个供应商代表一套完整的配置——API 地址、API Key、模型映射。内置了 50 多个预设，包括：

- **Anthropic 官方**（Claude 直连）
- **DeepSeek**（国产高性价比）
- **AWS Bedrock**（企业级部署）
- **Google Vertex AI**
- **小米 MiMo**
- **智谱 GLM、Kimi、MiniMax、百度千帆**等国产模型
- 各种社区中转服务

操作步骤：

1. 点击右上角「+」新建供应商
2. 选择预设（比如 DeepSeek）或自定义
3. 填入 API Key
4. 设置模型映射（主模型用什么、轻量模型用什么）
5. 保存

以后要切换，直接在系统托盘点一下，选另一个供应商，秒切。

**一个细节：** CC Switch 支持"通用供应商"——一个配置同时应用到 Claude Code、Codex、Gemini CLI 等多个工具。不用每个工具单独配一遍。

### 2. 本地代理：自动容错

CC Switch 内置了一个本地代理服务器，作用是在你和 AI 模型之间加一层"中间人"。

这个中间人能做什么？

**自动容错（Failover）。** 你配了两个供应商——DeepSeek 为主，Anthropic 官方为备。当 DeepSeek 的 API 挂了或者限流了，CC Switch 自动把请求转到 Anthropic 官方，你完全无感。不用手动切。

**格式转换。** 不同供应商的 API 格式不完全一样——有的用 Chat Completions，有的用 Responses API，有的用自己的格式。CC Switch 的代理层负责在中间做格式转换，让 Claude Code 以为自己在跟 Anthropic 官方对话，实际上请求已经转发到了 DeepSeek。

**熔断机制。** 某个供应商连续报错，CC Switch 会自动把它"熔断"——暂时停止向它发请求，避免每次请求都等超时再失败。

对财资人来说，这些技术细节不需要深入了解。你只需要知道：**开了 CC Switch 之后，AI 工具更稳定了。**

### 3. 用量追踪：花了多少钱一目了然

CC Switch 有一个仪表盘，实时显示：

- 今天/本周/本月消耗了多少 Token
- 花了多少钱
- 每个模型的用量占比
- 请求次数和响应时间

可以按天、按周、按月查看趋势。还能自定义每个模型的单价，精确计算费用。

**对用 API 按量付费的人来说，这个功能很实用。** 不用再去 DeepSeek 后台看账单了，CC Switch 的系统托盘菜单里直接显示当前用量和余额。

### 4. MCP、提示词、Skills 统一管理

CC Switch 不只管模型配置，还能管理：

**MCP 服务器。** 如果你给 Claude Code 装了 MCP 插件（比如连接飞书、操作数据库），可以在 CC Switch 的统一面板里管理所有 MCP 配置，支持跨工具同步。

**提示词文件。** Claude Code 的 `CLAUDE.md`、Codex 的 `AGENTS.md`、Gemini 的 `GEMINI.md`——这些"员工手册"文件可以在 CC Switch 里统一编辑和同步。

**Skills。** 从 GitHub 仓库安装 Skills（技能包），自动检测更新，批量升级。

### 5. 会话管理

CC Switch 能浏览和搜索所有 AI 工具的历史会话记录。不用在终端里翻 `/resume` 了，图形界面里搜索关键词就能找到之前的对话。

---

## 六、财资人的典型用法

说几个具体场景，都不是"程序员"的需求：

**场景一：日常用 DeepSeek，关键时刻切 Claude Opus**

写资金日报脚本、整理 Excel 数据——这些用 DeepSeek V4 够了，便宜又快。但遇到复杂的财务模型设计、跨系统的架构规划——切到 Claude Opus，推理能力更强。

举个具体例子：你在用 Claude Code 维护公司的资金管理知识库，日常工作是批量校验文章格式、自动更新索引，这些任务 DeepSeek V4 每次消耗不到 ¥0.1。但某天老板要求你设计一套跨境资金池的自动化监控方案，涉及多币种汇率联动、流动性压力测试、TMS 系统对接——这种复杂架构设计，DeepSeek 偶尔会给出"看起来对但经不起推敲"的方案，Opus 的推理深度明显更稳。这时候在 CC Switch 的系统托盘上点一下，切到 Opus，问题解决了再切回来，全程不到 3 秒。

**场景二：多工具统一配置**

你同时用 Claude Code 管知识库、用 Codex 写数据分析脚本。两个工具都要接 DeepSeek V4，但配置文件各管各的。

CC Switch 的"通用供应商"功能，一套配置同时应用到两个工具。改一次 API Key，两个工具自动同步。

**场景三：控制 AI 费用**

用 API 按量付费，最怕的就是"不知道花了多少"。DeepSeek 便宜，但一天跑几十个任务累积下来也不少；Claude Opus 按 token 计费，一个复杂对话可能就几美金。没有用量追踪的时候，只能月底去各家后台查账单，数字对不上还得一条条核对。

CC Switch 的仪表盘把所有供应商的消耗汇总到一张图上。哪天花多了、哪个模型最费钱、Codex 和 Claude Code 各占多少——打开看一眼就知道。月底老板问"AI 工具花了多少钱"，截图发过去，清清楚楚。

---

## 七、和直接改环境变量比，好在哪

| 维度 | 手动改环境变量 | CC Switch |
|------|--------------|-----------|
| 切换速度 | 2-3分钟 | 2秒 |
| 出错概率 | 高（容易改错变量名） | 低（图形界面选择） |
| 多工具同步 | 手动逐个改 | 通用供应商一键同步 |
| 用量追踪 | 无 | 实时仪表盘 |
| 自动容错 | 无 | 内置 Failover 和熔断 |
| 配置备份 | 无 | 云同步（Dropbox/iCloud/WebDAV） |
| 学习成本 | 要记命令 | 点点就行 |

坦率说，如果你只用一个 AI 工具、只接一个供应商、从不切换，那确实不需要 CC Switch。但只要你有两个以上的模型或工具需要管理，CC Switch 节省的时间和避免的错误，远超它的安装成本。

---

## 八、注意事项

1. **CC Switch 不提供 AI 模型本身。** 它只是管理配置，你仍然需要自己注册各供应商的账号、获取 API Key。

2. **API Key 要妥善保管。** CC Switch 把 Key 存在本地 SQLite 数据库里（`~/.cc-switch/`），不会上传到任何服务器。但如果你开了云同步功能，Key 会跟着同步到你的云盘——注意云盘的安全性。

3. **轻量模式。** 如果不想看到桌面窗口，CC Switch 支持"轻量模式"——只在系统托盘显示一个小图标，占用资源极小。日常使用推荐开这个模式。

4. **自动更新。** CC Switch 内置自动更新功能，新版本发布后会提示升级。最近一次更新（v3.16.1，2026年6月1日）是 Codex 稳定性补丁，上一版 v3.16.0 新增了 22 个 Codex 供应商预设和 CLI 工具管理功能。更新频率大约每两周一次，迭代速度很快。

5. **云同步。** 如果你有多台电脑（比如公司一台、家里一台），CC Switch 支持通过 Dropbox、OneDrive、iCloud 或 WebDAV 同步配置。一台电脑上改了供应商，另一台自动更新，不用重复配置。

6. **Deep Link 导入。** CC Switch 支持 `ccswitch://` 协议——别人分享一个链接，点击就能一键导入供应商配置、MCP 服务器或 Skills。社区经常有人分享好用的配置方案，点一下就装好了。

---

## 写在最后

AI 工具越来越多，管理成本也在上升。

半年前，大多数人只用一个 ChatGPT 网页版。现在，Claude Code、Codex、Gemini CLI、DeepSeek、MiMo……光记住"哪个工具接了哪个模型"就已经很费劲了。

CC Switch 的价值不是"让你用更多的 AI"，而是**让用多个 AI 的管理成本降到接近零**。装一次，配好供应商，以后就是系统托盘点一下的事。

回到文章开头的问题——当你同时用好几个 AI 工具，配置怎么管？答案已经很清楚了：不要手动管。手工改环境变量这种做法，在只用一个工具、一个供应商的时候还凑合，一旦工具数量超过两个、或者需要在不同场景下切换不同模型，手工操作的出错率和时间成本就会急剧上升。CC Switch 把所有配置集中到一个图形界面里，切换只需要点一下，容错、用量追踪、MCP 管理这些原本需要自己折腾的功能也一并内置了。8.6 万颗 GitHub 星不是白给的——社区用脚投票的结果，说明这个工具确实解决了大量用户的真实痛点。如果你已经在用 Claude Code 或者打算同时使用多个 AI 编程工具，CC Switch 值得花 5 分钟装一下。

---

**相关阅读：**
- [[【AI系列·第03篇】Claude Code + DeepSeek V4：财资人的AI助手搭建指南|CC + DeepSeek 搭建指南]]
- [[【AI系列·第05篇】Claude Code + Obsidian 知识库工作流的神器|CC + Obsidian 工作流]]
- [[【AI系列·第06篇】MiMo+Claude Code：财资人最佳性价比AI方案|MiMo + CC 性价比方案]]

---

*免责声明：本文所述工具和功能基于 2026 年 5 月的公开资料，实际功能和定价可能随版本更新而变化。工具使用请遵守各平台服务条款。*
