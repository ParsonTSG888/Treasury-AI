---
title: "MCP实战指南：让Claude Code连接一切"
series: AI系列
episode: "18"
created: 2026-06-02
tags:
  - MCP
  - ClaudeCode
  - AI工具
  - 飞书
  - 数据库
  - 自动化
---

【AI系列·第18篇】MCP实战指南：让Claude Code连接一切

文 / Parson资金研习社

---

前 17 篇讲了 Claude Code 怎么装、怎么接模型、怎么用 Skill、怎么用 CC Switch 管理配置。但有一个核心能力一直没展开——**MCP（Model Context Protocol）**。

第 7 篇讲 Skill 的时候提过一嘴 MCP，第 5 篇讲 Obsidian 工作流的时候也暗示过"连接外部系统"的可能性。但到底什么是 MCP、怎么装、装哪些、怎么用——没有一篇专门讲清楚。

今天用一篇讲透。

---

## 一、MCP 是什么

**一句话解释：MCP 是 Anthropic 开发的一个协议，让 Claude 能连接外部工具和数据源。**

打个比方：Claude 本身是一个很聪明的大脑，但它被关在一个房间里——只能处理你输入的文字，看不到你的文件系统、连不上你的飞书、查不了你的数据库。MCP 就是给这个房间装上了门和窗户。

![MCP 架构图：Claude Code 如何连接一切](../参考图片/mcp-architecture.png)

通过 MCP，Claude 可以：
- 读写你的飞书文档
- 查询数据库
- 操作浏览器
- 调用企业内部 API
- 管理你的文件系统

**MCP 的核心价值：**

| 价值 | 说明 |
|------|------|
| **标准化** | 统一的协议，不用为每个工具写适配代码 |
| **安全** | 权限可控，数据不离开本地 |
| **可扩展** | 社区有大量现成的 MCP Server 可以直接用 |

---

## 二、为什么需要 MCP

### 没有 MCP 的痛点

以前的做法：

1. 在飞书里找到数据
2. 复制到本地文件
3. 让 Claude 分析
4. 把结果复制回飞书

效率低，容易出错。尤其是跨系统操作——你在 TMS 里导出资金流水，在 Excel 里整理格式，再粘贴到飞书文档里写日报。每一步都是手动的，每一步都可能出错。

### 有了 MCP 之后

现在的一句话：

> "帮我查一下飞书文档《资金日报》里今天的数据，生成分析报告"

Claude 直接通过 MCP 读取飞书 → 分析 → 写入新文档。全自动，零手动。

### 典型应用场景

| 场景 | 没有 MCP | 有 MCP |
|------|----------|--------|
| 读取飞书文档 | 手动复制 | 一句话读取 |
| 查询数据库 | 写 SQL → 导出 → 分析 | 直接查询分析 |
| 操作浏览器 | 手动点击 | 自动化操作 |
| 管理文件 | 命令行操作 | 自然语言指令 |
| 调用内部 API | 写代码调用 | 直接对话调用 |

---

## 三、怎么装 MCP

### 3.1 前置条件

- 已安装 Claude Code（参考第 3 篇）
- 已配置好模型（参考第 6 篇 MiMo 方案或第 3 篇 DeepSeek 方案）
- Node.js 18+（MCP Server 通常用 Node.js 编写）

### 3.2 安装 MCP Server 的通用步骤

**步骤 1：找到你需要的 MCP Server**

社区维护了一个 MCP Server 目录：
```
https://github.com/modelcontextprotocol/servers
```

或者直接搜索："mcp server + 你需要的功能"

**步骤 2：安装 MCP Server**

大多数 MCP Server 可以通过 npm 安装：
```bash
# 示例：安装文件系统 MCP Server
npm install -g @modelcontextprotocol/server-filesystem
```

**步骤 3：配置 Claude Code**

使用 `claude mcp add` 命令添加 MCP Server：

