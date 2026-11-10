---
tags:
  - 术
---
# Tracker 数据可视化插件

## 核心观点

Tracker 是 Obsidian 的数据追踪与可视化插件，能从笔记内容中提取结构化数据，生成月历热力图、折线图、柱状图、饼图等多种图表。它让量化习惯、追踪进度变得直观可见。

---

## 详细内容

### 一、设置界面说明

打开设置面板：**设置 → 社区插件 → Tracker → 选项**

#### 1. Default folder location（默认文件夹位置）

| 项目 | 说明 |
|------|------|
| 作用 | Tracker 默认从该文件夹读取数据 |
| 当前配置 | `日志/日复盘`（库内相对路径） |
| 覆盖方式 | 在 codeblock 中用 `folder: 路径` 参数覆盖 |

**提示**：建议设为最常用的数据文件夹（如日复盘），特殊图表再单独指定路径。

#### 2. Default date format（默认日期格式）

| 项目 | 说明 |
|------|------|
| 作用 | 解析笔记文件名中的日期 |
| 当前配置 | `YYYY-MM-DD` |
| 覆盖方式 | 在 codeblock 中用 `dateFormat: 格式` 参数覆盖 |

**支持的格式符号**：
- `YYYY` - 四位年份（2026）
- `MM` - 两位月份（05）
- `DD` - 两位日期（28）
- `MMM` - 缩写月份（May）

---

### 二、已实现功能

#### ✅ 功能一：任务完成热力图（月历形式）

**场景**：追踪健身、练琴等习惯，日历上显示哪天完成、哪天未完成。

**代码示例**：

```yaml
searchType: task.done
searchTarget: 健身打卡
folder: 日志/日复盘
startDate: 2026-05-01
datasetName: 健身
month:
    mode: circle
    color: green
```

**关键参数解释**：

| 参数 | 作用 | 可选值 |
|------|------|--------|
| `searchType` | 搜索类型 | `task.done`(已完成任务), `task.notdone`(未完成任务), `task.all`(所有任务), `text`(文本搜索) |
| `searchTarget` | 搜索目标 | 任务名称（如"健身打卡"）；**task 类型不支持正则**（仅 text 支持，2026-09-02 验证） |
| `folder` | 数据文件夹 | 覆盖默认设置的路径 |
| `startDate` | 开始日期 | 图表显示的起始日期，**月历必须加这个，否则不渲染** |
| `datasetName` | 数据集名称 | 显示在图例上，替代默认的 UNTITLED0 |
| `month.mode` | 月历显示模式 | `circle`(圆点), `annotation`(标注), `fill`(填充) |
| `month.color` | 颜色 | `green`, `orange`, `red`, `blue`, `purple` 等 |

**互动原理**：
- 在日复盘的「系统迭代」中勾选 `- [x] 健身打卡`
- Tracker 自动检测到任务完成
- 月历上对应日期显示绿色圆点
- 取消勾选 → 圆点消失

---

### 三、扩展功能（待实现）

Tracker 支持多种图表类型，以下是可用但未在当前系统中使用的功能：

#### 1. 折线图（Line）- 追踪数值变化

**场景**：追踪体重、睡眠时长、学习时长等连续数值。

```yaml
searchType: text
searchTarget: '体重: (?<value>[0-9]+)'
folder: 日志/日复盘
startDate: 2026-05-01
line:
    title: 体重变化
    yAxisLabel: kg
    lineColor: '#4CAF50'
```

**数据格式**：在日志中写 `体重: 72.5`

---

#### 2. 柱状图（Bar）- 对比不同类别

**场景**：对比一周中不同学科的学习时长。

```yaml
searchType: text
searchTarget: '数学学习: (?<value>[0-9]+)分钟'
folder: 日志/周复盘
bar:
    title: 每周数学学习时长
    barColor: '#2196F3'
```

---

#### 3. 饼图（Pie）- 占比分析

**场景**：分析时间分配、任务类型占比。

```yaml
searchType: task.done
searchTarget: 健身, 练琴, 阅读
folder: 日志/日复盘
pie:
    title: 习惯完成占比
    data: '{{sum(dataset(0))}},{{sum(dataset(1))}},{{sum(dataset(2))}}'
    dataColor: '#4CAF50,#FF9800,#2196F3'
    dataName: 健身,练琴,阅读
```

---

#### 4. 摘要统计（Summary）- 数字总结

**场景**：显示总完成天数、平均值等统计信息。

```yaml
searchType: task.done
searchTarget: 健身打卡
folder: 日志/日复盘
summary:
    template: "本月健身 {{sum()::i}} 天，完成率 {{percentage()::.1f}}%"
```

**模板变量**：
- `{{sum()}}` - 总和
- `{{average()}}` - 平均值
- `{{max()}}` - 最大值
- `{{min()}}` - 最小值
- `{{percentage()}}` - 百分比
- `::i` - 整数格式
- `::.1f` - 保留1位小数

---

#### 5. 子弹图（Bullet）- 目标进度

**场景**：显示当前进度与目标的对比。

```yaml
searchType: task.done
searchTarget: 健身打卡
folder: 日志/日复盘
bullet:
    title: 健身目标
    target: 20
    targetColor: '#4CAF50'
```

---

### 四、搜索类型详解

| 搜索类型 | 用途 | 数据源 |
|----------|------|--------|
| `text` | 搜索文本内容，可提取数值 | 笔记中的任意文本 |
| `task` / `task.all` | 所有任务（完成+未完成） | `- [ ]` 和 `- [x]` 任务 |
| `task.done` | 仅已完成的任务 | `- [x]` 任务 |
| `task.notdone` | 仅未完成的任务 | `- [ ]` 任务 |

**正则提取数值**（仅 text 类型）：
```yaml
searchTarget: '睡眠: (?<value>[0-9.]+)小时'
```
在笔记中写 `睡眠: 7.5小时`，Tracker 会提取 7.5 作为数值。

---

### 五、常见问题

#### Q1: 月历不显示数据？

**检查清单**：
1. 是否加了 `startDate` 参数？**必须加**
2. 是否加了 `month.mode`？建议加 `mode: circle`
3. 数据文件夹路径是否正确？
4. 日期格式是否与文件名匹配？（如 `2026-05-28.md` 对应 `YYYY-MM-DD`）

#### Q2: 图例显示 UNTITLED0？

**解决**：加上 `datasetName: 你的名称`

#### Q3: 如何追踪多个习惯？

**方案**：每个习惯一个 codeblock，或在一个 codeblock 里用逗号分隔多个 `searchTarget`：
```yaml
searchType: task.done, task.done
searchTarget: 健身, 练琴
```

#### Q4: 数据更新后图表不刷新？

**解决**：Tracker 会缓存数据。切换一下笔记（打开别的再回来）或重启 Obsidian。

---

### 六、参考与来源

- 官方文档：https://github.com/pyrochlore/obsidian-tracker
- 中文教程：https://pkmer.cn/Pkmer-Docs/10-obsidian/obsidian社区插件/obsidian-tracker/

---

