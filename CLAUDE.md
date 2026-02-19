# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a WeChat Official Account (微信公众号) AI content creation pipeline. It uses Claude Code's custom agent system to orchestrate 5 specialized subagents into a sequential article production workflow. There is no traditional application code — the project consists entirely of agent prompt definitions and orchestration logic.

## Architecture

The pipeline follows a strict sequential flow:

```
Research → Outline → Write → Review (content-editor + humanizer) → Save → Cover Image → Publish
```

### Custom Agents (`.claude/agents/`)

| Agent | File | Role |
|-------|------|------|
| `ai-research-analyst` | `ai-research-analyst.md` | Searches Chinese & English web sources, produces a structured research report |
| `wechat-outline-planner` | `wechat-outline-planner.md` | Transforms research into a mobile-friendly article outline |
| `wechat-tech-writer` | `wechat-tech-writer.md` | Writes the full article from research + outline |
| `wechat-content-editor` | `wechat-content-editor.md` | Review: accuracy, readability, formatting, SEO |
| `humanizer` | `humanizer.md` | Review: removes AI writing patterns, makes text sound natural and human |
| `baoyu-cover-image` | `baoyu-cover-image/SKILL.md` | Generates article cover images with 5-dimensional customization (type, palette, rendering, text, mood) |
| `baoyu-post-to-wechat` | `baoyu-post-to-wechat/SKILL.md` | Publishes articles to WeChat Official Account via API or Chrome CDP browser automation |

All agents use `model: opus` and `memory: project` (except `humanizer` which uses default settings). Each agent with project memory has its own persistent memory directory under `.claude/agent-memory/<agent-name>/`.

## Orchestration Workflow

You are the orchestration agent. When the user provides a topic, coordinate the 5 subagents sequentially. Replace `${topic}` with the user's topic and `${styleDesc}` with the requested style (default: 深入浅出的技术科普).

### Step 1: Research (ai-research-analyst)
Launch the researcher agent with the topic. It will perform bilingual web searches and return a structured report.

Prompt: "请研究主题：${topic}，搜索最新的中英文资料，整理出核心概念、最新进展、实用案例和技术要点。"

### Step 2: Outline (wechat-outline-planner)
Pass the full research report to the outliner agent.

Prompt: "基于以下研究报告，创建一篇${styleDesc}风格的微信公众号文章大纲：\n\n{研究报告内容}"

### Step 3: Write (wechat-tech-writer)
Pass both the research report and outline to the writer agent.

Prompt: "基于以下研究报告和大纲，撰写一篇完整的微信公众号文章。\n\n## 研究报告\n{研究报告}\n\n## 文章大纲\n{大纲内容}"

### Step 4: Review (wechat-content-editor + humanizer)
This step uses two review agents **in parallel** for efficiency:

**4a: Content Review (wechat-content-editor)**
Pass the full article for formatting, accuracy, readability, and SEO review.

Prompt: "请审核并优化以下微信公众号文章，确保质量达到发布标准，直接返回优化后的完整文章：\n\n{文章内容}"

**4b: Humanizer Review (humanizer)**
Pass the same article to detect and flag AI writing patterns.

Prompt: "请审查以下微信公众号文章中的 AI 写作痕迹，识别需要修改的具体位置和模式，返回修改建议列表：\n\n{文章内容}"

After both agents complete, apply the humanizer's suggestions to the content editor's optimized version to produce the final article. If there are conflicts, prioritize humanizer's suggestions on language naturalness while keeping content editor's formatting fixes.

### Step 5: Save
Create a date-prefixed directory under `./output/` and save all artifacts there:

```
./output/yyyy-mm-dd-文章标题/
├── research.md          # Step 1 research report
├── outline.md           # Step 2 article outline
├── article.md           # Final reviewed article (main deliverable)
└── cover.png            # Step 6 cover image (generated after save)
```

- Directory name format: `yyyy-mm-dd-简短标题` (e.g., `2026-02-19-OpenClaw安装指南`)
- Use today's date and a concise title derived from the topic
- Save all intermediate artifacts (research, outline) alongside the final article