```bash
# 添加文件系统 MCP Server
claude mcp add filesystem -- npx -y @modelcontextprotocol/server-filesystem /Users/你的用户名

# 添加飞书 MCP Server
claude mcp add feishu -- npx -y @anthropic-ai/feishu-mcp-server

# 添加浏览器 MCP Server
claude mcp add browser -- npx -y @agent-infra/mcp-server-browser
```

也可以在项目根目录创建 `.mcp.json` 文件：

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/directory"]
    }
  }
}
```

**步骤 4：重启 Claude Code**

```bash
# 退出当前会话
exit

# 重新启动
claude
```

**步骤 5：验证安装**

在 Claude Code 中输入：
```
/mcp
```
应该能看到已安装的 MCP Server 列表，状态显示 "Connected"。

---

## 四、装哪些 MCP Server

### 4.1 必装推荐（Top 5）

#### 1. 文件系统 MCP Server

**用途：** 让 Claude 读写本地文件

```bash
claude mcp add filesystem -- npx -y @modelcontextprotocol/server-filesystem /Users/你的用户名
```

**使用场景：**
- "帮我整理下载文件夹里的 PDF"
- "把这个目录下的所有 MD 文件备份到外置硬盘"
- "批量重命名这些文件"

#### 2. 飞书 MCP Server

**用途：** 连接飞书，读写文档、表格、消息

```bash
claude mcp add feishu -- npx -y @anthropic-ai/feishu-mcp-server
```

需要配置环境变量：
```bash
claude mcp add feishu -e FEISHU_APP_ID=你的飞书应用ID -e FEISHU_APP_SECRET=你的飞书应用密钥 -- npx -y @anthropic-ai/feishu-mcp-server
```

**使用场景：**
- "帮我读取飞书文档《资金日报》的数据"
- "把分析结果写入飞书表格"
- "在飞书群里发一条消息"

#### 3. 浏览器 MCP Server

**用途：** 让 Claude 操作浏览器，自动化网页任务

```bash
claude mcp add browser -- npx -y @agent-infra/mcp-server-browser
```

**使用场景：**
- "帮我登录 XX 系统，导出本月数据"
- "自动填写这个表单"
- "截图这个网页"

#### 4. 数据库 MCP Server

**用途：** 连接 MySQL、PostgreSQL、SQLite 等数据库

```bash
claude mcp add postgres -- npx -y @modelcontextprotocol/server-postgres "postgresql://user:pass@localhost:5432/dbname"
```

**使用场景：**
- "查询本月所有资金流水"
- "生成资金余额表"
- "分析付款趋势"

#### 5. GitHub MCP Server

**用途：** 管理 GitHub 仓库、Issues、PR

```bash
claude mcp add github -e GITHUB_TOKEN=你的GitHubToken -- npx -y @anthropic-ai/github-mcp-server
```

**使用场景：**
- "帮我创建一个 Issue"
- "查看这个 PR 的评论"
- "合并这个分支"

### 4.2 进阶推荐

| MCP Server | 用途 | 安装命令 |
|------------|------|----------|
| **Slack** | 连接 Slack 工作区 | `claude mcp add slack -- npx -y @anthropic-ai/slack-mcp-server` |
| **Google Drive** | 读写 Google 文档 | `claude mcp add gdrive -- npx -y @anthropic-ai/google-drive-mcp-server` |
| **Notion** | 连接 Notion 数据库 | `claude mcp add notion -- npx -y @anthropic-ai/notion-mcp-server` |
| **Jira** | 管理 Jira 任务 | `claude mcp add jira -- npx -y @anthropic-ai/jira-mcp-server` |
| **Sentry** | 查看错误监控 | `claude mcp add sentry -- npx -y @anthropic-ai/sentry-mcp-server` |

---

## 五、实战演示

### 场景 1：用 MCP 读取飞书文档并分析

**需求：** 读取飞书文档《2026年5月资金日报》，生成分析报告

**步骤：**

1. 确保飞书 MCP Server 已配置并启动

2. 在 Claude Code 中输入：
```
帮我读取飞书文档《2026年5月资金日报》，分析本月资金流动情况，生成一份分析报告
```

3. Claude 会自动：
   - 通过 MCP 连接飞书
   - 读取文档内容
   - 分析数据
   - 生成报告
   - 询问是否写入新文档

### 场景 2：用 MCP 查询数据库

**需求：** 查询本月所有超过 100 万的付款记录

**步骤：**

1. 确保数据库 MCP Server 已配置

2. 在 Claude Code 中输入：
```
查询数据库中本月所有金额超过 100 万的付款记录，按日期排序，生成 Excel 报表
```

3. Claude 会自动：
   - 连接数据库
   - 执行 SQL 查询
   - 格式化数据
   - 生成 Excel 文件

### 场景 3：用 MCP 自动化浏览器操作

**需求：** 自动登录企业 OA 系统，导出本月考勤数据

**步骤：**

1. 确保浏览器 MCP Server 已配置

2. 在 Claude Code 中输入：
```
帮我登录 OA 系统（地址：https://oa.company.com），导出本月考勤数据到桌面
```

3. Claude 会自动：
   - 打开浏览器
   - 输入账号密码（需要提前配置）
   - 导航到考勤页面
   - 导出数据
   - 保存到桌面

---

## 六、常见问题

### Q1：MCP Server 安装后没反应？

**检查清单：**
1. 运行 `claude mcp list` 查看状态
2. 如果显示 "Disconnected"，尝试重启 Claude Code
3. 手动在终端运行 MCP Server 命令，看有没有报错
4. 运行 `claude --mcp-debug` 查看详细日志

### Q2：MCP Server 连接失败？

**常见原因：**
- 环境变量未配置（如飞书 App ID）
- 网络问题（某些 Server 需要代理）
- 权限不足（某些操作需要管理员权限）

**解决方法：**
```bash
# 检查环境变量
echo $FEISHU_APP_ID

