---
title: Claude Code + Obsidian 知识库工作流的神器
series: AI系列
episode: "05"
created: 2026-05-26
tags:
  - 博客系列
  - Claude Code
  - Obsidian
  - 知识管理
  - LLM Wiki
---

# Claude Code + Obsidian 知识库工作流的神器

**文 | Parson资金研习社**

---

笔记工具用了十几年，从有道云笔记到 Notion 再到 Obsidian，收藏夹里躺着几千条"以后再看"——从来没再看过。

不是不想整理，是整理这件事本身就反人性。分类、打标签、建关联，每一步都需要在"创造"和"管理"之间切换心智模式。脑子已经在想下一个问题了，手还在给上一篇笔记归类。

2026 年 4 月，前特斯拉 AI 负责人、OpenAI 联合创始人 Andrej Karpathy 发了一份 Markdown 文档 `llm-wiki.md`，浏览量超过 1600 万。他提了一个观点：

> RAG 每次提问都重头搜索原始材料、拼接、生成回答，用完即弃。但 LLM 也可以把读过的素材编译成结构化的 wiki，被保留、被迭代、被交叉引用。

一句话：**AI 不是检索器，是编译器。**

顺着这个思路，把 Claude Code 接进 Obsidian——整理这件事，终于可以从任务清单上划掉了。

---

## 一、为什么偏偏是这两个组合

市面上笔记工具几十款，AI 助手也一堆。为什么单独挑 Obsidian + Claude Code？

三个字：零摩擦。

**Markdown 是大模型的母语。** GitHub 上几十亿个 README、技术文档、博客，AI 全读过。它对 `##` 标题、`**加粗**`、`- 列表` 的理解跟你对标点符号一样自然。同样的信息用 Markdown 表示比 JSON 紧凑 30%—50%，意味着同样的上下文窗口能装下更多笔记内容。

**本地文件 = AI 直接读写。** Obsidian 的 Vault 本质就是一个文件夹，里面全是 `.md` 文件。Claude Code 的工作方式是直接操作本地文件系统——读、写、搜索、移动、重命名，不需要 API、不需要认证、不需要网络请求。

两种方式接入，效果一样：

```
# 方式一：Claudian 插件（推荐）
# Obsidian 侧边栏直接聊天，不用离开笔记

# 方式二：终端命令行
cd ~/Documents/我的知识库
claude
```

Claudian 是 Obsidian 社区插件，把 Claude 直接嵌入侧边栏——左边文件夹，中间笔记，右边 AI 聊天。底层调用的就是 Claude Code，能力完全一样。

对比一下：Notion 的 AI 只能通过 API 间接操作，Confluence 需要认证和网络。而 Obsidian + Claude Code，文件就在本地，AI 直接碰。

**不是向量数据库，不是 SQL，不是 JSON。就是 `.md` 文件。**

---

## 二、Karpathy 的 LLM Wiki：一个值得细品的想法

Karpathy 管 `llm-wiki.md` 叫 idea file——一份描述想法的文件，而不是一个成品软件。

这个想法的核心可以用一句话概括：**知识库的瓶颈从来不是阅读或思考，而是交叉引用与一致性维护这类永远没人愿意做的 bookkeeping，而 LLM 恰好将这项成本压到接近零。**

过去几十年，所有笔记工具都假设"整理是你的事"。分类你来，打标签你来，发现关联你来。Karpathy 说，不对——人类专注策展与提问，LLM 承担其余一切。

### 三层架构

```
Vault/
├── raw/          # 原始素材，只增不改（人类输入）
├── wiki/         # AI 编译过的可复用知识（LLM 独占读写）
│   ├── concepts/   # 概念页：某个观点、技术、框架的解释
│   ├── entities/   # 实体页：人物、组织、产品、地点
│   └── sources/    # 摘要页：每份原始素材对应一个摘要
└── CLAUDE.md     # 告诉 LLM 如何组织与维护知识库的"员工手册"
```

关键设计原则：**源料与派生分离。** LLM 绝对不能动人类的原始材料。wiki 目录可以随时删除和重建，对人类创作和收藏的内容毫无影响。

### 三个核心操作

| 操作 | 做什么 | 打个比方 |
|------|--------|----------|
| **Ingest** | 读取新素材 → 提取关键信息 → 更新概念页/实体页/摘要 → 刷新索引 | 图书管理员收到新书，编目入库 |
| **Query** | 针对 wiki 提问 → 搜索相关页面 → 综合回答 → 有价值的回答回填 wiki | 不只是查资料，查完还把结论存档 |
| **Lint** | 周期性体检 → 发现矛盾、孤儿页、过时论断、缺失关联 | 知识库的"质量审计" |