### Step 6: Cover Image (baoyu-cover-image + chrome-mcp-server)
After the article is saved, generate a cover image. This step has两个阶段：先用 baoyu-cover-image 生成 prompt，再用 chrome-mcp-server 通过 ChatGPT 生成图片。

**⚠️ 重要：chrome-mcp-server 浏览器连接须知**
- 必须使用 chrome-mcp-server（而非 chrome-devtools）与 ChatGPT 交互
- chrome-mcp-server 通过 Chrome 扩展连接到**用户现有的浏览器实例**，不要用 `chrome_navigate` 的 `newWindow: true` 开启新的隔离浏览器窗口——隔离窗口没有安装 Chrome 扩展，chrome-mcp-server 无法连接
- 现有浏览器保持了 OpenAI、微信公众号等网站的登录状态，直接在现有窗口中打开新标签页即可

#### 6a: 生成 Prompt (baoyu-cover-image)

Use the Skill tool to invoke:

```
/baoyu-cover-image ./output/yyyy-mm-dd-标题/article.md --quick
```

- Use `--quick` to skip interactive confirmation and auto-select dimensions based on article content
- This will generate `prompts/cover.md` in the output directory

#### 6b: 通过 ChatGPT 生成图片 (chrome-mcp-server)

1. **读取 prompt 文件**：读取 `./output/yyyy-mm-dd-标题/prompts/cover.md`，将其中的视觉描述、色彩方案、构图要求等转化为一条英文 image generation prompt
2. **导航到 ChatGPT**：使用 `chrome_navigate` 打开 `https://chatgpt.com/`
3. **发送 prompt**：使用 `chrome_read_page` 定位输入框，通过 `chrome_computer` (type + key Return) 发送生成图片的 prompt
4. **等待生成**：`chrome_computer` (wait 30s)，然后 `chrome_read_page` 确认出现 "图片已创建" / "已生成图片" 等元素
5. **保存图片**：使用 `chrome_screenshot` 通过 CSS selector 截取生成的图片元素并保存为 PNG：
   ```
   chrome_screenshot(selector='img[alt="已生成图片"]', savePng=true, name='cover')
   ```
6. **移动文件**：`chrome_screenshot` 会将文件保存到 `~/Downloads/`，由于 macOS TCC 权限限制，无法直接用 `cp` 访问 Downloads 目录。必须使用 AppleScript 移动文件：
   ```bash
   osascript -e 'tell application "Finder" to move POSIX file "/Users/bruce/Downloads/{截图文件名}.png" to POSIX file "./output/yyyy-mm-dd-标题/" with replacing'
   ```
   然后重命名为 `cover.png`：
   ```bash
   mv "./output/yyyy-mm-dd-标题/{截图文件名}.png" "./output/yyyy-mm-dd-标题/cover.png"
   ```

#### 已知限制

- `chrome_javascript` 提取的图片 URL 和 base64 数据会被 chrome-mcp-server **自动脱敏 (redacted)**，无法直接获取图片原始数据
- ChatGPT 的 "下载此图片" 按钮点击后 `chrome_handle_download` 经常超时，不可靠
- JS 触发的 `a.click()` 下载方式同样不可靠
- **可靠方案是 `chrome_screenshot` + `osascript` 移动文件**

### Step 7: Publish to WeChat (baoyu-post-to-wechat)

文章和封面图完成后，将文章发布到微信公众号草稿箱。

#### 发布方式

| 方式 | 速度 | 可靠性 | 要求 |
|------|------|--------|------|
| `api`（**推荐**） | 快（10 秒内） | 高 | 需要 WECHAT_APP_ID、WECHAT_APP_SECRET 和 IP 白名单 |
| `browser` | 慢 | **低，不推荐** | 微信编辑器已迁移为 ProseMirror，剪贴板粘贴和 DOM 注入均不可靠 |

#### API 发布流程（推荐）

