---
title: MiMo+Claude Code：财资人最佳性价比AI方案
series: AI系列
episode: "06"
created: 2026-05-27
tags:
  - MiMo
  - ClaudeCode
  - AI工具
  - 财资数字化
---

# MiMo+Claude Code：财资人最佳性价比AI方案

文 / Parson资金研习社

---

小米的 MiMo 模型，最近太夯了。

5月27日，小米宣布 MiMo-V2.5 系列 API **永久降价**，输入价格低至 0.02 元/百万 tokens，输出价格 2 元/百万 tokens——最高降幅 99%。这不是促销，不是限时活动，是永久定价。

更重要的是：**MiMo 完美兼容 Claude Code。**

换句话说，财资人可以用极低的成本，把 Claude Code 这个"全能数字助手"跑起来。

---

## 一、MiMo 是什么

小米 MiMo 是小米自研的大语言模型，最新版本 MiMo-V2.5-Pro 已经跻身 Artificial Analysis 榜单**全球开源模型综合智能指数、Agent 指数并列第一**。

雷军在一季度业绩发布会上重申：**未来三年 AI 领域投入 600 亿元。**

对财资人来说，MiMo 的价值不在于"小米造了个大模型"，而在于三个字：**够用、便宜、稳。**

- **够用**：支持 1M 超长上下文、多模态理解、工具调用、Agent 模式——Claude Code 需要的能力它都有
- **便宜**：同等任务量，成本只有主流模型的 1/3 到 1/10
- **稳**：小米自研推理集群，基于 SGLang HiCache 优化，数据搬运量压缩至 1/7，缓存 Token 提升至 5 倍

---

## 二、价格到底有多便宜

直接上对比表：

| 指标 | DeepSeek V4-Pro | 小米 MiMo-V2.5 | 差距 |
|------|----------------|-----------------|------|
| 输入（缓存命中） | 0.025 元/百万 tokens | 0.02 元/百万 tokens | MiMo 便宜 20% |
| 输入（未命中） | 3 元/百万 tokens | 1 元/百万 tokens | MiMo 便宜 67% |
| 输出价格 | 6 元/百万 tokens | 2 元/百万 tokens | MiMo 便宜 67% |

MiMo-V2.5-Pro 的定价：

| 模型 | 输入（缓存命中） | 输入（未命中） | 输出 |
|------|----------------|---------------|------|
| MiMo-V2.5 Pro | 0.025 元/百万 tokens | 2 元/百万 tokens | 6 元/百万 tokens |
| MiMo-V2.5 | 0.02 元/百万 tokens | 1 元/百万 tokens | 2 元/百万 tokens |

如果选择 Token Plan 订阅套餐（固定月费），用量还能再提升 5-8 倍：

- **39 元 Lite 套餐**：约可跑 5 亿 tokens
- **99 元 Standard 套餐**：约可跑 13 亿 tokens
- **329 元 Pro 套餐**：约可跑 47 亿 tokens

在 Claude Code 的 Agent 和 Code 场景下，由于缓存命中率更高，实际可用 Token 数量还会更多。

**一句话总结：99 元/月，够一个财资人日常高强度使用 Claude Code。**

---

## 三、为什么说 MiMo + Claude Code 是最佳性价比

Claude Code 是 Anthropic 推出的 AI 编程助手，但它远不只是"写代码的工具"。对财资人来说，它更像一个**全能数字员工**——能读文件、处理 Excel、写报告、搭看板、管知识库。

问题是：**Claude Code 默认用 Anthropic 的 Claude 模型，价格不便宜。**

而 MiMo 官方提供了**原生 Anthropic 兼容协议**，也就是说：Claude Code 以为自己在用 Claude，实际上跑的是 MiMo。切换成本为零，使用体验几乎一致。

这意味着什么？

| 方案 | 月成本（估算） | 适用场景 |
|------|--------------|---------|
| Claude Code + Claude Sonnet | 约 500-2000 元 | 高性能要求、复杂推理 |
| Claude Code + DeepSeek V4 | 约 100-300 元 | 性价比方案 |
| **Claude Code + MiMo V2.5** | **约 39-99 元** | **极致性价比，日常够用** |

对于大多数财资人的日常需求——批量处理数据、生成报告模板、维护知识库、搭建自动化流程——MiMo 完全够用。省下来的钱，可以多买几杯咖啡。

---

## 四、5 分钟接入 Claude Code（附完整配置）

### 第一步：安装 Claude Code

打开终端，执行：

```
npm install -g @anthropic-ai/claude-code
```

验证安装：

```
claude --version
```

看到版本号即可。

