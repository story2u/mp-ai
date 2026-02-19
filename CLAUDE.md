# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a WeChat Official Account (微信公众号) AI content creation pipeline. It uses Claude Code's custom agent system to orchestrate 5 specialized subagents into a sequential article production workflow. There is no traditional application code — the project consists entirely of agent prompt definitions and orchestration logic.

## Architecture

The pipeline follows a strict sequential flow:

```
Research → Outline → Write → Review (content-editor + humanizer) → Save to ./output/yyyy-mm-dd-标题/
```

### Custom Agents (`.claude/agents/`)

| Agent | File | Role |
|-------|------|------|
| `ai-research-analyst` | `ai-research-analyst.md` | Searches Chinese & English web sources, produces a structured research report |
| `wechat-outline-planner` | `wechat-outline-planner.md` | Transforms research into a mobile-friendly article outline |
| `wechat-tech-writer` | `wechat-tech-writer.md` | Writes the full article from research + outline |
| `wechat-content-editor` | `wechat-content-editor.md` | Review: accuracy, readability, formatting, SEO |
| `humanizer` | `humanizer.md` | Review: removes AI writing patterns, makes text sound natural and human |

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
└── article.md           # Final reviewed article (main deliverable)
```

- Directory name format: `yyyy-mm-dd-简短标题` (e.g., `2026-02-19-OpenClaw安装指南`)
- Use today's date and a concise title derived from the topic
- Save all intermediate artifacts (research, outline) alongside the final article

## Key Rules

- Each step must wait for the previous step to complete (except Step 4a and 4b which run in parallel)
- Pass the complete output of each step to the next
- The final saved file must incorporate both reviewers' feedback
- Save all intermediate artifacts (research, outline) to the output directory
- After saving, report the directory path and a brief creation summary

## WeChat Formatting Standards (enforced across agents)

- Use `##` headings minimum (never `#`)
- No tables, HTML tags, or image links
- Paragraphs ≤ 3-4 lines (mobile-friendly)
- Add spaces between Chinese and English text / numbers
- Use `>` blockquotes for key insights
- Target article length: 3000-5000 Chinese characters
