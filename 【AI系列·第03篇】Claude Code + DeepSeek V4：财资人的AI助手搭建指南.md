---
title: Claude Code + DeepSeek V4：财资人的AI编程助手搭建指南
series: AI系列
episode: "03"
created: 2026-05-24
tags:
  - DeepSeek
  - AI工具
  - 财资数字化
  - ClaudeCode
---

# Claude Code + DeepSeek V4：财资人的AI助手搭建指南

文 / Parson资金研习社

---

最近有不少读者问：Claude Code 到底是什么？能不能用国产模型？成本多少？

坦率说，Claude Code 不是给程序员专属的玩具。对财资人来说，它更像是一个**全能数字助手**——能帮你批量处理 Excel 数据、自动写报告模板、搭建资金管理看板、甚至维护一套几千篇文章的知识库。

这篇文章做一件事：**从零开始，5 分钟装好 Claude Code，接入 DeepSeek V4，跑起来。**

不讲概念，只讲操作。

---

## 一、Claude Code 是什么

一句话：**它是一个住在终端里的 AI 员工，能读文件、写代码、跑命令、管项目。**

和 ChatGPT 网页版最大的区别在于——ChatGPT 是聊天框，你得把内容复制粘贴进去再拿出来；Claude Code 直接在你的电脑上干活，能访问本地文件，能执行操作，不需要你当"人肉搬运工"。

举个财资场景的例子：

> "帮我把这 50 个子公司的资金日报 Excel 汇总成一张集团级报表，按币种分类，异常值标红。"

在 ChatGPT 里，你需要手动上传文件、描述格式、下载结果。在 Claude Code 里，它直接在你的文件夹里操作，改完原地保存。

**对财资人的真实价值不是"写代码"，而是"自动化重复劳动"。**

---

## 二、安装 Claude Code（3 步搞定）

### Mac / Linux 用户

打开终端（macOS 按 `Cmd+空格` 搜索"终端"），粘贴一行命令：

```
curl -fsSL https://claude.ai/install.sh | bash
```

等它跑完，输入以下命令验证：

```
claude --version
```

看到版本号就装好了。

### Windows 用户

打开 PowerShell（按 `Win+X` 选择"Windows PowerShell"），粘贴：

```
irm https://claude.ai/install.ps1 | iex
```

同样等跑完后验证版本号。

### 备选方案：npm 安装

如果上面的一键脚本网络不通，也可以用 npm 安装（需要先装 Node.js 18+）：

```
npm install -g @anthropic-ai/claude-code
```

Node.js 下载地址：https://nodejs.org/zh-cn/download/

> **注意**：Windows 用户还需安装 Git for Windows（https://git-scm.com/download/win）

![[AI系列/参考图片/Claude code启动页面.png]]
这样就安装成功了
---

## 三、获取 DeepSeek API Key

Claude Code 装好之后，默认走的是 Anthropic 官方 API——但价格不便宜，Max 模式下一个复杂任务几轮下来，一两刀就没了。

**DeepSeek V4 的优势就在这里：性能对标 Claude Opus 4.6，价格只有十分之一。**

获取 API Key 只需两步：

**第一步**：打开 https://platform.deepseek.com ，注册账号

**第二步**：进入后台，点击「API Keys」→「创建 API Key」，复制保存

> **重要**：API Key 只显示一次，创建后立刻复制。丢了只能重新生成。

![[博客系列/视频/项目文件/第25期_动画版/assets/scene4.png]]

---

## 四、接入 DeepSeek V4（两种方式任选）

### 方式一：CC Switch 图形化工具（推荐）

CC Switch 是一个桌面小工具，专门管理 Claude Code 的模型配置，**不用记命令，点两下就切换**。

**下载地址**：https://github.com/farion1231/cc-switch/releases

- macOS 选 `.dmg` 文件
- Windows 选 `.msi` 文件

安装后打开，操作步骤：

1. 点击右上角「+」新建配置
2. 供应商选择「DeepSeek」
3. 粘贴刚才复制的 API Key
4. 添加两个模型：
   - 主模型：`deepseek-v4-pro[1m]`
   - 轻量模型：`deepseek-v4-flash`
