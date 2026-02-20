# 开源 AI 管家 OpenClaw 安装指南 研究报告

> 报告生成时间：2026-02-20 | 数据来源：中英文综合检索（5 组关键词，8 个权威来源）

---

### 核心概念

OpenClaw（原名 Clawdbot / Moltbot）是一个免费开源的 **AI 自主智能体平台**（Autonomous AI Agent Platform），由奥地利开发者 Peter Steinberger 于 2025 年 11 月创建。它不是一个 AI 模型，而是一个"连接器 + 执行器"——将大语言模型的理解能力转化为实际操作能力，通过用户日常使用的聊天平台（WhatsApp、Telegram、飞书、钉钉等）接收指令，再调用各类技能插件执行具体任务。

项目采用 MIT 开源协议，核心理念是 **"本地优先、隐私可控"**：所有配置数据和交互历史存储在用户自己的设备上，不依赖云端托管。

**关键术语中英文对照：**

| 中文术语 | 英文术语 | 解释 |
|---------|---------|------|
| 智能体/Agent | Autonomous AI Agent | 能自主执行任务的 AI 程序，不仅回答问题，还能操作系统、收发邮件、管理文件 |
| 网关 | Gateway | OpenClaw 的核心服务组件，监听 18789 端口，负责路由消息和调度任务 |
| 技能插件 | Skills | 扩展 OpenClaw 功能的模块化插件，社区已贡献 1,700+ 个 |
| 引导向导 | Onboarding Wizard | 安装后的交互式配置流程，引导用户完成 LLM 选择、通道配置等 8 个步骤 |
| 守护进程 | Daemon | 后台持续运行的服务进程，确保系统重启后 OpenClaw 自动上线（macOS 用 launchd，Linux 用 systemd） |
| 沙箱 | Sandbox | Docker 隔离执行环境，用于安全运行非主会话中的工具命令 |

---

### 最新进展

**2026 年 2 月 14 日 -- 创始人加入 OpenAI，项目转入独立基金会**

Peter Steinberger 宣布加入 OpenAI，同时将 OpenClaw 项目移交给独立的开源基金会管理。项目继续保持 MIT 协议，独立于 OpenAI 运营，治理模式参照 Linux 基金会和 Kubernetes 基金会的成熟框架。[来源：NxCode]

**2026 年 2 月初 -- GitHub Stars 突破 19 万**

截至 2026 年 2 月 2 日，OpenClaw 在 GitHub 上获得超过 140,000 Star 和 20,000 Fork。到 2 月中旬，Star 数已攀升至 190,000+，首周访问量达 200 万。这使其成为 GitHub 历史上增长最快的开源项目之一。[来源：NxCode、Wikipedia]

**2026 年 1 月 29 日 -- v2026.1.29 安全更新（Breaking Change）**

该版本永久移除了 `auth: "none"` 选项，强制所有实例使用 token 或密码认证。npm 包从 `moltbot` 更名为 `openclaw`，扩展包作用域从 `@moltbot/*` 迁移到 `@openclaw/*`。[来源：NxCode]

**2026 年 1 月 27-30 日 -- 三次更名风波**

- 1 月 27 日：因 Anthropic 商标投诉，从 Clawdbot 更名为 Moltbot
- 1 月 29 日：更名过程中出现 10 秒账号空窗期，被加密货币骗子利用，假冒 $CLAWD 代币在 Solana 上一度达到 1,600 万美元市值后归零
- 1 月 30 日：社区反馈"Moltbot 不好发音"，最终更名为 OpenClaw

[来源：NxCode、CNBC]

**2026 年 2 月 -- Cisco 安全团队发现恶意技能插件**

Cisco AI 安全研究团队测试了第三方 OpenClaw 技能，发现存在 **数据窃取和提示注入**攻击，且技能仓库缺乏充分的安全审查机制。Vectra AI 随后发布了详细的安全分析报告，指出 OpenClaw 的广泛系统权限使其成为新的攻击面。[来源：Vectra AI、CyberNews]

**2025 年 11 月 -- 项目首次发布**

Peter Steinberger 以 Clawdbot 名称首次发布项目，第一天获得 5,000 Star，72 小时内飙升至 60,000 Star，被 TechCrunch 和 Hacker News 重点报道。[来源：NxCode]

---

### 实用案例

**案例一：国内企业通过飞书 + DeepSeek 部署 AI 办公助手**

