---
title: "AI搭资金监控看板——从零到实时大屏"
series: AI系列
episode: "23"
created: 2026-06-02
tags:
  - 数据可视化
  - ECharts
  - 资金监控
  - 看板
  - 实战
---

【AI系列·第23篇】AI搭资金监控看板——从零到实时大屏

文 / Parson资金研习社

---

上一篇，用 ECharts 生成了单个图表——柱状图、折线图、饼图。

但单个图表是"散装"的。你给 CFO 看一张柱状图，他说"不错"。你再给他看一张饼图，他说"也行"。你再打开第三张折线图，他开始看手机了。

**CFO 需要的不是一张图，是一面墙。**

打开一个页面，所有子公司的资金状态、头寸趋势、授信使用率、预警信息一目了然。不用翻页、不用切换、不用问"那个数据在哪"。

这就是**资金监控看板**——把所有关键图表放在一个屏幕上，实时更新。

![AI 搭资金监控看板：从零到实时大屏](../参考图片/financial-dashboard.png)

这一篇，用 Claude Code 从零搭建一个完整的资金监控看板。不需要前端开发经验，不需要学 React 或 Vue——一个 HTML 文件搞定。

---

## 一、看板的四个区域

一个专业的资金监控看板，通常分四个区域：

```
┌─────────────────────────────────────────────┐
│              顶部：核心指标卡片              │
│  总余额 | 今日流入 | 今日流出 | 授信使用率    │
├───────────────────┬─────────────────────────┤
│                   │                         │
│  左上：资金趋势    │  右上：子公司余额分布    │
│  （折线图）        │  （柱状图或饼图）        │
│                   │                         │
├───────────────────┼─────────────────────────┤
│                   │                         │
│  左下：授信使用    │  右下：预警信息          │
│  （仪表盘）        │  （数据表格）            │
│                   │                         │
└───────────────────┴─────────────────────────┘
```

**顶部** 是一眼就要看到的数字——今天集团还有多少钱、进了多少、出了多少、授信用了多少。

**下半部分** 是四个图表——趋势、分布、充足率、预警。

---

## 二、用 Claude Code 搭建看板

### 第一步：告诉 AI 你的需求

打开 Claude Code：

```
> 帮我搭建一个资金监控看板，用 ECharts，要求：
> 1. 顶部显示 4 个核心指标卡片：总余额、今日流入、今日流出、授信使用率
> 2. 左上：过去 30 天资金头寸折线图
> 3. 右上：各子公司余额柱状图
> 4. 左下：资金充足率仪表盘
> 5. 右下：预警信息表格（余额低于阈值的子公司）
> 6. 深色背景，适合大屏展示
> 7. 输出一个完整的 HTML 文件
```

### 第二步：AI 生成代码

Claude Code 会生成一个完整的 HTML 文件，包含：
- CSS 布局（Grid 或 Flexbox）
- 四个 ECharts 图表实例
- 核心指标卡片
- 预警表格

代码结构大概是这样：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>集团资金监控看板</title>
  <script src="https://cdn.jsdelivr.net/npm/echarts@5/dist/echarts.min.js"></script>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { 
      background: #0f1923; 
      color: #fff; 
      font-family: 'Microsoft YaHei', sans-serif;
      padding: 20px;
    }
    .header { 
      display: flex; 
      justify-content: space-between; 
      margin-bottom: 20px; 
    }
    .card { 
      background: rgba(255,255,255,0.08); 
      border-radius: 8px; 
      padding: 20px; 
      flex: 1; 
      margin: 0 10px; 
    }
    .card .value { 
      font-size: 32px; 
      font-weight: bold; 
      color: #00d4ff; 
    }
    .grid { 
      display: grid; 
      grid-template-columns: 1fr 1fr; 
      grid-template-rows: 1fr 1fr; 
      gap: 20px; 
      height: calc(100vh - 200px); 
    }
    .chart-box { 
      background: rgba(255,255,255,0.05); 
      border-radius: 8px; 
      padding: 15px; 
    }
  </style>
