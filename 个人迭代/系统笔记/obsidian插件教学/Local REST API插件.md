---
tags:
  - 术
---
# Local REST API with MCP 插件

> 版本：当前适用 | 路径：设置 → 社区插件 → Local REST API with MCP → 选项

---

## 核心功能

Local REST API 在 Obsidian 本地启动一个 HTTP 服务器（默认 `http://127.0.0.1:27123`），让外部工具通过 REST 接口安全读写 vault。它是浏览器扩展（如 Bilibili Obsidian Clipper）与 vault 之间的桥梁——扩展不能直接写文件，通过 HTTP API 中转即可。

---

## 一、安装与启用

1. 打开 Obsidian 设置 → 社区插件 → 浏览
2. 搜索 `Local REST API with MCP`
3. 安装并启用

---

## 二、核心配置

打开插件设置面板（插件列表 → Local REST API with MCP → 选项图标）。

| 配置项 | 设置 | 说明 |
|------|------|------|
| **Enable Non-encrypted (HTTP) Server** | ✅ 勾选 | 开启 HTTP 服务（非 HTTPS），地址 `http://127.0.0.1:27123` |
| **API Key** | 自动生成一串密钥 | 点击复制按钮备用，下一步浏览器扩展需要填入 |

> 安全说明：`127.0.0.1` 是本地回环地址，只有本机能访问。外部网络无法连接。HTTP 非加密仅在本机内传输，不影响安全性。

---

## 三、与 Bilibili Obsidian Clipper 的联动

### 3.1 浏览器扩展端配置

打开浏览器扩展设置页：扩展图标右键 → 选项（或地址栏输入 `chrome://extensions` → Bilibili Obsidian Clipper → 详情 → 扩展选项）。

| 配置项 | 填入值 | 说明 |
|------|------|------|
| Local REST API 地址 | `http://127.0.0.1:27123` | 即 Obsidian 插件启动的 HTTP 服务地址 |
| Local REST API Key | （从插件设置复制的密钥） | 身份验证，防止其他本地程序随意写入 vault |
| 保存路径 | `个人迭代/待处理笔记/网站爬取` | 所有爬取文件统一落于此目录 |

### 3.2 联动流程

```
Bilibili Obsidian Clipper（浏览器端）
    │
    │  HTTP POST → http://127.0.0.1:27123/...
    │  携带 API Key 认证
    ▼
Local REST API with MCP（Obsidian 端）
    │
    │  验证 API Key → 写入文件
    ▼
个人迭代/待处理笔记/网站爬取/{视频标题}.srt（或 .txt）
```

### 3.3 爬取内容

Bilibili Clipper 支持：
- **字幕提取**：爬取视频 CC 字幕，存为 SRT（带时间码）或 TXT（纯文本）
- **阅读模式**：扩展内置视频播放器 + 可跳转时间码的字幕面板
- **元数据**：标题、UP 主、发布时间、视频简介

---

## 四、与 Agent 的后续联动

爬取文件落地后，Agent 可读取 vault 中的字幕文件进行 AI 总结。SRT 格式的时间码在 AI 总结时会被自动过滤，只保留纯字幕文本。

典型使用链路：

```
B站视频 → Clipper 爬取字幕 → 写入 vault
    │
    ▼
Agent 读取原件 → 总结 → 输出到常青笔记（#识）
                           → 或系统笔记（属某体系时）
```

---

## 五、两个插件对比

| 维度 | 官方 Web Clipper | Bilibili Obsidian Clipper |
|------|:---:|:---:|
| 写入方式 | 浏览器直接写 vault | 通过 Local REST API 中转 |
| B站字幕提取 | ❌ | ✅ |
| 视频阅读模式 | ❌ | ✅（视频+跳转字幕） |
| 中文/B站适配 | 通用 | 针对 B站优化 |
| 额外依赖 | 无 | 需 Obsidian 安装 Local REST API 插件 |

当前策略：B站视频爬取用 Bilibili Clipper + Local REST API；通用网页裁剪用官方 Web Clipper。两者均直接写入 `个人迭代/待处理笔记/网站爬取/`，不经过模板（见 [[笔记系统框架#网站爬取]]）。

---

## 六、常见问题

| 问题 | 排查 |
|------|------|
| 浏览器扩展报"无法连接" | 检查 Obsidian 是否打开、Local REST API 插件是否已启用、`Enable HTTP Server` 是否勾选 |
| 报"401 Unauthorized" | API Key 是否粘贴正确（注意前后无空格） |
| 文件没出现在 vault 里 | 检查保存路径是否与 vault 目录结构一致（`个人迭代/待处理笔记/网站爬取`） |
| API Key 找不到了 | 进入 Obsidian → Local REST API 插件设置 → 复制当前的 API Key，更新浏览器扩展端 |