- **场景描述：** 国内团队需要一个能在飞书群聊中响应的 AI 助手，用于日报整理、会议纪要生成、邮件摘要等日常办公任务。
- **技术方案：** 使用社区项目 OpenClaw-Docker-CN-IM 进行 Docker 一键部署，预装飞书、钉钉、QQ 机器人、企业微信插件。AI 模型选择 DeepSeek，成本亲民。关键配置步骤包括在飞书开放平台创建自建应用、启用长连接模式的事件订阅、订阅 `im.message.receive_v1` 事件。
- **实际效果：** Docker Compose 一条命令启动，无需复杂配置。飞书集成中最常被遗漏的步骤是"启用事件订阅"，这也是社区反馈中最高频的配置问题。

**案例二：开发者使用 Docker 沙箱安全部署 OpenClaw**

- **场景描述：** 个人开发者希望在 VPS 上 24 小时运行 OpenClaw，同时确保安全隔离，防止 AI Agent 的操作影响宿主机。
- **技术方案：** 使用官方 Docker 部署方案，运行 `docker-setup.sh` 一键完成镜像构建、引导配置和 Compose 启动。启用 Agent 沙箱模式（`agents.defaults.sandbox.mode: "non-main"`），使非主会话在独立 Docker 容器中执行。网络策略默认为 "none"，按需开放。推荐 VPS 配置：至少 2 vCPU、4GB RAM。
- **实际效果：** 容器以非 root 用户（node, UID 1000）运行，配置和工作区数据通过卷挂载持久化（`~/.openclaw/` 和 `~/openclaw/workspace/`）。升级只需拉取最新镜像并重启容器。[来源：OpenClaw 官方 Docker 文档]

**案例三：macOS 用户五分钟快速体验**

- **场景描述：** macOS 用户想用最快速度体验 OpenClaw，通过 iMessage 或 Telegram 与 AI 助手对话。
- **技术方案：** 执行一键安装脚本 `curl -fsSL https://openclaw.ai/install.sh | bash`，脚本自动检测系统环境、安装 Node.js 依赖、下载 OpenClaw。随后运行 `openclaw onboard --install-daemon` 完成 8 步引导配置，选择 Anthropic 作为 LLM 提供商（推荐 Sonnet 4.5 模型，性价比最优），配置 Telegram 通道。守护进程通过 launchd 注册，系统重启后自动上线。
- **实际效果：** 从开始安装到发送第一条消息，全程约 5 分钟。月成本根据使用量在 $10-70 之间，轻度使用约 $10-30/月。也可通过 Ollama 运行本地模型实现零成本使用。[来源：OpenClaw 官方文档、Codecademy 教程]

---

### 技术要点

#### 一、系统架构

OpenClaw 的核心架构由以下组件构成：

**Gateway（网关）** -- 核心服务，监听默认端口 18789，负责消息路由、Agent 调度和 API 代理。支持前台模式（`openclaw gateway --port 18789`）和后台守护进程模式。

**Workspace（工作区）** -- 位于 `~/.openclaw/workspace/`，包含 `USER.md`（用户画像）、`MEMORY.md`（持久记忆）等文件，Agent 在此目录下执行文件操作。

**Skills（技能系统）** -- 模块化功能扩展，支持 Markdown 或 TypeScript 编写，可通过 `clawhub install <slug>` 从官方市场安装，也可让 OpenClaw 自主生成新技能。

**Memory（记忆系统）** -- 基于本地 SQLite 数据库（`~/.openclaw/memory/<cid>.sqlite`）的持久化长期记忆，支持向量搜索（`openclaw memory search "X"`）。

#### 二、四种安装方法对比

| 方法 | 命令 | 适合人群 | 优点 | 缺点 |
|------|------|---------|------|------|
| 一键脚本 | `curl -fsSL https://openclaw.ai/install.sh \| bash` | 新手 | 全自动，2-3 分钟完成 | 不透明，难以排查问题 |
| npm 手动安装 | `npm install -g openclaw@latest` | Node.js 开发者 | 掌控感强，易于升级 | 需手动处理 PATH 问题 |
| Docker 部署 | `git clone ... && docker compose up -d` | 运维人员/VPS 部署 | 环境隔离，安全性高 | 需要 Docker 基础知识 |
| 源码构建 | `git clone ... && pnpm install && pnpm build` | 贡献者/深度定制 | 完全控制源码 | 构建时间长，需 pnpm |

#### 三、关键配置文件

| 路径 | 说明 |
|------|------|
| `~/.openclaw/openclaw.json` | 主配置文件（LLM 设置、通道、端口等） |
| `~/.openclaw/credentials/` | API 密钥存储目录 |
| `~/.openclaw/workspace/` | Agent 工作区 |
| `~/.openclaw/skills/` | 全局技能目录 |
| `~/.openclaw/memory/` | 长期记忆 SQLite 数据库 |