Query 操作有个容易被忽略的细节：**有价值的回答应该回填进 wiki。** 这意味着每次探索都在给知识库增加资产，而不是用完即弃。知识从一次性消耗变成永久积累。

正如 Karpathy 说的：Obsidian 是 IDE，LLM 是程序员，wiki 是代码库。

---

## 三、30 分钟搭建：从零到能用

接入 Claude Code 有两条路，推荐程度从高到低：

| 方案 | 优点 | 适合谁 |
|------|------|--------|
| **Claudian 插件（推荐）** | 装在 Obsidian 内，侧边栏直接聊天，不用切窗口 | 所有人 |
| 终端命令行 | 纯命令行操作，灵活度最高 | 开发者、喜欢终端的人 |

### 方案一：Claudian 插件（推荐）

Claudian 是一个 Obsidian 社区插件，把 Claude 直接嵌入 Obsidian 侧边栏——左边文件夹，中间笔记，右边 AI 聊天窗口，一个界面搞定一切。

安装步骤：

1. Obsidian 设置 → 关闭安全模式
2. 社区插件 → 浏览 → 搜索"Claudian" → 安装 → 启用
3. 打开插件设置，填入 Anthropic API Key
4. 侧边栏出现聊天图标，点开就能用

Claudian 底层调用的就是 Claude Code，能力完全一样——读写文件、搜索、移动、重命名——只是不用离开 Obsidian。

### 方案二：终端命令行

如果更喜欢终端，两行命令直接上：

```
cd ~/Documents/我的知识库
claude
```

如果已经有一堆 Markdown 文件，直接拿它当 Vault。Obsidian 不会改任何文件，只是加一层 UI。

第一次启动试一句"帮我概览一下这个知识库"，作为冒烟测试。

### 第一步：写一份 CLAUDE.md 当"员工手册"

Claude Code 第一次进 Vault 像新员工第一天——满屋子文件柜，无从下手。在 Vault 根目录创建 `CLAUDE.md`，Claude Code 每次启动都会自动读取：

```markdown
# 我的知识库

## 关于我
- 关注领域：AI、效率工具、知识管理

## Vault 结构
- daily/：每日记录
- notes/：永久笔记
- raw/：待整理的原始素材

## 行为规则
- 可以：加标签、建双向链接、生成摘要、整理分类
- 不可以：删除已有笔记内容、修改原始记录
```

不需要一上来就完美。三五行起步，每次 Claude Code 犯错就加一条规则——半年下来它会自然长成一份很详尽的指南。

### 第二步：每个大目录补一份 index.md

Claude Code 进入一个文件夹，看到 50 个文件需要逐个扫描。但如果有份 3—5 行的 `index.md`，它先读这一份，3 秒钟就知道这里是干嘛的、关键文件在哪。

```markdown
# notes/ — 永久笔记

存放经过思考整理的、可被反复引用的判断。

## 关键文件
- [[认知偏差笔记]] — 卡尼曼的核心框架
- [[供应链金融模型]] — 行业分析
```

### 第三步：装 obsidian-skills

Obsidian 的 Markdown 有些独有语法——`[[wikilinks]]` 双向链接、callout 提示框、`.canvas` 空间笔记——Claude Code 默认不一定懂。

Obsidian CEO kepano（Steph Ango）亲自做了 `obsidian-skills` 这套官方 Skill 集，GitHub 已超 3.3 万星。包含 5 个技能：

| Skill | 作用 |
|-------|------|
| obsidian-markdown | 创建/编辑 Obsidian 风格 Markdown（wikilinks、embeds、callouts） |
| obsidian-bases | 操作 Obsidian Bases 数据库文件 |
| json-canvas | 操作 Canvas 画布文件 |
| obsidian-cli | 通过命令行控制 Obsidian |
| defuddle | 从网页提取干净 Markdown，节省 token |

一行命令安装：

```bash
cd ~/Documents/我的知识库
git clone https://github.com/kepano/obsidian-skills.git .claude/skills/obsidian-skills
```

从此处理 `[[]]`、callout、`.canvas`、Bases 都不会出错。

---

## 四、真实提效场景

### 场景一：散乱笔记批量整理

把备忘录、微信收藏里的乱账转成 `.md` 扔进 `raw/` 目录，然后一句话：

> "帮我整理 raw/ 下的散乱笔记。按主题分类，加 Front Matter，建双向链接，生成 index.md。"

Claude Code 会：扫描分类 → 移动到对应文件夹 → 加标题标签摘要 → 发现关联自动插入 `[[双向链接]]` → 生成目录索引。

5 分钟，几百条笔记从"信息坟场"变成有结构的知识网络。

### 场景二：日记自动织网

