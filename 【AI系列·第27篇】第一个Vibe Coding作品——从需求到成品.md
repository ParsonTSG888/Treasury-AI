---
title: "第一个Vibe Coding作品——从需求到成品"
series: AI系列
episode: "27"
created: 2026-06-02
tags:
  - Vibe Coding
  - Claude Code
  - 实操
  - 资金工具
---

# 【AI系列·第27篇】第一个Vibe Coding作品——从需求到成品

文 / Parson资金研习社

---

前两篇讲了Vibe Coding的概念和工具选型。这一篇，动手做。

选一个资金人最头疼的重复性工作——**每日资金余额汇总**——从零到能用，手把手走一遍。

---

## 一、需求场景

假设你是一家集团企业的资金经理，集团旗下有10家子公司，每家子公司在不同的银行有账户。每天你需要：

1. 收集10家子公司的资金余额（从银行系统导出的Excel）
2. 汇总到一张总表里
3. 计算集团总余额、各币种余额、较昨日变动
4. 生成一份简报，发给财务总监

现在这个流程是手动的：打开10个Excel → 复制粘贴到总表 → 手动计算 → 写邮件。每天花30-40分钟。

**目标：** 用Vibe Coding做一个Python脚本，自动完成以上所有步骤。输入是10个子公司的Excel文件，输出是一份汇总表和一份简报。

---

## 二、第一步：描述需求

打开Claude Code，用自然语言描述你的需求：

```
我需要一个Python脚本，功能如下：

1. 读取"input"文件夹里的所有Excel文件（每个文件是一个子公司的资金余额表）
2. 提取每个文件中的：子公司名称、币种、账户余额
3. 汇总到一张总表，包含：
   - 各子公司各币种的余额
   - 集团总余额（按币种汇总）
   - 较昨日变动（如果有昨日数据的话）
4. 输出两个文件：
   - "汇总表.xlsx"：完整的汇总数据
   - "简报.txt"：一段文字简报，包含集团总余额、主要变动、异常提醒

数据格式示例：
- 文件名：子公司A_20260602.xlsx
- 表头：账户名称、币种、余额
- 示例数据：招商银行主账户、CNY、12580000

请生成完整的Python脚本，我直接运行。
```

---

## 三、第二步：AI生成代码

Claude Code会生成一个完整的Python脚本。大概长这样：

```python
import pandas as pd
import os
from datetime import datetime, timedelta

def read_subcompany_data(input_folder):
    """读取所有子公司的Excel文件"""
    all_data = []
    for filename in os.listdir(input_folder):
        if filename.endswith('.xlsx'):
            filepath = os.path.join(input_folder, filename)
            # 从文件名提取子公司名称和日期
            parts = filename.replace('.xlsx', '').split('_')
            subcompany = parts[0]
            date = parts[1] if len(parts) > 1 else datetime.now().strftime('%Y%m%d')
            
            df = pd.read_excel(filepath)
            df['子公司'] = subcompany
            df['日期'] = date
            all_data.append(df)
    
    return pd.concat(all_data, ignore_index=True)

def generate_summary(data):
    """生成汇总表"""
    # 按子公司和币种汇总
    summary = data.groupby(['子公司', '币种'])['余额'].sum().reset_index()
    
    # 计算集团总余额
    total_by_currency = data.groupby('币种')['余额'].sum().reset_index()
    total_by_currency['子公司'] = '集团合计'
    
    summary = pd.concat([summary, total_by_currency], ignore_index=True)
    return summary

def generate_briefing(summary, yesterday_summary=None):
    """生成文字简报"""
    briefing = []
    briefing.append(f"资金日报简报 - {datetime.now().strftime('%Y年%m月%d日')}")
    briefing.append("=" * 40)
    
    # 集团总余额
    total = summary[summary['子公司'] == '集团合计']
    for _, row in total.iterrows():
        briefing.append(f"集团{row['币种']}总余额：{row['余额']:,.2f}")
    
    # 较昨日变动
    if yesterday_summary is not None:
        briefing.append("\n较昨日变动：")
        # 这里可以添加变动计算逻辑
    
    # 异常提醒（余额低于阈值）
    briefing.append("\n异常提醒：")
    threshold = 1000000  # 100万
    low_balance = summary[(summary['余额'] < threshold) & (summary['子公司'] != '集团合计')]
    if len(low_balance) > 0:
        for _, row in low_balance.iterrows():
            briefing.append(f"⚠️ {row['子公司']} {row['币种']}余额仅{row['余额']:,.2f}，低于预警线")
    else:
        briefing.append("无异常")
    
    return '\n'.join(briefing)

def main():
    input_folder = 'input'
    output_folder = 'output'
    
    # 创建输出文件夹
    os.makedirs(output_folder, exist_ok=True)
    
    # 读取数据
    data = read_subcompany_data(input_folder)
    
    # 生成汇总表
    summary = generate_summary(data)
    summary.to_excel(os.path.join(output_folder, '汇总表.xlsx'), index=False)
    
    # 生成简报
    briefing = generate_briefing(summary)
    with open(os.path.join(output_folder, '简报.txt'), 'w', encoding='utf-8') as f:
        f.write(briefing)
    
    print(f"处理完成！共处理{len(data)}条记录")
    print(f"汇总表已保存到：{output_folder}/汇总表.xlsx")
    print(f"简报已保存到：{output_folder}/简报.txt")

if __name__ == '__main__':
    main()
```