5. 点击「保存」→「启动」

切换模型时，直接在 CC Switch 里选就行，不用重启终端。

### 方式二：环境变量（命令行方式）

如果你不想装额外工具，直接在终端设置环境变量也能达到同样效果。

**Mac / Linux**：

```bash
export ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
export ANTHROPIC_AUTH_TOKEN=你的DeepSeek-API-Key
export ANTHROPIC_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_OPUS_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_SONNET_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_HAIKU_MODEL=deepseek-v4-flash
export CLAUDE_CODE_SUBAGENT_MODEL=deepseek-v4-flash
export CLAUDE_CODE_EFFORT_LEVEL=max
```

**Windows PowerShell**：

```powershell
$env:ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
$env:ANTHROPIC_AUTH_TOKEN="你的DeepSeek-API-Key"
$env:ANTHROPIC_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_OPUS_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_SONNET_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_HAIKU_MODEL="deepseek-v4-flash"
$env:CLAUDE_CODE_SUBAGENT_MODEL="deepseek-v4-flash"
$env:CLAUDE_CODE_EFFORT_LEVEL="max"
```

> **提醒**：环境变量方式每次开新终端都要重新设置。长期使用建议用 CC Switch。

---

## 五、验证与启动

打开终端，直接输入：

```
claude
```

首次启动会有一个确认提示，选 yes 即可。

验证是否接入了 DeepSeek：

1. 启动后直接问："你是什么模型？"——如果回答包含 DeepSeek 就对了
2. 输入 `/status` 查看当前模型状态

**建议开启 Max 模式**——按 `Shift+Tab` 切换，推理深度和编码质量都会提升。

---

## 六、怎么用？其实就是一个对话框

看到黑乎乎的终端窗口，很多人的第一反应是"这是什么，好可怕"。

说白了，**Claude Code 的用法和微信聊天一模一样**——你打字发消息，它回复结果。唯一的区别是，它能直接操作你电脑上的文件。

### 输入：三种方式

**方式一：直接打字描述需求**

启动后光标闪烁的地方就是输入框，直接打字就行：

```
帮我把这个目录下所有 .xlsx 文件合并成一张表
```

**方式二：拖文件进去**

想让它处理某个文件？直接从文件夹把文件**拖进终端窗口**，路径会自动填上。然后跟一句话：

```
分析一下这个文件，按月份汇总收支，输出到同目录下
```

**方式三：给地址**

不想拖文件也可以直接告诉它路径：

```
读取 ~/Desktop/资金日报/ 目录下所有 CSV，生成汇总报表
```

### 输出：告诉它三件事

1. **输出什么格式**——"生成 Excel / Markdown / PDF / 图表"
2. **存在哪里**——"保存到桌面 / 当前目录 / 指定文件夹"
3. **叫什么名字**——"文件名叫 2026年5月资金汇总.xlsx"

举个完整的例子：

```
把 ~/Desktop/子公司日报/ 下所有 Excel 的余额数据汇总到一张表里，
按币种分类，异常值（超过均值2倍的）标红，
输出为 Excel，保存到桌面，文件名叫 集团资金日报汇总.xlsx
```

它就会自己去读文件、处理数据、生成表格、保存到桌面。全程不需要写一行代码。

### 常用快捷操作

| 操作 | 方法 |
|------|------|
| 切换模式（标准/Max） | 按 `Shift+Tab` |
| 查看当前模型 | 输入 `/status` |
| 清空对话重新开始 | 输入 `/clear` |
| 退出 | 按两次 `Ctrl+C` 或输入 `/exit` |
| 让它暂停思考 | 按 `Esc` |

**本质上，终端只是一个输入框。你要做的就是：描述需求 → 指定文件 → 说清输出。剩下的它来。**

---

## 七、财资人能用它做什么（实际场景）

说几个实际场景，都不是"写代码"的需求：

