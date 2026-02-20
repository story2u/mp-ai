---
illustration_id: 04
type: infographic
style: blueprint
---

6 Security Principles for OpenClaw - Safety Checklist

Layout: vertical checklist with 6 items, each as a horizontal row with icon, title, and description

ZONES:
- Header: "OpenClaw 安全部署清单" with a shield icon
- Item 1: Shield/user icon. Title: "不用 root 运行". Description: "专用用户 node (UID 1000)". Status indicator: critical.
- Item 2: Lock/network icon. Title: "Gateway 绑定 localhost". Description: "绑定 127.0.0.1, 远程用 Tailscale/Cloudflare Tunnel". Status indicator: critical.
- Item 3: Magnifying glass icon. Title: "审查第三方插件". Description: "Cisco 已发现数据窃取风险". Status indicator: warning.
- Item 4: Folder/lock icon. Title: "保护配置目录". Description: "~/.openclaw/ 含 API Key 和会话数据". Status indicator: critical.
- Item 5: Refresh/update icon. Title: "保持版本更新". Description: "v2026.1.29 已强制 token 认证". Status indicator: important.
- Item 6: Box/sandbox icon. Title: "启用沙箱模式". Description: "Docker 中隔离 Agent 操作". Status indicator: recommended.
- Footer: Diagnostic command callout: "openclaw doctor --deep"

LABELS: "UID 1000", "127.0.0.1", "Tailscale", "Cloudflare Tunnel", "~/.openclaw/", "v2026.1.29", "token 认证", "Cisco AI 安全报告", "openclaw doctor --deep"

COLORS: Deep navy background (#1B2838), critical items border in red (#EF5350), warning items border in amber (#FFB300), recommended items border in teal (#26A69A), item backgrounds in slightly lighter navy (#243447), shield icon in cyan (#00D4FF), white text (#FFFFFF), checklist checkmarks in green (#66BB6A), footer command in monospace cyan

STYLE: Technical blueprint schematic style. Fine grid lines on background. Each checklist item is a horizontal panel with left icon zone, center text zone, and right status indicator. Icons are geometric and simplified (shield, lock, magnifying glass, folder, refresh arrow, box). Thin technical border lines. Monospace font for commands and paths. Clean, structured security-document aesthetic.

Clean composition with generous white space between items. Simple grid background. Items vertically stacked with consistent spacing.

Text should be large and prominent. Keep minimal, focus on the action and key technical detail per item.

ASPECT: 9:16
