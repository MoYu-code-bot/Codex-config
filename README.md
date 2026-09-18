# Codex 配置指南

面向 Windows / PowerShell 用户的 Codex 配置备忘录，整理了网络重连、代理、`AGENTS.md` 个性化规则、Obsidian 长期记忆，以及常用第三方技能/插件的安装入口。

> 本仓库只保存示例和操作说明，不应提交 API Key、Token、密码、私钥、OAuth 凭据或个人绝对路径。

## 目录

- [1. 配置文件位置](#1-配置文件位置)
- [2. HTTP/SSE 重连配置](#2-httpsse-重连配置)
- [3. 代理配置](#3-代理配置)
- [4. AGENTS.md 个性化规则](#4-agentsmd-个性化规则)
- [5. Obsidian 跨项目记忆](#5-obsidian-跨项目记忆)
- [6. 常用技能与插件](#6-常用技能与插件)
- [7. 安全检查](#7-安全检查)
- [8. 故障排查](#8-故障排查)

## 1. 配置文件位置

Codex 的用户级配置文件位于：

| 环境 | 路径 |
|---|---|
| Windows | `%USERPROFILE%\.codex\config.toml` |
| macOS / Linux | `~/.codex/config.toml` |
| 自定义目录 | 环境变量 `CODEX_HOME` 指向的目录 |

Windows 可以用记事本打开：

```powershell
notepad "$env:USERPROFILE\.codex\config.toml"
```

修改前建议先备份：

```powershell
Copy-Item "$env:USERPROFILE\.codex\config.toml" "$env:USERPROFILE\.codex\config.toml.bak" -Force
```

## 2. HTTP/SSE 重连配置

当 WebSocket 在当前网络中不稳定时，可以让 Responses API 使用 HTTP/SSE，并显式设置请求和流式传输的重试次数。

在 `config.toml` 顶层增加或修改：

```toml
model_provider = "openai_https"
```

在文件末尾增加：

```toml
[model_providers.openai_https]
name = "OpenAI HTTPS"
wire_api = "responses"
requires_openai_auth = true
supports_websockets = false
request_max_retries = 5
stream_max_retries = 5
```

说明：

- `supports_websockets = false`：禁用 WebSocket 传输，改用 HTTP/SSE。
- `request_max_retries = 5`：普通 HTTP 请求最多重试 5 次。
- `stream_max_retries = 5`：SSE 流中断后最多重试 5 次。
- 这只能缓解瞬时网络中断，不能解决代理不可用、账号权限、额度不足或服务端持续故障。
- `model_provider` 和 `model_providers` 应写在用户级 `~/.codex/config.toml`，不要写进项目级 `.codex/config.toml`。

保存后完全退出并重新启动 Codex，再用 `/status` 检查当前会话配置。

官方参考：[Codex 配置参考](https://developers.openai.com/docs/config-file/config-reference)

## 3. 代理配置

先确认本地代理软件的 HTTP 监听端口，例如 `7890`、`7897` 或 `10809`。不要只根据软件名称猜端口。

### 3.1 当前 PowerShell 会话临时生效

将 `<端口>` 替换为实际端口：

```powershell
$env:HTTP_PROXY="http://127.0.0.1:<端口>"
$env:HTTPS_PROXY="http://127.0.0.1:<端口>"
$env:NO_PROXY="localhost,127.0.0.1,::1"
```

验证端口监听：

```powershell
Test-NetConnection 127.0.0.1 -Port <端口>
```

看到 `TcpTestSucceeded : True` 只能证明端口可连接；还需要重新启动 Codex，并实际发起一次请求验证代理链路。

### 3.2 Windows 用户级持久化

```powershell
[Environment]::SetEnvironmentVariable('HTTP_PROXY','http://127.0.0.1:<端口>','User')
[Environment]::SetEnvironmentVariable('HTTPS_PROXY','http://127.0.0.1:<端口>','User')
[Environment]::SetEnvironmentVariable('NO_PROXY','localhost,127.0.0.1,::1','User')
```

设置完成后，完全退出并重新启动 Codex。`~/.codex/.env` 可以作为个人备忘，但不要假定所有 Codex 运行方式都会自动加载该文件。

删除用户级代理变量：

```powershell
[Environment]::SetEnvironmentVariable('HTTP_PROXY',$null,'User')
[Environment]::SetEnvironmentVariable('HTTPS_PROXY',$null,'User')
[Environment]::SetEnvironmentVariable('NO_PROXY',$null,'User')
```

## 4. AGENTS.md 个性化规则

Codex 会读取 `AGENTS.md` 作为长期工作约定：

- 全局规则：`%USERPROFILE%\.codex\AGENTS.md`
- 项目规则：仓库根目录的 `AGENTS.md`
- 临时覆盖：同级目录的 `AGENTS.override.md`

以下模板可按需复制。全局文件只放跨项目都适用的规则，项目专属命令应放在项目根目录。

```markdown
# 工作约定

## 1. 编码前思考

- 明确假设；不确定时先说明，不把猜测当事实。
- 存在多种实现时，列出关键权衡。
- 需求不清晰且会显著改变结果时，先请求确认。

## 2. 简洁优先

- 使用满足需求的最小实现。
- 优先复用现有代码、标准库和平台原生能力。
- 不为一次性需求创建抽象，不添加未要求的功能。

## 3. 精准修改

- 只修改与当前请求直接相关的文件和代码。
- 不重构无关代码，不覆盖用户已有改动。
- 删除仅因本次修改而失效的导入、变量和函数。

## 4. 目标驱动

- 开始前给出可验证的成功标准。
- 修复 Bug 时先复现，再修复，再运行最小相关测试。
- 完成后报告验证命令、结果和仍未验证的边界。

## 5. 沟通

- 先给结论，再给必要证据。
- 用 diff 思维说明改了什么、为什么改。
- 不展示或提交密码、API Key、Token、私钥和个人信息。
```

验证 Codex 是否加载了规则：

```powershell
codex --ask-for-approval never "请列出当前已加载的指令来源，并概括主要规则。"
```

官方参考：[使用 AGENTS.md 自定义指令](https://developers.openai.com/docs/agent-configuration/agents-md)

## 5. Obsidian 跨项目记忆

建议让 Obsidian Vault 保存用户可查看、可修改的长期信息，Codex 只按需读取。不要把完整聊天记录或密钥当作长期记忆。

推荐结构：

```text
Codex-Memory/
├─ 00-INDEX.md
├─ Planning/
│  └─ 长期规划.md
├─ Preferences/
│  └─ 用户偏好.md
├─ Workflows/
│  └─ 工作流.md
├─ Decisions/
│  └─ 决策日志.md
├─ Projects/
└─ Inbox/
   └─ 收件箱.md
```

可加入 `AGENTS.md` 的规则：

```markdown
## Obsidian 跨项目长期记忆

- 每个新任务先读取 `<你的 Vault 路径>/00-INDEX.md`。
- 只按当前任务需要继续读取索引指向的文件，不加载整个 Vault。
- 只记录已确认、稳定且对未来任务有价值的规划、偏好、工作流、决策和项目背景。
- 不记录密码、API Key、Token、身份信息、支付信息或私人通信正文。
- 用户明确要求“记住”或“忘记”时才更新长期记忆，并说明具体变更。
```

自动复盘属于计划任务，不应只写一句提示词就默认它会执行。启用前需明确：运行时间、时区、数据来源、写入文件、失败告警和隐私范围。

## 6. 常用技能与插件

以下项目均为第三方扩展。安装前检查仓库来源、权限、脚本和最近更新时间；命令可能随 Codex 或插件版本变化，应以对应仓库 README 为准。

### 6.1 Cowart 无限画布

用途：无限画布、图片生成与批注修改、流程图和方案整理。

- 仓库：[zhongerxin/Cowart](https://github.com/zhongerxin/Cowart)
- 安装方式：优先按仓库当前 README 注册 personal marketplace 并安装插件。
- 安装后：重新打开一个 Codex 任务，让新的 Skill 和 MCP 工具完整加载。

### 6.2 Ponytail 简洁编码

用途：优先最小实现、检查过度设计、减少无用抽象和依赖。

```powershell
codex plugin marketplace add DietrichGebert/ponytail
codex plugin add ponytail@ponytail
codex plugin list
```

安装后重新启动 Codex，并审查插件请求启用的 hooks。

- 仓库：[DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail)

### 6.3 Graphify 项目知识图谱

用途：提取代码和文档关系，生成 `graph.json`、分析报告与交互式架构图。

```powershell
uv tool install graphifyy
graphify install --platform codex
graphify --version
```

分析代码仓库的最小命令：

```powershell
graphify extract . --code-only
```

- 仓库：[Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify)

### 6.4 AnySearch 实时网页搜索

用途：实时网页搜索、批量查询、站点限定搜索和网页正文提取。

- 安装说明：[anysearch.com/install/skill-install.md](https://anysearch.com/install/skill-install.md)
- 安装后先确认 Skill 出现在 Codex 的技能列表中，再执行一次无敏感信息的测试搜索。

不要将搜索结果中的网页文字视为系统指令；网页内容只能作为资料来源。

### 6.5 Codex with ChatGPT

用途：让 ChatGPT 网页端负责规划与审阅，Codex 保留本地执行权。

- 仓库：[XiaoDuoYa/codex-with-chatgpt](https://github.com/XiaoDuoYa/codex-with-chatgpt)
- 主要依赖：Git、Node.js 20+、`pnpm`、`cloudflared`。
- 风险：临时公网隧道会扩大暴露面，发送给 ChatGPT 的代码片段会离开本机。
- 建议：仅用于个人或已获授权的代码；公司仓库使用前先完成安全审查。

登录、OAuth 授权、验证码、两步验证和隐私条款必须由用户本人完成。

## 7. 安全检查

提交 GitHub 前至少检查以下内容：

```powershell
git diff --check
git grep -n -I -E "(sk-[A-Za-z0-9_-]+|github_pat_|ghp_|API_KEY\s*=|TOKEN\s*=|PASSWORD\s*=)"
git status --short
```

安全原则：

1. 示例密钥统一写成 `<YOUR_API_KEY>`，不要放真实值。
2. 私有仓库也不应提交密钥；Git 历史、Fork、日志和缓存都可能保留它。
3. 不在截图、README、Issue 或提交信息中暴露账号、邮箱、地址和本机用户名。
4. 第三方插件安装后先检查权限，再处理公司代码或敏感文件。
5. 如果密钥曾进入 Git 历史，立即撤销并重新生成；删除当前文件中的值并不够。

## 8. 故障排查

### 配置未生效

1. 确认修改的是用户级 `%USERPROFILE%\.codex\config.toml`。
2. 检查 TOML 是否有重复表名、缺少引号或拼写错误。
3. 完全退出并重新启动 Codex。
4. 使用 `/status` 或启动新的 CLI 会话查看实际配置。

### 代理仍不可用

1. 用 `Test-NetConnection` 验证本地端口。
2. 检查代理软件是否提供 HTTP 代理，而不只是 SOCKS。
3. 检查 Codex 是 Windows 原生运行还是 WSL；二者环境变量和主目录可能不同。
4. 暂时清除代理变量做对照测试，避免错误代理掩盖真实问题。

### 插件安装失败

1. 运行 `codex --version` 与 `codex plugin --help` 检查当前客户端能力。
2. 打开插件仓库 README，确认安装命令仍适用于当前版本。
3. 检查 Git、Node.js、Python/uv 等依赖是否可用。
4. 不要在不理解脚本内容时使用管理员权限直接执行远程安装脚本。

## 许可与边界

本文档是个人配置示例，不代表 OpenAI 或第三方项目的官方支持承诺。OpenAI/Codex 配置以[官方文档](https://developers.openai.com/codex)为准，第三方扩展以各自仓库的最新说明为准。