写日记顺手提到"今天和老王聊了塔勒布"，不主动输入 `[[老王]]`、`[[塔勒布]]`，这条日记和之前的笔记就没关系。

让 Claude Code 自动处理：读取日记 → 识别人名/书名/概念 → 搜索 Vault 是否已有对应笔记 → 有就替换成 wikilink，没有就建占位页再链接。

10 分钟的手工活变成几秒钟，而且它不会漏——可能忘了 Vault 里有某个概念页，它不会忘。

### 场景三：知识编译成 Wiki

这是 Karpathy LLM Wiki 思路的直接落地。每次读完一篇文章或做完一个项目，让 Claude Code 把"可独立成立的知识点"提炼到 wiki/：

- 提到一个新概念？→ 写进 `wiki/concepts/`
- 提到一个人物或公司？→ 写进 `wiki/entities/`
- 文章本身值得保留？→ 写进 `wiki/sources/`

下次写相关主题时它先读 wiki，不重新调研。知识从一次性消耗变成永久资产。

### 场景四：一个界面搞定一切

如果用 Claudian 插件，以上所有场景都不需要离开 Obsidian。左边文件夹，中间笔记，右边 AI 聊天——选中一段文字右键就能让 Claude 处理，处理完直接在笔记里看到结果。

不管是 Claudian 还是终端命令行，读的都是同一份 `CLAUDE.md`，用的同一套 skills，数据完全互通。

---

## 五、几条实战建议

用了一段时间后的真实体感：

**40 万字以内不需要向量数据库。** Claude 的上下文窗口够大，纯文本 wiki + index.md 导航，AI 找到相关知识的速度和准确度都很高。别急着上 RAG。

**先写后整理。** 不要花一下午设计完美分类体系，一周后发现实际笔记根本不符合预设。笔记系统得从笔记里长出来，不是从设计图纸里长出来。

**一定做的三件事：** 装 obsidian-skills、补 index.md、Front Matter 加 `type` 和 `summary` 字段。投入不超过 1 小时，日常每次操作都受益。

**不要一上来装一堆插件。** 先裸跑几周，等明确知道缺什么再加——否则只是在收集工具不在解决问题。

**善用 Obsidian CLI。** Obsidian 1.12+ 发布了命令行工具，支持搜索、任务管理、属性操作、甚至执行 JavaScript。Claude Code 可以直接调用这些命令，不需要手动打开 Obsidian 界面。

**用 Git 管理版本。** wiki 目录就是一个 Git 仓库。版本历史、分支、协作全免费。Agent 搞砸了？`git revert` 一秒回滚。这对 Agent 工作流来说至关重要。

**Claudian 还是终端？** 不纠结。两者底层都是 Claude Code，读同一份 CLAUDE.md，用同一套 skills。日常整理用 Claudian（方便），批量操作用终端（灵活）。完全可以混着用。

---

## 六、写在最后

这套体系的核心不是 Obsidian，也不是 Claude Code。

是一个认知转变：**整理这件事，不再是你的责任。**

过去几十年所有笔记工具都假设"整理是你的事"。AI 改变的是这个假设——只管记录和思考，整理交给 AI。

Karpathy 引用了范内瓦·布什 1945 年的话：所谓 memex，就是这样一种设备——个人可以将自己所有的书籍、记录和信函都存储其中，以极高的速度和灵活性进行查阅。它就像是个人记忆的一种延伸。

80 年前布什设想了这个东西，但解决不了维护成本的问题。现在 LLM 把这个成本压到了接近零。

工具会变，但 Vault 里的 Markdown 文件一直是你的。想试的话路径很短：装个 Claudian 插件，填个 API Key，选中一段文字让 Claude 帮整理——剩下的 AI 会搞定。

---

### 参考资料

1. Andrej Karpathy, llm-wiki.md, 2026. https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
2. kepano (Steph Ango), obsidian-skills, GitHub. https://github.com/kepano/obsidian-skills
3. Obsidian CLI 官方文档. https://obsidian.md/help/cli
4. Obsidian Flavored Markdown. https://obsidian.md/help/obsidian-flavored-markdown
5. Vannevar Bush, "As We May Think", The Atlantic, 1945. https://www.theatlantic.com/magazine/archive/1945/07/as-we-may-think/303881/

---

**相关阅读：**
- [[Karpathy的LLM Wiki深度拆解：从理念到落地，如何构建可进化的知识底座]]
- [[LLM Knowledge Bases]]
- [[别再手动整理笔记了：Obsidian + Claude Code 才是 2026 年效率天花板]]
- [[知识工程新篇章（Claude Code + Obsidian）]]

---

*免责声明：本文所述工具和方法基于公开资料和个人实践整理，仅供学习交流。工具版本、功能以各官方最新发布为准。*
