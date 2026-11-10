---
tags:
  - 术
---
## Style Settings插件完整逐项详解
### 1. 欢迎信息 — Welcome!

|原文|中文翻译|
|---|---|
|Welcome! Use these settings to adjust basic extra settings or various extra elements of the theme.|欢迎！使用这些设置来调整主题的基础选项和各种额外元素。|

纯信息展示，没有可操作的控件。

---

### 2. Editor（编辑器）— 折叠后显示

**2.1 Folders（文件夹）**

|原文|中文|类型|说明|
|---|---|---|---|
|Enable highlighted active folders|启用高亮当前活动文件夹|开关|打开后，当前选中的文件夹会有背景高亮|

**2.2 Line Length（行宽）**

| 原文               | 中文   | 类型   | 默认值    | 范围                           |
| ---------------- | ---- | ---- | ------ | ---------------------------- |
| Line length      | 正文行宽 | 数字输入 | 700px  | 自定义                          |
| Wide Line length | 宽版行宽 | 数字输入 | 1000px | 配合 CSS class `wide-width` 使用 |

**2.3 Line Numbers（行号）**

|原文|中文|类型|说明|
|---|---|---|---|
|Disable stacked tabs floating line numbers|禁止叠放标签页中行号浮动|开关|禁用后在叠放模式下，行号不会悬浮在内容上方，适合面板较窄的场景|

**2.4 Menu modals（菜单弹窗）**

|原文|中文|类型|默认值|说明|
|---|---|---|---|---|
|Disable menu animations|禁用菜单动画|开关|关闭|禁用弹窗（如设置面板、命令面板）的弹出动画|
|Enable menu blur|启用菜单背景模糊|开关|关闭|弹窗背后内容显示模糊效果|
|Menu blur radius|模糊半径|滑块/数字|5px|模糊程度，值越大越模糊|

**2.5 Vim（Vim 模式相关）**

| 原文                 | 中文       | 类型   | 说明                                                                                                                                                                                |
| ------------------ | -------- | ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Animate vim cursor | Vim 光标动画 | 开关   | 启用后 Vim 模式下光标移动时有动画效果                                                                                                                                                             |
| Vim cursor speed   | 光标移动速度   | 下拉菜单 | 可选：FTL（超光速）/ Light speed（光速）/ Fast（快速）/ Normal（正常）/ Slow（慢）/ Very slow（很慢）/ Snail speed（蜗牛速度）/ Molases in January（一月的糖浆）/ 0 degrees kelvin（绝对零度）/ 课堂时钟秒针速度 / Zeno's paradox（芝诺悖论） |

_后面几个速度选项是作者的幽默命名，实际对应 30ms ~ 60s 不等_

---

### 3. Elements（元素）— 折叠后显示

**3.1 Canvas（画布）**

|原文|中文|类型|默认值|说明|
|---|---|---|---|---|
|Canvas - Background dot size|画布背景点大小|数字输入|0.7px|画布背景网格点的大小，超过 10px 会变形|

**3.2 Headers（标题）— H1~H6 各自独立**

对 H1~H6 每个级别都有两套设置：

|原文|中文|类型|默认值|说明|
|---|---|---|---|---|
|Headers - h1 underline|标题1 下划线|开关|关闭|给标题添加底部下划线|
|Headers - h1 underline style|下划线样式|下拉菜单|solid（实线）|可选：Solid（实线）/ Dotted（点线）/ Dashed（虚线）|

_H2~H6 完全一样，只是标题级别不同_

**3.3 Image Embeds（图片嵌入）**

|原文|中文|类型|说明|
|---|---|---|---|
|Image Embeds - Enable zoom|启用点击放大|开关|点击图片可放大查看（类似 Minimal 主题）|
|Image Embeds - Disable dragging|禁止拖动|开关|放大后的图片不允许拖动（会破坏模糊效果）|
|Image Embeds - Stretch all images to fit the full width|拉伸图片占满全宽|开关|默认让所有图片撑满全宽|

**3.4 Links（链接）**

|原文|中文|类型|说明|
|---|---|---|---|
|Links - Remove external link icon|移除外部链接图标|开关|去掉外部链接前面的小图标|

**3.5 Lists（列表）**

| 原文                       | 中文     | 类型   | 默认值  | 说明                           |
| ------------------------ | ------ | ---- | ---- | ---------------------------- |
| Lists - List indent size | 列表缩进大小 | 数字输入 | 28px | 列表项的缩进宽度。默认主题用 2.25em（≈36px） |
|                          |        |      |      |                              |

**3.6 Math blocks（数学公式块）**

| 原文                            | 中文        | 类型  | 说明          |
| ----------------------------- | --------- | --- | ----------- |
| Math blocks - Disable outline | 禁用数学公式块边框 | 开关  | 移除公式块周围的轮廓线 |


**3.7 Tables（表格）**

|原文|中文|类型|默认值|说明|
|---|---|---|---|---|
|Tables - Center alignment|表格居中|开关|关闭|表格水平居中显示|
|Tables - Full width|表格全宽|开关|关闭|表格撑满全宽|
|Tables - Rounded corners|表格圆角|开关|关闭|表格四角变圆|
|Tables - Rounded corners radius|圆角半径|数字输入|8px|圆角大小|

---

### 4. Extras（额外功能）— 折叠后显示

**4.1 Background Image（背景图片）** ← **你已开启**

|原文|中文|类型|说明|
|---|---|---|---|
|Info|说明信息|纯文本|建议用外链图片；本地图片需要 CSS 片段|
|**Background Image**|**启用背景图片**|**开关 ✅**|**你已经打开**|
|Dark Mode Background URL|深色模式背景图链接|文本框|填入图片 URL，你当前已设置一张 3DM 游戏图|
|Light Mode Background URL|浅色模式背景图链接|文本框|浅色模式下的背景图|
|Background Position|背景图对齐位置|下拉菜单|Center（居中）/ Top（顶部）/ Bottom（底部）/ Left（左）/ Right（右）/ Top Left（左上）/ Top Right（右上）/ Bottom Left（左下）/ Bottom Right（右下）|

**4.2 Opacity（透明度滑块）** ← **你已调整这三个**

|原文|中文|类型|你当前的值|范围|
|---|---|---|---|---|
|**Pane Opacity**|**面板不透明度**|**滑块**|**0.95**|0~1，步长 0.05|
|**UI Opacity**|**UI 元素不透明度**|**滑块**|**0.9**|0~1，步长 0.05|
|**Tab Opacity**|**叠放标签页不透明度**|**滑块**|**0.85**|0~1，步长 0.05|

值越大越不透明（越遮住背景图），值越小越透明（越能看到背景图）。

**4.3 Fun Settings（趣味设置）**

|原文|中文|类型|说明|
|---|---|---|---|
|Pane view|面板视图模式|开关|让页面变成透明背景上的独立面板。**配合 UI Opacity 设为 0 效果最佳**|

**4.4 Stacked Pane Count（叠放面板数量）**

|原文|中文|类型|默认|说明|
|---|---|---|---|---|
|Custom Stacked Panes|自定义叠放面板|开关 ❌|关闭|你当前**未开启**|
|Pane Count|同时显示面板数|滑块|2|1~5，设置叠放标签页同时显示几个|

**4.5 Visual Parity（视觉一致性）**

|原文|中文|类型|说明|
|---|---|---|---|
|Visual Parity|启用视觉一致性|开关|让阅读模式在边距等方面更接近编辑模式的视觉效果|


