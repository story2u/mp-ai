# 如何安装 OpenClaw 研究报告

> 报告生成时间：2026-02-19 | 数据来源：中英文综合检索

### 核心概念

- **OpenClaw 是什么**：OpenClaw（前身为 Clawdbot 和 Moltbot）是一款免费开源的自主 AI 智能体（Autonomous AI Agent），由奥地利开发者 Peter Steinberger 创建。它不是一个新的 AI 模型，而是一个开源软件平台，通过接入大语言模型（如 Claude、GPT、DeepSeek 等）来执行实际任务。用户可以通过 WhatsApp、Telegram、Discord、飞书、钉钉等日常聊天工具与之交互，让它帮助处理邮件、管理日历、编写代码、操作文件系统，甚至控制智能家居。OpenClaw 采用"本地优先、隐私可控"的设计理念，所有数据由用户完全掌控。

- **关键术语解释**：

| 术语 | 英文 | 解释 |
|------|------|------|
| 网关（Gateway） | Gateway | OpenClaw 的核心组件，一个始终在后台运行的进程，负责连接聊天平台与 AI 模型，管理会话、通道和事件 |
| 智能体（Agent） | Agent | 驱动思考的部分，接入 LLM 处理上下文记忆与逻辑推理，实际执行用户指令 |
| 技能（Skills） | Skills | 可扩展的功能模块，使 OpenClaw 能执行网页调研、浏览器自动化、邮箱操作等任务。内置 49 个 + 官方 93 个 + 社区 1715+ 个 |
| 引导向导（Onboarding Wizard） | Onboarding Wizard | 首次安装后的交互式配置流程，引导用户设置 API Key、通信渠道和技能插件 |
| 守护进程（Daemon） | Daemon | 通过 launchd（macOS）或 systemd（Linux）注册的后台服务，确保 Gateway 持续运行 |

---

### 最新进展