</head>
<body>
  <!-- 顶部指标卡片 -->
  <div class="header">
    <div class="card">
      <div class="label">总余额</div>
      <div class="value" id="totalBalance">-</div>
      <div class="unit">万元</div>
    </div>
    <div class="card">
      <div class="label">今日流入</div>
      <div class="value" id="todayInflow">-</div>
      <div class="unit">万元</div>
    </div>
    <div class="card">
      <div class="label">今日流出</div>
      <div class="value" id="todayOutflow">-</div>
      <div class="unit">万元</div>
    </div>
    <div class="card">
      <div class="label">授信使用率</div>
      <div class="value" id="creditUsage">-</div>
      <div class="unit">%</div>
    </div>
  </div>

  <!-- 四个图表区域 -->
  <div class="grid">
    <div class="chart-box" id="trendChart"></div>
    <div class="chart-box" id="barChart"></div>
    <div class="chart-box" id="gaugeChart"></div>
    <div class="chart-box" id="alertTable"></div>
  </div>

  <script>
    // 图表初始化和数据配置由 AI 自动生成
    // ...
  </script>
</body>
</html>
```

### 第三步：替换示例数据

AI 生成的代码里，数据是硬编码的示例数据。你需要替换成真实数据。

两种方式：

**方式一：手动替换（简单）**

直接在 HTML 文件里修改数据数组。适合数据变化不频繁的场景。

**方式二：从 Excel/数据库读取（进阶）**

让 AI 写一个 Python 脚本，读取 Excel 或数据库，自动生成 HTML 文件：

```
> 帮我写一个 Python 脚本，读取"子公司余额.xlsx"，
> 自动生成资金监控看板的 HTML 文件。
> 每天运行一次，输出到"dashboard.html"。
```

---

## 三、实时数据更新

静态看板已经很有用了，但如果数据能自动更新——打开浏览器就能看到最新状态——那就更完美了。

### 方案一：定时刷新（最简单）

在 HTML 文件的 `<head>` 里加一行：

```html
<meta http-equiv="refresh" content="300">
```

这行代码让浏览器每 5 分钟自动刷新一次。如果后端有一个脚本定期更新 HTML 文件里的数据，前端就能看到最新数据。

### 方案二：WebSocket 推送（进阶）

如果需要真正的"实时"——数据一变，看板立即更新——可以用 WebSocket。

```
> 帮我改造看板，用 WebSocket 连接后端服务器，
> 数据变化时自动更新图表，不需要手动刷新。
```

AI 会生成前后端代码：
- 后端：一个 Python WebSocket 服务器，监听数据库变化，推送给前端
- 前端：看板页面连接 WebSocket，收到新数据时调用 `chart.setOption()` 更新图表

### 方案三：API 轮询（折中）

每 30 秒向后端 API 请求一次最新数据：

```javascript
setInterval(function() {
  fetch('/api/fund-data')
    .then(res => res.json())
    .then(data => {
      updateCharts(data);
    });
}, 30000);
```

比 WebSocket 简单，比 meta refresh 更灵活。

---

## 四、看板美化：让 CFO 眼前一亮

### 技巧一：动态数字

顶部的指标卡片，数字不要直接显示——用"滚动计数"动画：

```javascript
function animateValue(id, start, end, duration) {
  var obj = document.getElementById(id);
  var range = end - start;
  var startTime = null;
  function step(timestamp) {
    if (!startTime) startTime = timestamp;
    var progress = Math.min((timestamp - startTime) / duration, 1);
    obj.innerText = Math.floor(progress * range + start).toLocaleString();
    if (progress < 1) requestAnimationFrame(step);
  }
  requestAnimationFrame(step);
}
```

打开页面时，数字从 0 滚动到真实值——视觉冲击力拉满。

### 技巧二：标题栏

在页面顶部加一个标题栏，显示"集团资金监控看板"和当前时间：

```html
<div class="title-bar">
  <h1>集团资金监控看板</h1>
  <div class="time" id="currentTime"></div>
