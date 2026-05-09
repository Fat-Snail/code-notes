# Fat-Snail 开源项目

> 个人开源作品集，涵盖企业应用、AI 对话、网络通信、前端工具等方向。
>
> GitHub：[Fat-Snail](https://github.com/Fat-Snail)

**📖 建议通过文档站访问，体验更佳（含侧边栏导航、搜索、代码高亮）：**
**[https://fat-snail.github.io/code-notes/](https://fat-snail.github.io/code-notes/)**

---

## 🐙 OctopusERP ⭐ 2

**面向中小企业的开源 ERP 系统**，采用微服务架构，100% 由 Claude Code 辅助完成。

**核心模块**

| 模块 | 说明 |
|------|------|
| OctopusUMC | 用户管理中心，OIDC 身份认证 + RBAC 权限 |
| OctopusOA | 办公自动化：审批流程、考勤、会议室预定 |
| OctopusPLM | 产品生命周期管理，支持 CLIP 图片向量搜索 |
| OctopusWMS | 仓库管理系统 |
| OctopusCRM | 客户关系管理 |
| OctopusMES | 制造执行系统 |

**技术栈**

- 后端：.NET 10 · ASP.NET Core · OpenIddict · EF Core · SignalR
- 前端：Vue 3.5 · TypeScript · Vite · Tailwind CSS v4 · Pinia
- 基础设施：.NET Aspire · Docker Compose · Qdrant 向量数据库
- 测试：184 个集成测试，TDD 驱动开发

**快速启动**

```bash
git clone https://github.com/Fat-Snail/OctopusERP.git
cd OctopusERP
docker-compose up -d
dotnet run --project aspire
```

🔗 [Fat-Snail/OctopusERP](https://github.com/Fat-Snail/OctopusERP)

---

## 🧰 OctopusUtils ⭐ 4

**C# 工具组件库**，聚合开发中常用的工具类，开箱即用。

**核心功能**

- 异步彩色控制台输出（ConsoleEx）
- 智能重试机制（支持同步 / 异步）
- 全文检索（Lucene.NET 4.8 + 中文分词）
- AI 客户端（OpenAI / Llama 多模型）
- Google Drive 大文件下载
- 轻量单元测试框架 + MiniProfiler 性能分析

**代码示例**

```csharp
// 异步非阻塞彩色输出
ConsoleEx.Info("应用启动成功");
ConsoleEx.WriteLine("警告信息", ConsoleColor.Yellow);
ConsoleEx.Error("发生错误");
await ConsoleEx.ShutdownAsync();

// 智能重试（同步）
var result = Utils.RetryMethod(
    () => CallExternalApi(),
    maxRetries: 3,
    interval: 1000
);

// 智能重试（异步）
await Utils.RetryMethodAsync(
    async () => await FetchRemoteDataAsync(),
    onRetry: (ex, attempt) => Console.WriteLine($"第 {attempt} 次重试：{ex.Message}")
);
```

🔗 [Fat-Snail/OctopusUtils](https://github.com/Fat-Snail/OctopusUtils)

---

## 🌐 X-Net-Mod ⭐ 3

**基于分发式架构的 .NET 网络通信组件集合**，覆盖多种通信场景。

**核心模块**

| 模块 | 说明 |
|------|------|
| Socket 双工通信 | 替代 HTTP 的长连接双向通信 |
| IM 即时通讯 | 消息分发与实时推送 |
| 数据采集 | 设备数据上报与汇聚 |
| 手机集群 | 多设备协同调度 |
| 下位机计算 | 边缘端数据处理 |
| Anti-Crawler | 接口防爬保护机制 |
| SemanticKernel | .NET 10 AI 框架集成 |
| OAuth 2.0 | Cube SSO 单点登录 |

**代码示例**

```csharp
// Socket 双工通信示例
var server = new NetServer();
server.Received += (session, data) =>
{
    var msg = data.ToStr();
    session.Send($"Echo: {msg}");
};
server.Start();

// 数据采集节点注册
var collector = new DataCollector("device-001");
collector.OnData += (point) => Console.WriteLine($"{point.Tag} = {point.Value}");
collector.Connect("192.168.1.100", 5000);
```

🔗 [Fat-Snail/X-Net-Mod](https://github.com/Fat-Snail/X-Net-Mod)

---

## 🤖 TerraMours.Chat.Ava

**基于 Avalonia 的跨平台 AI 对话桌面客户端**，接入 ChatGPT / OpenAI，支持 Windows / Linux / macOS。

**核心功能**

- ChatGPT 多会话管理
- Markdown 消息渲染
- SQLite 本地对话历史持久化
- CSV 导入 / 导出
- 多语言支持（CultureInfo）
- 自定义快捷键与全局样式

**技术栈**

```
Avalonia 11        → 跨平台 UI 框架
ReactiveUI         → MVVM 响应式架构
FluentAvaloniaUI   → 增强控件
Betalgo.OpenAI     → ChatGPT SDK
SQLite             → 本地数据存储
```

**界面结构**

```
App
├── LoadingWindow    # 启动 / 登录页
├── MainWindow       # 主对话界面
│   ├── ChatPanel    # 消息输入与展示
│   └── SessionGrid  # 会话列表管理
└── SettingsPanel    # API Key 配置
```

🔗 [Fat-Snail/TerraMours.Chat.Ava](https://github.com/Fat-Snail/TerraMours.Chat.Ava)

---

## 📝 vue3-rtf ⭐ 1

**零构建的 Vue3 富文本编辑器**，无需 Node.js，直接在浏览器中引入使用，专为后端开发者设计。

**核心特性**

- 不依赖 Node.js / Webpack / 脚手架
- 浏览器直接运行 `.vue` 文件
- 支持 CSS Scoped 样式隔离
- 内置 Element Plus 组件
- 可与 jQuery 混用，平滑迁移遗留项目

**代码示例**

```html
<!DOCTYPE html>
<html>
<head>
  <!-- 直接 CDN 引入，无需构建 -->
  <link rel="stylesheet" href="https://unpkg.com/element-plus/dist/index.css" />
  <script src="https://unpkg.com/vue@3/dist/vue.esm-browser.js" type="importmap-shim"></script>
  <script src="https://unpkg.com/element-plus"></script>
</head>
<body>
  <div id="app"></div>
  <script type="module">
    // 直接加载 .vue 文件，无需编译
    import App from './App.vue'
    const { createApp } = Vue
    createApp(App).use(ElementPlus).mount('#app')
  </script>
</body>
</html>
```

```html
<!-- App.vue —— 正常写 Vue3 单文件组件 -->
<template>
  <el-input v-model="content" type="textarea" :rows="6" placeholder="输入内容..." />
  <el-button type="primary" @click="submit">提交</el-button>
</template>

<script setup>
import { ref } from 'vue'
const content = ref('')
const submit = () => console.log(content.value)
</script>

<style scoped>
.el-button { margin-top: 12px; }
</style>
```

🔗 [Fat-Snail/vue3-rtf](https://github.com/Fat-Snail/vue3-rtf)

---

## 📦 OctopusJs

**TypeScript 前端工具库**，与 Octopus 系列后端配套。

🔗 [Fat-Snail/OctopusJs](https://github.com/Fat-Snail/OctopusJs)

---

## 🛠 XCoder

**多合一开发工具箱**，集成以下功能：代码生成、网络调试、API 测试、串口调试、正则验证、图标处理、加密解密。

🔗 [Fat-Snail/XCoder](https://github.com/Fat-Snail/XCoder)

---

## 🎨 CsharpSkin ⭐ 1

**C# UI 皮肤框架**，用于美化 WinForm / WPF 应用界面。

🔗 [Fat-Snail/CsharpSkin](https://github.com/Fat-Snail/CsharpSkin)

---

## 🔧 基础设施（Fork & 定制）

对 [NewLife](https://github.com/newlifex) 系列框架的个人定制与扩展。

### X-Mod ⭐ 3
NewLife.XCode 个人定制版，针对性修改与功能扩展。
🔗 [Fat-Snail/X-Mod](https://github.com/Fat-Snail/X-Mod)

---

## 📖 学习与文档

| 项目 | 说明 | 链接 |
|------|------|------|
| techdoc ⭐ 1 | 免费在线编程教程合集 | [Fat-Snail/techdoc](https://github.com/Fat-Snail/techdoc) |
| SweetHouse ⭐ 1 | 茶饮店模拟器 | [Fat-Snail/SweetHouse](https://github.com/Fat-Snail/SweetHouse) |
| doc-fatcode | 个人技术文档管理 | [Fat-Snail/doc-fatcode](https://github.com/Fat-Snail/doc-fatcode) |

---

*最后更新：2026-05-09*
