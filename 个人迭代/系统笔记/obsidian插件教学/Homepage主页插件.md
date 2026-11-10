---
tags:
  - 术
---
## Obsidian Homepage 插件完整逐项详解

### 一、配置文件结构与版本

你在 `data.json` 中看到的 `"version": 4` 表示插件当前的配置格式版本。插件每次更新，如果有配置结构变化，会自动升级这个版本号。

---

### 二、插件主设置页面（你在 Obsidian 里打开看到的界面）

#### 🔷 设置组一：主页文件选择（settings.ts 第 83-127 行）

**名称：Homepage（主页）**

这是插件的首要设置——让你选择**什么内容作为主页**。


**主页类型下拉菜单（Kind）**，11 种可选：

|类型英文|中文|类型|功能说明|
|---|---|---|---|
|`File`|**文件**|文本输入|指定一个具体的笔记、Canvas 画布或 Base 文件|
|`Workspace`|**工作区**|文本输入|指定一个 Obsidian 工作区布局|
|`Random file`|**随机文件**|自动|每次启动随机从整个库中选一个文件|
|`Random in folder`|**随机文件夹内**|文本输入|指定一个文件夹，每次从该文件夹内随机选|
|`New note`|**新笔记**|文本输入|每次启动创建一个带前缀的新笔记|
|`Graph view`|**图谱视图**|自动|主页显示为关系图谱|
|`Nothing`|**无**|自动|不打开任何内容（但仍可执行附加命令）|
|`Journal`|**日志**|文本输入|使用指定的 Journal 笔记|
|`Daily Note**|**每日笔记**|自动|使用当天的每日笔记|
|`Weekly Note**|**每周笔记**|自动|使用本周的周期笔记|
|`Monthly Note**|**每月笔记**|自动|使用本月的周期笔记|
|**Yearly Note**|**每年笔记**|自动|使用今年的周期笔记|

**你当前的选择：** `File`，文件为 `🏠Home.canvas`

**每种类型的详细说明（来自 locale 源码）：**

|类型|描述原文|中文说明|
|---|---|---|
|File|Enter a note, base, or canvas to use.|输入要使用的笔记、Base 或 Canvas 画布|
|Workspace|Enter an Obsidian workspace to use.|输入要使用的 Obsidian 工作区|
|New note|Enter a prefix to use. A note using that prefix will be created for each Obsidian session.|输入前缀。每次打开 Obsidian 都会创建一个以该前缀命名的新笔记|
|Graph view|Your graph view will be used.|你的图谱视图将被用作主页|
|Nothing|Nothing will occur by default. Any commands added will still take effect.|默认不执行任何操作。但已添加的命令仍然有效|
|Random file|A random note, base, or canvas from your Obsidian folder will be selected.|从整个库中随机选取笔记、Base 或 Canvas|
|Random in folder|Enter a folder. A random note, base, or canvas from it will be selected.|输入一个文件夹。从中随机选取笔记、Base 或 Canvas|
|Journal|Enter a Journal to use.|输入要使用的 Journal 名称|
|Daily/Weekly/Monthly/Yearly note|Your Periodic/Daily Note will be used.|使用对应的周期笔记|

**重要交互逻辑：**

- 某些类型（Random、Graph、None、所有周期笔记）**不可编辑**文件路径（由代码自动决定）
- 如果某类型依赖的插件未安装（如 Journal 需要 Journal 插件），该类型会被禁用并显示`"The plugin required for this homepage type isn't available."`
- 文本输入框默认值为 `Home`（即 `DEFAULT_DATA.value`）

---

#### 🔷 设置组二：主要设置（Primary Group，settings.ts 第 130-171 行）

**1. Open on startup（启动时打开）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Open on startup|`✅ 开启`|**openOnStartup**|Obsidian 启动时自动打开主页|
|警告提示|—|This will override the built-in "Default file to open" setting.|此设置会覆盖 Obsidian 自带的"默认打开文件"设置|

**源码行为：** 启动时 `plugin.loaded` 为 `false`，所以会使用 `openMode`（而不是 `manualOpenMode`）来决定如何处理已有标签页。如果关闭此选项，下面的 Vault environment 组中的 `Opening method` 会被禁用。

---

**2. Open when empty（空窗口时打开）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Open when empty|`❌ 关闭`|**openWhenEmpty**|当所有标签页都关闭时，自动重新打开主页|

**源码行为（homepage.ts 第 198-211 行）：** 当 Obsidian 处于已加载状态且不在执行中时，如果当前活动标签页的类型是 `"empty"` 且所在分栏只有一个子面板，会自动以 **保留模式（Retain）** 打开主页。

> 注意：即使 `openOnStartup` 关闭了，这个设置仍然可能触发打开主页。

---

**3. Use when opening normally（正常打开时应用）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Use when opening normally|`❌ 关闭`|**alwaysApply**|通过点击链接、文件浏览器等"普通"方式打开主页时，也应用主页的视图设置|

**源码行为（homepage.ts 第 213-227 行）：** 每当一个文件被打开时，插件都会检查打开的文件是否为当前主页文件。如果是，就会应用 `configure()` 方法里的视图设置（pin、view mode、autoScroll 等）。开启此选项相当于让这些设置在"非启动、非手动命令"的普通打开中也生效。

---

**4. Separate mobile homepage（独立移动端主页）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Separate mobile homepage|`❌ 关闭`|**separateMobile**|在手机上使用一套**独立**的主页配置文件|
|描述|—|For mobile devices, store the homepage and its settings separately.|为移动设备单独存储主页及其设置|

**交互逻辑：** 开启后，手机上看到的设置和 PC 上的设置完全分离，互不干扰。编辑 PC 设置时会有警告提示 `Mobile settings are stored separately. Therefore, changes to other settings will not affect mobile devices.`，反之亦然。

---

#### 🔷 设置组三：Commands（命令，settings.ts 第 173 行）

|设置项|英文原文|功能说明|
|---|---|---|
|Commands（命令）|Select commands that will be executed when opening the homepage.|选择在打开主页时自动执行的 Obsidian 命令|
|Add...（添加...）|—|点击后弹出一个命令选择框，可以搜索并添加任意 Obsidian 命令|

**你当前的值：** `[]`（空数组），即没有配置任何自动执行的命令。

**每条命令会记录两部分信息：**

|字段|类型|说明|
|---|---|---|
|`id`|string|命令的唯一 ID（如 `"editor:open-search"`）|
|`period`|Period 枚举|命令何时执行|

**Period 枚举有三种选择：**

|选项|中文|说明|
|---|---|---|
|`Both`|两者都|启动时和手动打开都执行 ← **默认**|
|`Startup only`|仅启动时|只在 Obsidian 启动时执行|
|`Manual only`|仅手动时|只在通过命令/按钮手动打开时执行|

**源码行为（homepage.ts 第 69-78 行）：** 当加载状态为 true 时禁用 Period.Startup，false 时禁用 Period.Manual。执行时通过 `app.commands.executeCommandById(id)` 触发。

---

#### 🔷 设置组四：Vault environment（库环境，settings.ts 第 175-183 行）

**1. Opening method（打开方式——启动时）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Opening method|`Replace all open notes`|**openMode**|决定 Obsidian **启动时**如何处理已有的标签页和视图|

**三个选项（来自 Mode 枚举）：**

|选项英文|中文|源码说明|详细行为|
|---|---|---|---|
|`Replace all open notes`|替换所有打开的笔记|启动时清除所有标签页|先解绑所有分叶，然后通过 `detachAllLeaves()` 清除，再打开主页。如果有浮动窗口也会关闭|
|`Replace last note`|替换最后一个笔记|替换最近打开的笔记为首页|若启动时已有打开的标签页但不是主页，把最近的那个替换掉|
|`Keep open notes`|保留已有笔记|保留所有现有标签页|在现有标签页基础上新打开一个主页标签页|

**你当前用的是 `Replace all open notes`**，这就是为什么你每次打开 Obsidian，之前打开的笔记都会被清空，只留主页。

**⚠️ 重要交互：** 如果 `Open on startup` 是关闭的，这个选项会被禁用（灰显）。

---

**2. Manual opening method（打开方式——手动时）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Manual opening method|`Keep open notes`|**manualOpenMode**|决定通过命令面板或功能区按钮**手动**打开主页时如何处理已有标签页|

**你当前用的是 `Keep open notes`**，这就是为什么你手动点"打开主页"按钮时，不会关闭其他笔记，只是新打开一个主页标签。

**补充：** 如果 `open()` 被传入 `alternate=true` 参数（当空窗口自动打开时），会强制使用 `Keep open notes` 模式，忽略此设置。

---

**3. Pin（固定标签页）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Pin|`❌ 关闭`|**pin**|打开主页时，将其标签页固定在标签栏上，防止误关闭|

**源码行为（homepage.ts 第 143-167 行）：** 在配置标签页（`configure()` 方法）中，如果是 Markdown 视图且设置了 pin，会执行 `view.leaf.setPinned(true)`。固定后的标签页右上角会出现一个图钉图标，关闭按钮不可用。如果是非 Markdown 视图（如 Canvas），也会同样固定。

---

**4. Hide release notes（隐藏发行说明）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Hide release notes|`❌ 关闭`|**hideReleaseNotes**|Obsidian 更新后，永远不显示发行说明弹窗|

---

**5. Auto-create（自动创建）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Auto-create|`❌ 关闭`|**autoCreate**|当指定的主页笔记还不存在时，自动创建一个空笔记|
|警告|—|If this vault is synced using unofficial services, this may lead to content being overwritten.|如果库使用了非官方同步服务（如坚果云），可能会造成内容被覆盖|

**源码行为（homepage.ts 第 120-126 行）：** 当 `launchNote()` 找不到目标文件时，如果 `autoCreate` 开启，会执行 `app.vault.create(untrimName(this.computedValue), "")` 创建一个空文件。注意创建的文件路径是 `computedValue`（不带 `.md` 后缀），插件会通过 `untrimName()` 自动补全后缀。

---

#### 🔷 设置组五：Opened view（打开的视图，settings.ts 第 185-194 行）

**1. Homepage view（主页视图模式）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Homepage view|`Default view`（默认视图）|**view**|选择主页以哪种视图模式打开|

**四个选项（来自 View 枚举）：**

|选项英文|中文|源码切换逻辑|
|---|---|---|
|`Default view`|默认视图|不做任何切换，使用 Obsidian 全局默认设置 ← **你当前用的**|
|`Reading view`|阅读视图|`state.mode = "preview"`|
|`Editing view (Source)`|编辑视图（源码）|`state.mode = "source"` + `state.source = true`|
|`Editing view (Live Preview)`|编辑视图（实时预览）|`state.mode = "source"` + `state.source = false`|

**源码行为（homepage.ts 第 156-167 行）：** 如果你选择非默认视图，插件会先获取当前 view 的 state，修改 `mode` 和 `source` 属性，然后通过 `view.leaf.setViewState()` 应用更改。并且会保留浏览历史（`view.leaf.history = history`）。

---

**2. Revert view on close（关闭时还原视图）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Revert view on close|`✅ 开启`|**revertView**|当你从主页跳转到其他笔记时，自动将视图恢复为 Obsidian 的默认视图|

**源码行为（homepage.ts 第 172-195 行）：**

1. 打开主页时，用 `WeakRef` 弱引用记录当前 view（homepage.ts 第 145 行）
2. 当用户导航到其他文件时（通过 `workspace.on("active-leaf-change")` 或其他监听触发 `revertView()`），检查记录的 view 是否还存活
3. 如果存活，比较当前文件是否仍是主页文件——已不是的话，获取 Obsidian 全局配置中的 `defaultViewMode` 和 `livePreview` 设置
4. 如果当前模式与默认模式不一致，就 `setViewState()` 把视图改回默认模式

**使用场景：** 比如你主页设置成了阅读视图打开，看完后点开另一个笔记，这个笔记会自动回到你习惯的编辑/实时预览视图。

---

**3. Auto-scroll（自动滚动）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Auto-scroll|`❌ 关闭`|**autoScroll**|打开主页时，自动滚动到底部并聚焦到最后一行|

**源码行为（homepage.ts 第 147-154 行）：** 对于 Markdown 视图，获取编辑器的总行数：

- 如果是预览模式（reading）：`view.previewMode.applyScroll(count - 4)`，滚动到倒数第 4 行
- 如果是编辑模式：`view.editor.setCursor(count)` 将光标设置到最后一行，然后 `view.editor.focus()` 聚焦编辑器

---

**4. Refresh Dataview（刷新 Dataview）**

|设置项|你当前的值|英文原文|功能说明|
|---|---|---|---|
|Refresh Dataview|`❌ 关闭`|**refreshDataview**|打开主页时强制重新加载 Dataview 查询|
|警告|—|Requires Dataview auto-refresh to be enabled.|需要 Dataview 的自动刷新功能已启用才生效|
|**显示条件**|—|只有检测到库中安装了 Dataview 插件时，此选项才会出现||

**源码行为（homepage.ts 第 186-188 行）：** `this.plugin.communityPlugins.dataview?.index.touch()`——如果 Dataview 插件存在，调用其 `index.touch()` 方法来刷新索引。

---

### 三、禁用逻辑汇总（了解哪些情况下某些设置会被禁用）

|条件|被禁用的设置|
|---|---|
|主页类型为 `Workspace` 或 `None`|Open when empty、Use when normally、Opening method、Manual opening method、Auto-create、Pin|
|主页类型为 `Workspace`、`None` 或 `Graph`|Opened view 组**全部禁用**（View、Revert view、Auto-scroll、Refresh Dataview）|
|`Open on startup` 关闭|Opening method（启动时的打开方式）被禁用|
|主页类型为周期笔记（Daily/Weekly/Monthly/Yearly）或 Journal|Auto-create 被禁用（因为周期笔记由专门的插件管理创建）|

这些禁用关系在代码中用 `setAttribute("nv-greyed", "")` 实现，在 UI 上表现为灰色不可交互状态。

---

### 四、调试功能

|功能|位置|说明|
|---|---|---|
|Copy debug info（复制调试信息）|设置面板底部（仅 PC 显示）|点击后会把当前插件的完整配置、Obsidian 版本、启用的插件列表等信息复制到剪贴板，用于报告问题时提供上下文|