</div>
```

时间每秒更新，给人大屏实时监控的感觉。

### 技巧三：预警闪烁

如果有子公司余额低于预警线，在预警表格里用闪烁的红色标记：

```css
@keyframes blink {
  0% { opacity: 1; }
  50% { opacity: 0.3; }
  100% { opacity: 1; }
}
.alert-row {
  animation: blink 1s infinite;
  color: #ff4757;
}
```

### 技巧四：响应式布局

看板在大屏显示器上好看，在笔记本上也要能用：

```css
@media (max-width: 1200px) {
  .grid {
    grid-template-columns: 1fr;
    grid-template-rows: auto;
  }
}
```

---

## 五、部署方式

看板做好了，怎么让团队看到？

### 方式一：本地文件（最简单）

直接把 HTML 文件发给同事，双击打开就能看。适合小团队、临时汇报。

### 方式二：内网服务器

把 HTML 文件放到公司内网的 Web 服务器上（比如 Nginx），同事通过内网地址访问。

```bash
# 假设你有一台内网服务器
scp dashboard.html user@192.168.1.100:/var/www/html/
```

### 方式三：WPS 知识库

上传到 WPS 知识库，生成分享链接。但 WPS 对 HTML 的渲染可能有限制，建议先测试。

### 方式四：投屏到会议室

最"震撼"的方式——把看板全屏显示在会议室的大屏幕上。

```
1. 打开看板 HTML 文件
2. 按 F11 全屏
3. 投屏到会议室的电视或投影仪
```

CFO 走进会议室，一眼看到实时资金状态——比任何 PPT 都有说服力。

---

## 六、一个完整的示例提示词

把上面所有内容串起来，下面是一个完整的提示词模板，你可以直接复制使用：

```
> 帮我搭建一个集团资金监控看板，要求：

> 【布局】
> - 深色背景（#0f1923），白色文字
> - 顶部：标题"集团资金监控看板" + 当前时间
> - 标题下方：4 个指标卡片（总余额、今日流入、今日流出、授信使用率）
> - 下方：2x2 网格，4 个图表区域

> 【图表】
> 1. 左上：过去 30 天资金头寸折线图（带预警线 1000 万）
> 2. 右上：各子公司余额柱状图（颜色区分正负变动）
> 3. 左下：资金充足率仪表盘（绿/黄/红三区间）
> 4. 右下：预警信息表格（余额低于阈值的标红闪烁）

> 【数据】
> 使用以下示例数据（我后续替换为真实数据）：
> [这里贴你的数据]

> 【技术要求】
> - 纯 HTML + ECharts，不需要后端
> - 数字显示用滚动计数动画
> - 支持响应式布局
> - 输出一个完整的 HTML 文件
```

---

## 七、从看板到决策

看板不是目的，决策才是。

一个好的资金监控看板，应该能让你在 30 秒内回答三个问题：

1. **集团今天还有多少钱？** → 顶部总余额卡片
2. **哪些子公司需要关注？** → 预警表格
3. **资金趋势是在好转还是恶化？** → 趋势折线图

如果看完看板还需要打开 Excel 再算一遍，说明看板的设计有问题——要么缺了关键指标，要么布局不合理。

**看板的设计原则：最重要的信息放最上面，最紧急的预警放最显眼的位置。**

---

**相关阅读：**
- [[【AI系列·第22篇】AI做财务图表——从数据到ECharts的全流程|ECharts 图表基础]]
- [[【AI系列·第14篇】AI做资金日报——从2小时到15分钟的全流程实操|资金日报实操]]
- [[【AI系列·第18篇】MCP——AI的万能插头，让Claude Code连通一切|MCP 概念与安装]]

---

*免责声明：本文所述 ECharts 和前端技术基于 2026 年 5 月的公开资料，实际功能可能随版本更新而变化。看板数据仅作演示用途，请替换为真实业务数据。部署到内网服务器时请遵守企业信息安全政策。*