# 检查网络
ping github.com

# 检查 MCP Server 状态
claude mcp list
```

### Q3：MCP Server 安全吗？

**安全机制：**
- MCP Server 运行在本地，数据不上传
- 权限可控，可以限制访问范围
- 可以随时禁用或卸载

**建议：**
- 只安装官方或可信的 MCP Server
- 不要在 MCP Server 中存储敏感信息
- 定期检查已安装的 MCP Server

### Q4：MCP Server 占用资源吗？

**资源占用：**
- 每个 MCP Server 会占用一定的内存和 CPU
- 建议只安装常用的，避免过多

**优化建议：**
- 不用时可以临时禁用：`claude mcp remove <name>`
- 选择轻量级的 MCP Server
- 定期清理不用的配置

---

## 七、总结

### MCP 的核心价值

1. **连接一切**：让 Claude 能访问你的所有工具和数据
2. **标准化**：统一的协议，不用为每个工具写适配代码
3. **安全可控**：权限可控，数据不离开本地
4. **可扩展**：社区有大量现成的 MCP Server

### 下一步行动

1. **立即尝试**：安装文件系统 MCP Server，体验基础功能
2. **按需扩展**：根据工作需要，逐步安装其他 MCP Server
3. **探索社区**：关注 MCP Server 目录，发现更多实用工具
4. **分享经验**：把你的 MCP 使用心得分享给团队

---

**相关阅读：**
- [[【AI系列·第03篇】Claude Code + DeepSeek V4：财资人的AI助手搭建指南|CC + DeepSeek 搭建指南]]
- [[【AI系列·第05篇】Claude Code + Obsidian 知识库工作流的神器|CC + Obsidian 工作流]]
- [[【AI系列·第07篇】Skill：让AI从"能聊天"到"能干活"的关键一步|Skill 关键一步]]
- [[【AI系列·第17篇】CC Switch：一个工具管住所有AI助手|CC Switch 管理工具]]

---

*免责声明：本文所述工具和功能基于 2026 年 6 月的公开资料，实际功能和定价可能随版本更新而变化。工具使用请遵守各平台服务条款。*