| 场景             | 怎么用                                |
| -------------- | ---------------------------------- |
| **批量处理 Excel** | "把这个目录下所有子公司的日报汇总成一张表，按币种分类"       |
| **自动生成报告**     | "根据这个模板和数据源，生成本月的资金分析报告"           |
| **做 PPT**      | "根据这篇文档的内容，生成一份 10 页的汇报 PPT"       |
| **做视频**        | "把这段文字脚本配上画面，生成一个 3 分钟的讲解视频"       |
| **搜网页**        | "帮我查一下最新的跨境资金池政策变化，整理成要点"          |
| **搭建管理看板**     | "帮我做一个资金监控看板，包含余额、收支、预警"           |
| **数据清洗**       | "把这个银行流水 CSV 清洗一下，去掉重复行，统一日期格式"    |
| **写校验脚本**      | "写一个 Python 脚本，检查所有子公司的资金日报是否按时提交" |

而且这些能力还在快速拓展。通过安装不同的插件（MCP），它能连接飞书、操作数据库、调用企业内部系统，几乎每天都有新玩法出来。

本质上，Claude Code 擅长的是**有规律、可描述、重复性高**的工作。财资领域的日常事务，大量属于这一类。

说白了，它不是某个特定功能的工具，而是一个**万能的操作入口**——你告诉它干什么，它就去干。能力边界取决于你敢不敢提需求。

---

## 八、费用参考

DeepSeek V4 当前定价（来源：DeepSeek API 官方文档，2026年5月）：

| 项目 | deepseek-v4-pro | deepseek-v4-flash |
|------|-----------------|-------------------|
| 百万 token 输入（缓存命中） | ¥0.025 | ¥0.02 |
| 百万 token 输入（缓存未命中） | ¥3 | ¥1 |
| 百万 token 输出 | ¥6 | ¥2 |
| 上下文长度 | 1M | 1M |
| 并发限制 | 500 | 2500 |

> **注意**：v4-pro 当前享受 2.5 折优惠（原价 ¥12/百万输入、¥24/百万输出），已宣布永久降价。
> 

对比 Claude Opus 4.6 官方定价（百万 token 输入约 ¥15、输出约 ¥75），DeepSeek v4-pro 即使恢复原价也只有 Claude 的六分之一，缓存命中时更是只有五百分之一。

实际体感：**跑两个完整的应用生成任务，消耗约 ¥1.5**。日常使用一个月几十块足够。

这就是为什么说"性价比炸了"——不是夸张，是账算得过来。

---

## 九、注意事项

1. **Claude Code 和 Claude 聊天机器人是两个东西**——前者是终端工具，后者是网页对话。这篇文章讲的是前者。

2. **API Key 要妥善保管**——不要写在代码里提交到公开仓库，不要发到群里。泄露了就去后台删掉重新生成。

3. **Max 模式一定要开**——不开的话用的是轻量模型，体验会打折扣。

4. **首次使用建议从简单任务开始**——比如让它整理一个文件夹、写一个小脚本，熟悉了再上复杂任务。

---

## 写在最后

AI 工具的迭代速度，已经快到"这个月不用，下个月就落伍"的程度。

但对财资人来说，不需要追每一个新模型。真正重要的是：**找到一两个趁手的工具，嵌入日常工作流，让它们替你干重复的活。**

Claude Code + DeepSeek V4 就是目前性价比最高的一套组合。装一次，用很久。

花 5 分钟装好，比花 5 小时手动做 Excel 汇总，值。

---

*参考来源：DeepSeek API 官方文档（api-docs.deepseek.com）、库森《Claude Code 配 DeepSeek V4 教程》（2026年4月）、Vposy《从0开始在国内用上 Claude Code》（2026年5月）*

---

**相关阅读：**
- [[Dataview仪表盘|知识库 Dataview 面板]]
- [[Skills/内容生成规范与校验清单|写作规范与校验清单]]

---

*免责声明：本文所述工具和价格信息基于 2026 年 5 月的公开资料，实际功能和定价可能随版本更新而变化。工具使用请遵守各平台服务条款。*
