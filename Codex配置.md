# Codex五次重连配置

顶部增加或修改

model_provider = "openai_https"

文件末尾增加

[model_providers.openai_https]
name = "OpenAI"
wire_api = "responses"
requires_openai_auth = true
supports_websockets = false

下方内容可选是否添加

配置完成后在Codex聊天中输入：
Prompt:
“请检查我电脑当前 HTTP Proxy 的本地监听端口（例如 7890）。
然后创建～/.codex/.env 文件（如果不存在就创建）
写入以下环境变量:
HTTP_PROXY=[http://127.0.0.1](https://link.wtturl.cn/?target=http%3A%2F%2F127.0.0.1&scene=im&aid=497858&lang=zh):<端口>
HTTPS_PROXY=[http://127.0.0.1](https://link.wtturl.cn/?target=http%3A%2F%2F127.0.0.1&scene=im&aid=497858&lang=zh):<端口>
http_proxy=[http://127.0.0.1](https://link.wtturl.cn/?target=http%3A%2F%2F127.0.0.1&scene=im&aid=497858&lang=zh):<端口>
https_proxy=[http://127.0.0.1](https://link.wtturl.cn/?target=http%3A%2F%2F127.0.0.1&scene=im&aid=497858&lang=zh):<端口>
NO_PROXY=[localhost](https://link.wtturl.cn/?target=https%3A%2F%2Flocalhost&scene=im&aid=497858&lang=zh),127.0.0.1,::1
最后请:
显示～/.codex/.env 的内容（敏感信息无需隐藏，因为这里只包含代理配置）。
验证 Codex 进程能够读取这些环境变量。
不修改其他任何配置文件。

# Obsidian配置

请帮我配置Obsidian作为Codex的跨项目永久记忆库，将长期规划、偏好、工作流和决策都写入Obsidian，每次任务前先读取该目录实现永久记忆与跨会话复用

下面的话术可根据自己的使用情况选择是否添加：

以后每天凌晨1:00复盘整理当日所有项目沟通记录，将结果同步给Obsidian作为不间断的新增记忆以备随时调用

# 个性化配置

- 1. 编码前思考

  不做假设，不隐藏困惑，展示权衡。动手写代码前：
  明确说出你的假设；不确定就问，不要猜测
  若存在多种理解方式，列出选项，不要默默选一种就开干
  如果有更简单的方案，主动说出来；该反驳时反驳
  需求不清晰就停下来，指出哪里模糊，要求澄清
  自检：我是否在“猜测”用户想要什么？如果是，先问再写

  2. 简洁优先

  用最少代码解决问题。不要过度推测。
  不添加需求外的功能
  不为一次性代码创建抽象
  不添加未要求的“灵活性”或“可配置性”
  不为不可能发生的场景做过度错误处理
  如果 200 行代码可以写成 50 行，重写它
  检验：资深工程师会觉得这过于复杂吗？如果是，简化

  3. 精准修改

  只碰必须碰的。只清理自己造成的混乱。
  不“改进”相邻的代码、注释或格式
  不重构没坏的东西；匹配现有风格
  预先存在的死代码只提一下，不擅自删除
  删除因我的改动而变得无用的导入 / 变量 / 函数
  检验：每一行修改都能直接追溯到用户的请求

  4. 目标驱动执行

  定义成功标准。循环验证直到达成。
  把指令式任务转为可验证的目标
  “添加验证” → 先为无效输入写测试，再让它通过
  “修复 bug” → 先写重现 bug 的测试，再让它通过
  “重构 X” → 确保重构前后测试都能通过
  多步骤任务给出一个简短计划：步骤 → 验证
  完成后对照成功标准自检，未达标不停止

  5. 沟通与输出

  先给结论，再给出必要的推理过程
  代码改动用 diff 思维说明：改了什么、为什么改
  遇到不确定先问，不在用户未确认时做大幅变更

  6. Obsidian 跨项目永久记忆

  每个新任务开始时，先读取 `***.md`。只按当前任务需要继续读取索引指向的相关文件，不要默认加载整个记忆库。

  将该 Vault 作为用户可查看、可修改的跨项目长期记忆库：

  - 只记录已确认、相对稳定且对未来任务有价值的长期规划、偏好、固定工作流、重要决策和项目背景。
  - 不记录密码、API Key、Token、身份信息、支付信息或私人通信正文。
  - 不把推测写成事实；重大规划、偏好变化和架构决策在写入前先询问用户。
  - 用户明确要求“记住”或“忘记”时，更新对应文件并说明具体变更。
  - 执行任务产生普通临时信息时不要写入记忆库；只有确有长期价值时才更新。
  - 记忆内容与用户当前指令冲突时，以当前指令为准，并询问是否更新旧记忆。



# 技能安装

## 1. Cowart画布

### 介绍

Cowart 是一个无限画布工具，类似“在线白板 + AI 绘图工具”。

它可以：

- 打开无限画布，自由摆放文字、图片和图形
- 根据描述生成图片并放入画布
- 根据批注修改图片
- 画流程图、思维导图和产品草图
- 整理多个方案和参考图片

### 安装

请通过 Cowart 仓库自带的 Git marketplace 安装 Cowart Codex 插件。
先运行 codex plugin marketplace add zhongerxin/Cowart --ref main，
再运行 codex plugin add cowart@cowart-github，并用 codex plugin list 确认插件已启用。
不要把仓库 clone 到 personal marketplace。安装完成后请告诉我开启一个新任务，
以便加载 Cowart 的新技能和 MCP 工具。



## 2. Ponytail代码检索

### 介绍

Ponytail 不是普通“代码检索”技能，它更像一位非常讨厌复杂代码的资深程序员。

它的原则是：

> 能用一行解决，就不要写五十行；能使用系统自带功能，就不要再安装依赖。

它可以：

- 编写尽可能简单的代码
- 修复 Bug 时避免大范围重构
- 检查项目是否过度设计
- 找出可以删除的抽象层、依赖和样板代码
- 记录为了快速交付而暂时搁置的问题
- 评审代码复杂度

### 安装

codex plugin marketplace add DietrichGebert/ponytail
codex plugin add ponytail@ponytail
根据命令和以下文档帮我安装技能：
https://github.com/DietrichGebert/ponytail



## 3. Graphify项目架构图

### 介绍

Graphify 会把一个项目中的文件、类、函数和依赖关系整理成“知识地图”。

可以把它想象成：

```
普通查看代码：一间一间进入房间查看
Graphify：先拿到整栋楼的结构图
```

它可以：

- 分析代码库整体架构
- 找出重要模块和核心文件
- 查看函数、类和文件之间的关系
- 追踪一个功能从入口到数据库的调用路径
- 生成交互式 HTML 架构图
- 生成项目分析报告
- 分析代码之外的文档、论文和图片

### 安装

https://github.com/Graphify-Labs/graphify
阅读并安装技能



## 4. Anysearch全局网页搜索

### 介绍

AnySearch 是实时互联网搜索工具，类似让 Codex 使用一个专门的搜索引擎。

它可以：

- 搜索最新网页
- 查询新闻和实时信息
- 搜索某个特定网站
- 同时搜索多个问题
- 提取指定网页的正文
- 查找产品、技术文档和教程

### 安装

anysearch.com/install/skill-install.md阅读文档，并根据文档帮我安装anysearch技能



## 5.Codex with ChatGPT

### 介绍

**Codex with ChatGPT** 是一个把"想"和"做"拆开的省钱工具

1、**定位**：Codex with ChatGPT 是一个把"想"和"做"拆开的省钱工具——让 ChatGPT 网页版当规划大脑（拆需求、审方案），Codex 只负责动手改代码，把你闲置的 ChatGPT Plus/Pro 网页额度用起来，省下紧张的 API 额度。
2、**原理**：本地服务经 Cloudflare 临时隧道暴露给 ChatGPT，用 OAuth + 一次性配对码鉴权，ChatGPT 通过**只读 MCP** 读代码（改不了你的文件），真正落笔改代码的只有 Codex。
3、**性质**：作者 XiaoDuoYa，TypeScript / Node.js ≥ 20，版本 v0.1.x（2026-08 首发），**不用 API Key、不做逆向代理**，走官方网页桥接。
4、**安装**：把作者给的一段提示词粘给 Codex，它会自动装 git / Node / cloudflared、克隆构建、配好 Skill，只有登录验证码时才需要你出手。
5、**优点**：思路切中"订阅额度 vs API 额度"的真实价差，权限设计克制（只读 + 一次性配对码），项目自带 76 个测试。
6、**风险**：公网隧道会暴露本地服务、代码片段仍会流向 OpenAI、项目很新导致 API/配置可能频繁变动、个人项目无第三方审计、Cloudflare 隧道断了整条链路就断。
7、**建议**：个人拿来省额度可以试，**公司代码仓库慎用**（代码片段会流向 ChatGPT）。

### 安装

请帮我完整安装并配置 Codex with ChatGPT，全程自动，我是不懂技术的小白，
所有事情你自己做：

1. 环境自检：需要 git 和 Node.js ≥ 20，缺什么就自动安装
    （macOS 用 Homebrew，Windows 用 winget），同时安装 cloudflared。
2. 下载：把 https://github.com/XiaoDuoYa/codex-with-chatgpt 克隆到
   ~/codex-with-chatgpt（已存在就 git pull 更新）。
3. 构建：在该目录里执行 corepack pnpm install 和 corepack pnpm build。
4. 安装 Skill：把仓库里的 skill/SKILL.md 复制到
   ~/.codex/skills/codex-with-chatgpt/SKILL.md，并把文件中
   "The codex-with-chatgpt checkout lives at:" 那一行的路径改成实际克隆路径。
5. 首次配置：按 SKILL.md 里的 first-time setup 流程执行
    （运行 c2c setup，用内置浏览器打开 ChatGPT 配置连接器并输入配对码）。
   全程只用内置浏览器，禁止打开任何第三方浏览器。
6. 只有遇到需要我登录（ChatGPT / Cloudflare）、验证码或两步验证时才叫我，
   而且一次只告诉我一个动作。
7. 完成后给我看 ✓ 清单，并确认文件读取测试通过。我不懂 MCP、OAuth、
   Tunnel、端口这些词，不要向我解释；出了问题先自己修。