### 第二步：注册 MiMo API Key

前往 MiMo 开放平台：https://platform.xiaomimimo.com

注册后，在「API Keys」页面创建一个 Key，格式为 `sk-xxxxx`。

如果选择 Token Plan 订阅套餐，在「订阅管理」页面获取专属 Key，格式为 `tp-xxxxx`。

### 第三步：配置 Claude Code

编辑配置文件 `~/.claude/settings.json`，写入：

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.xiaomimimo.com/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "sk-你的API密钥",
    "ANTHROPIC_MODEL": "mimo-v2.5-pro",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "mimo-v2.5-pro",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "mimo-v2.5-pro",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "mimo-v2.5-pro"
  }
}
```

如果使用 Token Plan 订阅套餐，BASE_URL 改为：

```
https://token-plan-cn.xiaomimimo.com/anthropic
```

如果需要 1M 超长上下文，模型 ID 后加 `[1m]` 后缀：`mimo-v2.5-pro[1m]`

### 第四步：启动使用

重新打开终端，进入项目目录，执行：

```
claude
```

首次启动选择「信任此文件夹」，然后用 `/status` 命令确认配置。

**配置完成，开始干活。**

---

## 五、财资人能用它干什么

接入 MiMo 的 Claude Code，和原版 Claude Code 的能力完全一致。以下是几个财资人高频场景：

**场景 1：批量处理资金日报**

> "把这 50 个子公司的资金日报 Excel 汇总成一张集团级报表，按币种分类，异常值标红。"

Claude Code 直接在文件夹里操作，改完原地保存，不需要手动上传下载。

**场景 2：自动生成报告模板**

> "根据这份资金周报的格式，帮我生成 12 个月的月报模板，每月自动填入对应的数据。"

**场景 3：搭建知识库**

> "把这个文件夹里的 200 篇制度文档建立索引，按主题分类，支持自然语言搜索。"

**场景 4：汇率数据处理**

> "从 SAP 导出的这份应收账款明细，帮我按币种汇总，自动折算为美元，生成逾期账龄分析表。"

这些任务，用 MiMo 跑 Claude Code，月成本不超过 100 元。

---

## 六、注意事项

**1. MiMo 不等于 Claude**

MiMo-V2.5-Pro 虽然综合能力很强，但在极其复杂的推理任务上，和 Claude Sonnet/Opus 还有差距。如果任务涉及复杂的多步推理、长链逻辑判断，建议用 Claude 原版模型。日常的数据处理、文档生成、知识库维护，MiMo 完全够用。

**2. 缓存命中率是关键**

MiMo 的低价优势在 Agent 和 Code 场景下尤为明显，因为这类场景的缓存命中率很高。如果只是偶尔问一两个问题，按量付费更划算；如果是日常高频使用，Token Plan 套餐更划算。

**3. 配置前清理旧环境变量**

如果之前配置过 Anthropic 官方的环境变量（`ANTHROPIC_AUTH_TOKEN`、`ANTHROPIC_BASE_URL`），需要先清除，否则会冲突。

**4. Windows 用户注意**

Claude Code 在 Windows 上需要通过 WSL 或 Git Bash 运行，不支持原生 PowerShell。具体安装方式参考微软官方文档。

---

## 七、总结

| 项目 | 内容 |
|------|------|
| 模型 | 小米 MiMo-V2.5-Pro |
| 能力 | 全球开源模型并列第一，支持 1M 上下文、Agent、工具调用 |
| 价格 | 输入 0.025 元/百万 tokens，输出 6 元/百万 tokens |
| 套餐 | 39 元/月起，最多可跑 5 亿 tokens |
| 接入 | 原生兼容 Claude Code，5 分钟配置完成 |
| 适用 | 数据处理、报告生成、知识库维护、自动化流程 |

小米这波降价，不是营销噱头——背后是推理效率的技术突破。对财资人来说，这意味着：**用一杯咖啡的钱，跑一个月的 AI 助手。**

这性价比，太夯了。

---


*免责声明：本文所述工具和方法基于公开资料和个人实践整理，仅供学习交流。工具版本、功能以各官方最新发布为准。*
**相关阅读：**
- [[【AI系列·第03篇】Claude Code + DeepSeek V4：财资人的AI助手搭建指南|Claude Code + DeepSeek V4 搭建指南]]
- [[【AI系列·第05篇】Claude Code + Obsidian 知识库工作流的神器|Claude Code + Obsidian 知识库工作流]]
- [[【AI系列·第04篇】资金人如何快速使用WorkBuddy|资金人如何快速使用WorkBuddy]]