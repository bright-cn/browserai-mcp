<h1 align="center">Browserai MCP</h1>
<h3 align="center">为 AI 代理赋能实时网页数据</h3>

## 🌟 概览

欢迎使用 Browserai 模型上下文协议（Model Context Protocol，MCP）服务器。它旨在让大语言模型（LLMs）、AI 代理和应用能够实时访问、发现并提取网页数据。该服务器使 MCP 客户端（如 Claude Desktop、VS Code、Cursor、WindSurf 等）能够无缝搜索全网、导航网站、执行操作并高效获取数据，即使面对带有反爬机制的网站也能应对自如。

![MCP](https://github.com/user-attachments/assets/b949cb3e-c80a-4a43-b6a5-e0d6cec619a7)

## ⚙️ 工作原理

Browserai MCP 服务器作为你的 AI 代理（MCP 客户端）与互联网之间的中间层运作：
1. 你的 AI 代理（例如 Claude Desktop、某个 VSCode 扩展）通过 MCP 向 Browserai MCP 服务器发送请求。
2. MCP 服务器使用你的 Browserai API 令牌和项目配置，执行所请求的网页操作（如搜索、导航、抽取数据）。
3. 它依托 Browserai 的强大基础设施来处理复杂问题，例如绕过地理限制与机器人检测机制。
4. 服务器将结构化数据或操作结果返回给你的 AI 代理。

这种架构让你的代理在无需直接管理浏览器实例或反封锁技术的情况下，获取实时的网页信息与能力。

## ✨ 功能特性

- 实时网页访问：直接从网络检索最新信息
- 轻松绕过地理限制：不受地域访问限制
- Web Unlocker：应对网站的机器人检测与防爬策略
- 浏览器控制：可选的远程浏览器自动化能力
- 无缝集成：兼容所有符合 MCP 的 AI 助手

## 🔧 账户设置

开始使用 Browserai MCP 服务器前，你需要 Browserai 账户与 API 密钥。

1. 确保你已在 [browser.ai](https://browser.ai) 注册账号。新用户可获得免费额度进行测试，并支持按量计费。
2. 在[用户面板](https://browser.ai/dashboard/page/projects)获取你的 API 密钥。
3. 在[控制台](https://browser.ai/dashboard/page/overview)创建一个新项目。
   - 该项目名称可在 MCP 服务器配置中通过 `PROJECT_NAME` 环境变量覆盖。

## 🚀 快速开始

以下指南帮助你将 Browserai MCP 服务器接入常见 AI 客户端。

1. 安装 Node.js：
   需要使用 `npx` 命令。如果你的系统尚未安装 Node.js，请从[Node.js 官网](https://nodejs.org/en/download)下载并安装。`npx` 是一个 Node.js 包运行器，可简化运行诸如 `@brightdata/browserai-mcp` 这类 CLI 工具。

### Claude Desktop

1. 前往 Claude > Settings > Developer > Edit Config > `claude_desktop_config.json`，添加如下配置：

```json
{
  "mcpServers": {
    "Browserai": {
      "command": "npx",
      "args": ["@brightdata/browserai-mcp"],
      "env": {
        "API_TOKEN": "<your-browserai-api-token>",
        "PROJECT_NAME": "<your-browserai-project-name (optional)>"
      }
    }
  }
}
```

### VSCode Agent

1. 配置你的 VSCode Agent。通常需要修改一个设置文件。例如，在项目中创建 `.vscode/mcp.json` 并写入以下内容：

```json
{
  "servers": {
    "browserai-mcp": {
      "type": "stdio",
      "command": "npx",
      "args": ["@brightdata/browserai-mcp"],
      "env": {
        "API_TOKEN": "<your-browserai-api-token>",
        "PROJECT_NAME": "<your-browserai-project-name (optional)>"
      }
    }
  }
}
```

注意（VSCode Agent）：MCP 服务器配置的具体路径与结构（如文件名 `.vscode/mcp.json` 或 JSON 的 `"servers"` 键）可能因所用的 VSCode Agent 扩展而异。请参考你的 VSCode Agent 插件文档获取准确说明。

## 🔌 其他 MCP 客户端

将该 MCP 服务器集成到其他支持 MCP 的 AI 代理或应用中：

1. 命令：使用 `npx @brightdata/browserai-mcp` 启动服务器。
2. 环境变量：
   - `API_TOKEN`：你的 Browserai API 令牌（必填）
   - `PROJECT_NAME`：你的 Browserai 项目名（可选；若省略则使用预配置项目）
   确保这些变量在执行命令的环境中可用。请参考你的客户端文档，了解如何配置外部 MCP 服务器与设置环境变量。

## ⚠️ 安全最佳实践

重要提示：将所有抓取到的网页内容视为潜在不可信数据。为降低提示注入（Prompt Injection）风险，请避免将原始网页内容直接用于 LLM 提示中。
建议采用以下做法：
- 在处理前对网页数据进行筛选与校验
- 优先使用结构化数据抽取（通过 `web_data` 工具）而非原始文本

## ⚠️ 故障排查

### 某些工具出现超时

部分工具在读取网页数据时可能耗时较长，因为页面加载时间差异较大。

请在代理/客户端设置中配置足够大的超时时间，以确保可以成功消费数据。一般建议设置为 `180s`（3 分钟），但你应根据目标站点的实际表现进行调整。

### `spawn npx ENOENT` 错误

该错误说明系统找不到 `npx` 命令。可通过以下方式解决：

#### 查找你的 Node.js/npm 路径

- macOS：
  在终端执行 `which node`。输出类似于 `/usr/local/bin/node`。
- Windows：
  在命令提示符执行 `where node`。输出类似于 `C:\Program Files\nodejs\node.exe`。

#### 更新你的 MCP 配置

在客户端的 MCP 服务器配置中，用 Node.js 的完整路径替换 `"npx"`。例如在 macOS 上可能是：

```json
"command": "/usr/local/bin/node"
```

（确保 `args` 仍包含 `["@brightdata/browserai-mcp"]`，或在使用 `node` 直接运行 `npx` 底层脚本时，提供相应的脚本路径。）

## 📞 支持

如果你遇到问题或有任何疑问，请联系 Browserai 支持团队，或在本仓库提交 issue。