- **2026 年 2 月 14 日**：创始人 Peter Steinberger 宣布加入 OpenAI，OpenClaw 项目将转移至一个独立的开源基金会管理。项目在 GitHub 上已获得超过 200,000 个 star 和 35,000+ 个 fork，维持 MIT 开源许可证不变。[来源：NxCode 完整指南](https://www.nxcode.io/resources/news/openclaw-complete-guide-2026)

- **2026 年 1 月 29 日**：发布重大安全更新版本 v2026.1.29，**永久移除了 `auth: "none"` 配置选项**，强制要求所有实例使用 token 或密码认证。此举源于 Shodan 发现大量暴露在公网的未保护实例，其中包含未加密的 API 密钥和凭据。[来源：NxCode](https://www.nxcode.io/resources/news/openclaw-complete-guide-2026)

- **2026 年 1 月 27-30 日**：因 Anthropic 的商标投诉，项目从 Clawdbot 更名为 Moltbot，三天后再更名为 OpenClaw。更名期间出现诈骗者抢注废弃的 GitHub/Twitter 账号并在 Solana 上发行假冒代币的事件。[来源：Wikipedia - OpenClaw](https://en.wikipedia.org/wiki/OpenClaw)

- **2026 年 2 月**：Cisco AI 安全研究团队发布测试报告，发现第三方 OpenClaw 技能存在数据窃取和提示注入风险，推动社区推出 Cisco Skill Scanner 安全扫描工具。[来源：Help Net Security](https://www.helpnetsecurity.com/2026/02/12/openclaw-scanner-open-source-tool-detects-autonomous-ai-agents/)

- **2025 年 11 月**：项目以 Clawdbot 名称首次发布，72 小时内获得 60,000+ GitHub star，一周内突破 100,000 star，成为开源历史上增长最快的项目之一。[来源：DigitalOcean](https://www.digitalocean.com/resources/articles/what-is-openclaw)

---

### 实用案例

**案例一：个人 AI 助手 -- 日常任务自动化**

- **场景描述**：用户通过 Telegram 或 WhatsApp 向 OpenClaw 发送自然语言指令，让其帮助管理日常事务。
- **技术方案**：在本地设备或云服务器上部署 OpenClaw Gateway，接入 Claude 或 GPT 模型作为大脑，通过 Telegram Bot 作为交互通道，启用邮件、日历和文件管理相关 Skills。
- **实际效果**：用户可以让 AI 发送和总结邮件、管理日历日程、检查航班状态并自动签到、整理文件系统。OpenClaw 支持 Heartbeat 系统和 Cron 定时任务，能在用户不主动发消息的情况下自动执行后台任务（如舆情监控、定时提醒）。[来源：DigitalOcean](https://www.digitalocean.com/resources/articles/what-is-openclaw)、[知乎 OpenClaw 工作原理解析](https://zhuanlan.zhihu.com/p/2002719503394567324)

**案例二：开发者工作流集成**

- **场景描述**：开发者通过聊天平台与 OpenClaw 交互，让其辅助代码开发与运维。
- **技术方案**：部署 OpenClaw 并启用 Shell 执行、Git 操作、Sentry 错误追踪等 Skills，配合 Claude Code 或 Codex 会话进行代码生成。
- **实际效果**：开发者可以让 OpenClaw 自主运行测试、通过 Sentry 捕获并定位错误、自动创建 Pull Request 修复问题。这极大减少了从发现 Bug 到修复的响应时间。[来源：Scientific American](https://www.scientificamerican.com/article/moltbot-is-an-open-source-ai-agent-that-runs-your-computer/)

**案例三：国内用户接入飞书/钉钉**

- **场景描述**：国内企业和个人用户希望通过飞书或钉钉使用 AI 助手。
- **技术方案**：部署 OpenClaw 后，通过社区开发的飞书/钉钉通道插件进行接入。可选用国产模型（如智谱 GLM、DeepSeek）降低 API 成本。汉化版（[OpenClawChineseTranslation](https://github.com/1186258278/OpenClawChineseTranslation)）提供 CLI 和 Dashboard 全中文支持。
- **实际效果**：国内用户可以在熟悉的办公软件中直接使用 AI 助手功能，且数据存储在自有服务器上，满足国内数据合规需求。API 成本方面，使用国产模型后可大幅降低，轻度使用每月约 $10-30，重度自动化约 $70-150。[来源：腾讯云开发者社区](https://cloud.tencent.com/developer/article/2626160)、[阿里云开发者社区](https://developer.aliyun.com/article/1711121)

---

### 技术要点

#### 一、系统要求

| 平台 | 要求 |
|------|------|
| **运行时** | Node.js >= 22（必须） |
| **macOS** | 原生支持，推荐平台 |
| **Linux** | 原生支持，Ubuntu 兼容性最佳 |
| **Windows** | 强烈推荐通过 WSL2 运行，原生支持有限 |
| **硬件** | 普通电脑即可，OpenClaw 本身不运行模型（依赖外部 API）|

验证 Node.js 版本：
```bash
node -v
# 应显示 v22.x.x 或更高
```

如需安装/升级 Node.js：
```bash
nvm install 22 && nvm use 22
```

#### 二、安装方法（4 种）

**方法 1：一键安装脚本（推荐新手使用）**

macOS / Linux：
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

Windows PowerShell：
```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

脚本会自动检测操作系统、检查 Node.js 版本、安装 CLI 并启动引导向导（Onboarding Wizard）。

**方法 2：手动 npm 安装**

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

`--install-daemon` 参数会将 OpenClaw 注册为后台服务（macOS 使用 launchd，Linux 使用 systemd）。

注意：如果安装后终端提示 `openclaw: command not found`，通常是 npm 全局安装目录未添加到 PATH 环境变量。

**方法 3：Docker 部署**

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
docker compose up -d
```

Docker 会挂载两个卷：`~/.openclaw`（配置和凭据）和 `~/openclaw/workspace`（智能体的工作沙箱）。

**方法 4：从源码构建（开发者用）**

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
pnpm install
pnpm ui:build
pnpm build
pnpm openclaw onboard --install-daemon
```

构建推荐使用 pnpm。Bun 可选用于直接运行 TypeScript。

#### 三、初始化配置（Onboarding Wizard）

安装完成后会自动进入（或手动运行 `openclaw onboard`），配置流程包括：

1. **安全确认**：确认了解实验性软件风险
2. **选择安装模式**：QuickStart（快速开始）或 Advanced（高级配置）
3. **配置 LLM 提供商**：支持 Anthropic Claude、OpenAI GPT、Google Gemini、DeepSeek、MiniMax 等
4. **输入 API Key**：在对应提供商的控制台创建并复制 API Key
5. **选择默认模型**：推荐 Sonnet 4.5（性价比最优）
6. **配置通信渠道**：选择 Telegram、WhatsApp、Discord 等（可跳过，后续配置）
7. **Skills 安装**：可选安装功能扩展插件
8. **完成注册守护进程**

#### 四、常用命令速查

| 命令 | 功能 |
|------|------|
| `openclaw onboard` | 初始化/重新配置向导 |
| `openclaw dashboard` | 打开 Web 控制台（浏览器中聊天）|
| `openclaw tui` | 终端全屏聊天界面 |
| `openclaw status` / `openclaw status --all` | 检查运行状态/完整诊断 |
| `openclaw doctor` | 自动诊断并修复问题 |
| `openclaw gateway start` | 后台启动网关 |
| `openclaw gateway restart` | 重启网关 |
| `openclaw gateway status` | 查看网关连接状态 |
| `openclaw logs --follow` | 实时查看日志 |
| `openclaw models status` | 检查模型配置与连接 |
| `openclaw update` | 检查并更新 CLI |
| `openclaw channels login <platform>` | 登录通信渠道 |
| `openclaw channels logout <platform>` | 登出通信渠道 |
| `openclaw memory search <keyword>` | 搜索 AI 记忆 |
| `openclaw --version` | 查看当前版本 |

#### 五、常见问题与排错

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| `openclaw: command not found` | npm 全局路径未加入 PATH | 检查 `npm prefix -g`，将其 `bin` 目录加入 `~/.bashrc` 或 `~/.zshrc` 的 PATH |
| Node.js 版本不兼容 | 版本低于 22 | `nvm install 22 && nvm use 22` |
| Gateway 启动失败 / 端口占用 | 端口 18789 被其他进程占用 | `lsof -i :18789` 查找占用进程，或用 `openclaw gateway --port 18790` 换端口 |
| API Key 无效 / 401 错误 | Key 过期、被撤销或权限不足 | 在提供商控制台重新生成 Key，确认有足够额度 |
| WhatsApp QR 扫码失败 | 登录状态异常 | 先 `openclaw channels logout whatsapp`，再重新 `login`，确保手机和电脑在同一网络 |
| Telegram Bot 不回复 | 配对码未审批 | `openclaw pairing list telegram` 查看，`openclaw pairing approve telegram [CODE]` 审批 |
| 会话频繁超时 | 网络不稳定或超时配置过短 | 检查网络，在配置中增加超时时间，确认网关运行中 |
| 配置修改不生效 | 未重启 Gateway | 修改 `~/.openclaw/openclaw.json` 后必须 `openclaw gateway restart` |

**重置所有配置**（谨慎操作）：
```bash
# 先备份
cp -r ~/.openclaw ~/.openclaw.backup
# 删除配置目录
rm -rf ~/.openclaw
# 重新初始化
openclaw onboard --install-daemon
```

#### 六、安全建议（重要）

OpenClaw 是实验性软件，官方明确声明 **"It is not secure"**。以下是关键安全建议：

1. **不要在个人主力设备上安装**：推荐使用独立设备、VPS 或虚拟机
2. **永远不要以 root 用户运行**
3. **绑定到 localhost**：将 `openclaw.json` 中 Gateway 地址从 `0.0.0.0` 改为 `127.0.0.1`
4. **审查社区插件**：使用 Cisco Skill Scanner 扫描第三方 Skills，已有恶意插件被发现执行数据窃取
5. **保护 `~/.openclaw` 目录**：视其为密码保管库，不要随意分享
6. **不要将 Control UI 暴露在公网**
7. **启用执行审批**：对高风险操作（如 Shell 命令）启用确认机制

#### 七、优缺点分析

| 优点 | 缺点 |
|------|------|
| 完全开源免费（MIT 协议） | 实验性软件，安全风险较高 |
| 本地运行，数据自主可控 | 安装配置对非技术用户门槛较高 |
| 模型无关，支持多种 LLM | Windows 原生支持不佳，需 WSL2 |
| 50+ 消息平台集成 | 社区插件缺乏充分安全审查 |
| 1800+ Skills 生态丰富 | 持续的 API 调用产生费用（$10-150/月）|
| 支持定时自动化任务 | 创始人已加入 OpenAI，项目未来方向待观察 |
| 持久记忆，跨会话个性化 | 配置不当可能泄露敏感数据 |

---

### 参考资料

[1] OpenClaw Official Documentation - Getting Started - https://docs.openclaw.ai/start/getting-started

[2] How to Install OpenClaw (2026): The Complete Step-by-Step Guide - Medium - https://medium.com/@guljabeen222/how-to-install-openclaw-2026-the-complete-step-by-step-guide-516b74c163b9

[3] How to install OpenClaw on macOS, Linux, and Windows - BitLaunch - https://bitlaunch.io/blog/install-configure-openclaw/

[4] OpenClaw Complete Guide 2026 - NxCode - https://www.nxcode.io/resources/news/openclaw-complete-guide-2026

[5] What is OpenClaw? Your Open-Source AI Assistant for 2026 - DigitalOcean - https://www.digitalocean.com/resources/articles/what-is-openclaw

[6] OpenClaw - Wikipedia - https://en.wikipedia.org/wiki/OpenClaw

[7] GitHub - openclaw/openclaw - https://github.com/openclaw/openclaw

[8] OpenClaw（原 ClawdBot/Moltbot）下载安装使用详细图文教程 - Apifox - https://apifox.com/apiskills/openclaw-installation-and-usage-guide/

[9] 手把手教你安装 OpenClaw 并接入飞书 - 腾讯云开发者社区 - https://cloud.tencent.com/developer/article/2626160

[10] Windows 也能跑 OpenClaw！最完整安装教程 + 飞书接入 - 阿里云开发者社区 - https://developer.aliyun.com/article/1711121

[11] OpenClaw 汉化版 - GitHub - https://github.com/1186258278/OpenClawChineseTranslation

[12] awesome-openclaw-tutorial 最全中文教程 - GitHub - https://github.com/xianyu110/awesome-openclaw-tutorial

[13] OpenClaw Troubleshooting: Common Issues & Solutions - https://open-claw.me/guide/troubleshooting

[14] OpenClaw Scanner: Open-source tool detects autonomous AI agents - Help Net Security - https://www.helpnetsecurity.com/2026/02/12/openclaw-scanner-open-source-tool-detects-autonomous-ai-agents/

[15] OpenClaw is an open-source AI agent that runs your computer - Scientific American - https://www.scientificamerican.com/article/moltbot-is-an-open-source-ai-agent-that-runs-your-computer/

[16] 目前最详细的 OpenClaw 工作原理解析 - 知乎 - https://zhuanlan.zhihu.com/p/2002719503394567324

[17] OpenClaw (Clawdbot) 教程 - 菜鸟教程 - https://www.runoob.com/ai-agent/openclaw-clawdbot-tutorial.html

[18] OpenClaw Review 2026 - CyberNews - https://cybernews.com/ai-tools/openclaw-review/

[19] OpenClaw Tutorial: Installation to First Chat Setup - Codecademy - https://www.codecademy.com/article/open-claw-tutorial-installation-to-first-chat-setup