#### 四、常见问题与解决方案

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| `openclaw: command not found` | npm 全局路径未加入 PATH | `export PATH="$PATH:$(npm config get prefix)/bin"` 并写入 `~/.zshrc` |
| Node.js 版本过低 | 系统自带 Node 18/20 | `nvm install 22 && nvm use 22` |
| 网关端口 18789 被占用 | 其他服务占用 | 修改 `~/.openclaw/config.json` 中的端口号 |
| API Key 报 401 错误 | Key 复制错误或余额不足 | 检查空格、过期、余额 |
| Docker 权限错误 | 卷挂载目录 UID 不匹配 | `sudo chown -R 1000:1000 /path/to/openclaw-config` |
| 网关未启动 | 多种可能原因 | 运行 `openclaw doctor --deep` 深度诊断 |
| 模型认证过期 | Token 失效 | `openclaw models auth setup-token` 重新认证 |

#### 五、安全最佳实践

根据 Cisco 安全研究团队和 Vectra AI 的分析报告，OpenClaw 部署需严格遵循以下安全原则：

1. **不要以 root 权限运行**：Docker 默认以 node 用户（UID 1000）运行
2. **网关绑定 localhost**：将 Gateway 绑定到 `127.0.0.1`，绝不直接暴露到公网
3. **远程访问用 VPN/隧道**：推荐 Tailscale、ZeroTier 或 Cloudflare Tunnel 进行安全内网穿透
4. **审查第三方技能**：安装社区插件前先评估来源可信度，Cisco 已发现存在数据窃取的恶意插件
5. **保护配置目录**：`~/.openclaw/` 存有 API Key 和会话数据，需严格控制访问权限
6. **使用 token 认证**：v2026.1.29 已强制移除 `auth: "none"`，所有连接必须认证
7. **启用沙箱模式**：在 Docker 部署中开启 Agent 沙箱，隔离非主会话的工具执行
8. **定期轮换凭证**：使用短期 OAuth token 替代长期 API Key，日志保留限制在 7-14 天

#### 六、中国用户特殊方案

| 方案 | 项目地址 | 特点 |
|------|---------|------|
| 汉化版 | MaoTouHU/OpenClawChinese | CLI + Dashboard 全中文界面，每小时自动同步上游 |
| 国内 IM Docker 版 | justlovemaki/OpenClaw-Docker-CN-IM | 预装飞书、钉钉、QQ 机器人、企业微信插件 |
| 国内云部署 | 阿里云/腾讯云一键镜像 | 云轻量级服务器部署，无需本地环境 |

#### 七、使用成本参考

| 使用级别 | 月费用估算 | 说明 |
|---------|----------|------|
| 免费 | $0 | 使用 Ollama 运行本地模型 |
| 轻度使用 | $10-30 | 日常对话、简单任务 |
| 中度使用 | $30-70 | 邮件处理、代码辅助 |
| 重度使用 | $70-150 | 全自动化办公流程 |

---

### 参考资料

[1] Getting Started - OpenClaw Official Documentation - https://docs.openclaw.ai/start/getting-started

[2] Docker Installation - OpenClaw Official Documentation - https://docs.openclaw.ai/install/docker

[3] OpenClaw Complete Guide 2026: Clawdbot to Moltbot to OpenClaw to Steinberger Joins OpenAI - NxCode - https://www.nxcode.io/resources/news/openclaw-complete-guide-2026

[4] OpenClaw (Clawdbot) 教程 - 菜鸟教程 - https://www.runoob.com/ai-agent/openclaw-clawdbot-tutorial.html

[5] From Clawdbot to OpenClaw: When Automation Becomes a Digital Backdoor - Vectra AI - https://www.vectra.ai/blog/clawdbot-to-moltbot-to-openclaw-when-automation-becomes-a-digital-backdoor

[6] OpenClaw-Docker-CN-IM: 中国 IM 平台整合 Docker 版本 - GitHub - https://github.com/justlovemaki/OpenClaw-Docker-CN-IM

[7] OpenClaw GitHub Repository - https://github.com/openclaw/openclaw

[8] OpenClaw - Wikipedia - https://en.wikipedia.org/wiki/OpenClaw

[9] What is OpenClaw? Your Open-Source AI Assistant for 2026 - DigitalOcean - https://www.digitalocean.com/resources/articles/what-is-openclaw

[10] OpenClaw Review 2026: How Does It Work? - CyberNews - https://cybernews.com/ai-tools/openclaw-review/

[11] OpenClaw 开源汉化发行版：介绍、下载、安装、配置教程 - CSDN - https://blog.csdn.net/qq_44866828/details/157876493

[12] OpenClaw Tutorial: Installation to First Chat Setup - Codecademy - https://www.codecademy.com/article/open-claw-tutorial-installation-to-first-chat-setup