---

## 四、第三步：测试和调整

AI生成的代码不一定一次就完美。你需要测试和调整。

### 测试步骤

**1. 准备测试数据**

在`input`文件夹里放几个测试用的Excel文件：

```
input/
├── 子公司A_20260602.xlsx
├── 子公司B_20260602.xlsx
└── 子公司C_20260602.xlsx
```

每个文件的格式：
| 账户名称 | 币种 | 余额 |
|---------|------|------|
| 招商银行主账户 | CNY | 12580000 |
| 中国银行外币账户 | USD | 500000 |

**2. 运行脚本**

```bash
python daily_summary.py
```

**3. 检查输出**

看`output`文件夹里的`汇总表.xlsx`和`简报.txt`是否符合预期。

### 常见问题和调整

**问题一：文件名格式不匹配**

你的Excel文件可能不叫"子公司A_20260602.xlsx"，而是"资金余额表_子公司A.xlsx"。告诉AI：

```
文件名格式改成"资金余额表_子公司A.xlsx"，子公司名称在下划线后面。
```

**问题二：表头不一致**

不同子公司的Excel表头可能不一样。告诉AI：

```
表头可能是"余额"、"账户余额"、"Balance"，请兼容这几种格式。
```

**问题三：需要增加功能**

比如你想增加"按银行汇总"的功能。告诉AI：

```
在汇总表里增加一个维度：按银行汇总。从账户名称里提取银行名称（比如"招商银行主账户"提取"招商银行"）。
```

**问题四：输出格式要调整**

比如你想让简报更详细。告诉AI：

```
简报里增加每个子公司的余额明细，按余额从大到小排列。
```

每调整一次，就测试一次。通常3-5轮迭代，就能得到满意的结果。

---

## 五、第四步：实际使用

脚本跑通了，怎么用？

### 方式一：手动运行

每天把10个子公司的Excel文件放到`input`文件夹，双击运行脚本（或在终端执行`python daily_summary.py`），`output`文件夹里就会生成汇总表和简报。

### 方式二：定时运行

用系统的定时任务功能，每天自动运行：

**Mac/Linux：**
```bash
# 编辑定时任务
crontab -e

# 添加一行：每天早上8点运行
0 8 * * * cd /path/to/script && python daily_summary.py
```

**Windows：**
用"任务计划程序"，设置每天定时运行Python脚本。

### 方式三：集成到工作流

把脚本的输出——汇总表和简报——自动发送到飞书/企微群，或者自动上传到WPS知识库。

---

## 六、完整提示词模板

把上面的需求整理成一个通用的提示词模板，你可以直接复制使用：

```
我需要一个Python脚本，功能如下：

1. 读取"[文件夹路径]"里的所有Excel文件
2. 提取关键字段：[字段1]、[字段2]、[字段3]
3. 汇总计算：[汇总逻辑]
4. 输出：
   - "[输出文件1]"：[格式和内容说明]
   - "[输出文件2]"：[格式和内容说明]

数据格式示例：
- 文件名：[示例文件名]
- 表头：[表头字段]
- 示例数据：[示例数据]

特殊要求：
- [特殊需求1]
- [特殊需求2]

请生成完整的Python脚本，我直接运行。
```

---

## 七、从这个例子学到什么

这个例子虽然简单，但包含了Vibe Coding的核心流程：

**1. 需求描述要具体。** 不要说"帮我做一个汇总工具"，要说"读取10个Excel文件，提取子公司名称、币种、余额，汇总到总表，输出汇总表和简报"。越具体，AI生成的代码越准确。

**2. 测试数据要准备。** 不要等AI生成代码后才去找数据。先准备几个测试用的Excel文件，格式和真实数据一致，这样测试效率最高。

**3. 迭代是正常的。** 第一次生成的代码90%的情况下需要调整。不要期望"一次完美"，而是"快速迭代"。每轮调整后立即测试，看到效果再决定下一步。

**4. 从简单开始。** 这个例子只做了"汇总"功能。等跑顺了，再逐步增加"较昨日变动"、"异常提醒"、"自动发送"等功能。不要一开始就追求"大而全"。

---

## 写在最后

第一个Vibe Coding作品，不需要多复杂。一个"每日汇总"脚本，解决的是资金人每天最头疼的重复性工作。

做完这个，你会发现：**原来"写代码"没那么难。** 你不需要学Python语法，不需要懂pandas库，你只需要把业务逻辑说清楚，AI帮你翻译成代码。

这就是Vibe Coding的魅力：**让业务人员自己做工具，而不是等IT排期。**

下一篇，进阶——做一个完整的资金管理系统，包含数据库、Web界面、多用户权限。

---

**相关阅读：**
- [[【AI系列·第25篇】Vibe Coding是什么——资金人为什么需要它|Vibe Coding概念]]
- [[【AI系列·第26篇】Vibe Coding工具选型——CC vs Cursor vs Windsurf|工具选型]]
- [[【AI系列·第14篇】AI做资金日报——从2小时到15分钟的全流程实操|资金日报]]

---

*免责声明：本文所述代码示例基于 2026 年 6 月的 Python 和 pandas 版本，实际运行环境可能有所不同。代码中的数据格式和业务逻辑请根据实际情况调整。*
