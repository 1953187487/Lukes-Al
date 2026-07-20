# Lukes-Al

> 基于单文件 HTML 的前端安全智能控制台

## 当前版本：v0.1 - First Light（测试发行版）

本仓库仅包含前端实现，核心逻辑集中在 `index.html` 内，以浏览器本地能力运行，不依赖自建后端服务。

### 运行方式

直接在浏览器中打开 `index.html` 即可使用。配置项与数据持久化保存在浏览器 `localStorage` 中。

---

## 功能与代码结构分析

### 1. AI Cortex
- **入口**：`AI` 对象，位于 `index.html` 中。
- **能力**：调用兼容 OpenAI 的 `/chat/completions` 接口，支持 SSE 流式输出。
- **实现要点**：
  - `AI.stream(messages)` 使用 `fetch` + `ReadableStream` 解析 `data:` 行；
  - `AI.send(msg)` 维护对话上下文，并将最近 10 条作为上下文发送；
  - 支持多 Provider，并在设置页切换 API URL。
- **可配置项**：`provider`、`apiUrl`、`apiKey`、`model`、`temperature`、`maxTokens`。

### 2. Strike Console
- **入口**：`Attack` 对象。
- **能力**：对目标 `IP:Port` 执行模拟任务，生成终端风格输出与执行日志。
- **实现要点**：
  - `Attack.validate(ip)` 校验 IPv4 格式，默认端口 `10200`；
  - `Attack.exec(target)` 为模拟执行，结果带有随机成功/失败与延迟；
  - 历史记录写入 `State.attackHistory`，并通过 `Utils.saveAttacks()` 持久化。
- **注意**：当前实现为前端演示级模拟，不代表真实网络探测或攻击行为。

### 3. Android FS Bridge
- **入口**：`FS` 对象。
- **能力**：调用浏览器 `File System Access API` 绑定目录，列目录内容并展示。
- **实现要点**：
  - `FS.bind()` 调用 `showDirectoryPicker()` 并请求读写权限；
  - `FS.list()` 递归遍历绑定目录，展示文件名与大小；
  - 绑定结果保存到配置中，重启浏览器后仍可恢复 UI 状态，但 `FileSystemHandle` 无法序列化，需要重新授权。

### 4. 主题与界面
- **入口**：CSS 变量与 `UI.initParticles()`、`UI.toggleSidebar()`。
- **能力**：提供 `quantum` 与 `matrix` 两套主题，支持侧边栏折叠、启动动画、粒子背景。
- **实现要点**：通过 `data-theme` 切换 CSS 变量，主题配置写入 `localStorage`。

### 5. 数据管理
- **入口**：`DataHub` 对象。
- **能力**：导出 `.lukeal` 文件、导入恢复数据、清空本地数据。
- **实现要点**：
  - 导出内容包含配置、聊天记录、攻击历史、日志；
  - 导入时合并配置并刷新页面相关视图；
  - 清空使用 `localStorage.clear()`。

### 6. 版本检查
- **入口**：`VersionManager`。
- **能力**：启动时访问远程 `version.json`，若有新版本则在顶部显示更新横幅。
- **实现要点**：基于时间戳避免缓存，用户可关闭并忽略特定版本。

---

## 已知限制

- **无后端**：所有数据存储在本机浏览器，换设备或清缓存会丢失。
- **文件系统访问受浏览器限制**：`showDirectoryPicker` 主要在 Chromium 系桌面/Android 环境可用。
- **Strike Console 为模拟演示**：不保证与实际服务端行为一致。
- **跨域与 API 兼容性**：需目标 AI 服务支持 CORS 与 OpenAI 兼容格式。

---

## 版本说明

| 版本 | 类型 | 说明 |
|------|------|------|
| v0.1 | 测试发行版 | 首次公开测试版本，包含基础 AI、模拟终端、FS Bridge、主题与数据管理 |

---

## 协议与声明

- 采用 MIT 协议发布。
- 本项目仅用于授权安全研究与教育场景，使用者需自行承担合规责任。

---

*仓库地址：https://github.com/1953187487/Lukes-Al*