**前置配置（仅首次需要）：**

1. **获取 API 凭证**：在微信公众号后台 → 设置与开发 → 基本配置，获取 AppID 和 AppSecret
2. **配置 IP 白名单**：同一页面，点击"IP白名单"→"配置"，添加本机公网 IP（API 报错 `40164` 时会提示具体 IP）
3. **保存凭证到 `.baoyu-skills/.env`**：
   ```
   WECHAT_APP_ID=your_app_id
   WECHAT_APP_SECRET=your_app_secret
   ```
4. **配置 EXTEND.md**（`.baoyu-skills/baoyu-post-to-wechat/EXTEND.md`）：
   ```yaml
   default_theme: grace
   default_publish_method: api
   default_author: 布鲁
   need_open_comment: 1
   only_fans_can_comment: 0
   ```

**发布命令：**

```bash
npx -y bun .claude/agents/baoyu-post-to-wechat/scripts/wechat-api.ts \
  "./output/yyyy-mm-dd-标题/article.md" \
  --theme grace \
  --author "布鲁" \
  --cover "./output/yyyy-mm-dd-标题/cover.png" \
  --title "文章标题"
```

**关键参数说明：**
- `--title`：必须手动指定，脚本自动提取的标题可能不准确
- `--cover`：封面图路径，使用 Step 6 生成的 cover.png
- `--theme`：HTML 渲染主题（default / grace / simple）
- `--author`：作者名

**API 工作流程：**
1. 渲染 Markdown → HTML（使用指定主题）
2. 获取 access_token（AppID + AppSecret）
3. 上传文章内图片到微信 CDN，替换 HTML 中的 src
4. 上传封面图，获取 thumb_media_id
5. 调用 `/cgi-bin/draft/add` 创建草稿

**常见问题：**
- **`40164` invalid ip**：本机公网 IP 未加入白名单，需在公众号后台 → 基本配置 → IP白名单中添加
- **`40001` invalid credential**：AppSecret 错误或已过期，需在公众号后台重新生成
- **标题提取不准**：使用 `--title` 参数手动指定正确标题

#### Browser 发布方式（不推荐）

微信公众号编辑器已从传统 UEditor 迁移为 ProseMirror（`div.ProseMirror[contenteditable="true"]`），导致以下问题：
- UEditor API（`UE.getEditor('js_editor').setContent()`）调用报错 "not import language file"
- 系统剪贴板粘贴（Cmd+V）无法被 ProseMirror 正确接收
- DOM innerHTML 注入和 JS paste event 模拟均不可靠

如必须使用浏览器方式，需通过 chrome-mcp-server 连接**用户现有浏览器**（已登录微信公众号后台），不要开启新的隔离浏览器实例。

#### Markdown 转 HTML

发布前需将 markdown 转为微信公众号兼容的 HTML。技能内置了转换引擎（`scripts/md-to-wechat.ts`），支持 3 种主题：
- `default`：经典主题，标题居中带底边，二级标题白字彩底
- `grace`：优雅主题，文字阴影，圆角卡片，精致引用块
- `simple`：简洁主题，现代极简风，不对称圆角，清爽留白

## Key Rules

- Each step must wait for the previous step to complete (except Step 4a and 4b which run in parallel)
- Pass the complete output of each step to the next
- The final saved file must incorporate both reviewers' feedback
- Save all intermediate artifacts (research, outline) to the output directory
- After saving, report the directory path and a brief creation summary
- Cover image is the final step of content creation; if it fails, the article is still complete and usable
- Publishing (Step 7) is optional — only execute when the user explicitly requests publishing to WeChat
- When publishing, ensure cover.png exists before calling the publish skill

## WeChat Formatting Standards (enforced across agents)

- Use `##` headings minimum (never `#`)
- No tables, HTML tags, or image links
- Paragraphs ≤ 3-4 lines (mobile-friendly)
- Add spaces between Chinese and English text / numbers
- Use `>` blockquotes for key insights
- Target article length: 3000-5000 Chinese characters
