# miles-rules

个人 Surge 规则仓库：配置模板 + 自维护规则文件。

## 目录结构

```
miles-rules/
├── oixcloud-template.conf   # 完整 Surge 配置模板（已脱敏，不含真实节点凭据）
├── rules/
│   ├── AI.list              # 自维护 AI 应用分流规则（39 条，含 Muse / Apple Intelligence）
│   ├── AdBlock.list         # 广告拦截（搬运自 dler-io/Rules 快照）
│   ├── Special.list         # 特殊直连规则（搬运自 dler-io/Rules 快照）
│   ├── Media/               # Netflix / Disney+ / YouTube / Spotify 等流媒体规则
│   ├── Apple*.list / Telegram.list / Discord.list / Crypto.list 等  # 各应用规则
│   ├── Domestic*.list / ASN.China.list  # 国内直连（含 GetSomeFries 的 ASN 数据）
│   └── Proxy.list           # 兜底代理规则
├── iptv/
│   └── gather-verified.m3u  # 独立 IPTV 源（2026-09-30 逐条验证，41 条可用）
└── README.md
```

> 说明：`rules/` 下除 `AI.list` 外均为 2026-09-30 从上游搬运的快照（dler-io/Rules 与 VirgilClyne/GetSomeFries）。上游会持续更新，需要刷新时告诉我，我重新拉取覆盖。

## 文件说明

### oixcloud-template.conf

基于 oixCloud 订阅配置整理的完整模板，包含：

- `[General]`：DNS（国内 119.29 / 223.5）、skip-proxy 直连名单、DNS 劫持、监听端口等
- `[Proxy]`：48 个节点占位（`你的服务器地址 / 你的密码 / 你的SNI域名`），填入自己的节点信息即可用
- `[Proxy Group]`：Proxy（总代理）、Domestic（国内优先）、Others（兜底）、AI Suite、AdBlock，以及 Netflix / Disney+ / YouTube / Spotify / Telegram / Discord / Crypto / Steam / TikTok / Apple 全家桶等 20+ 细分策略组
- `[Rule]`：约 80 条，按 AdBlock → Special → 流媒体 → 应用 → Apple → Telegram → miHoYo → 代理/国内 → GEOIP CN → FINAL 的顺序匹配
- `[Host]` / `[URL Rewrite]` / `[Script]` / `[Panel]`：国内域名 DNS 优化、google.cn 跳转、订阅流量面板与解锁检测脚本

**使用前必须替换的占位符：**

| 占位符 | 说明 |
|---|---|
| `你的服务器地址` / `你的密码` / `你的SNI域名` | 填入你自己的节点信息（48 处） |
| `你的控制器密码` | 外部控制器密码，务必用强密码 |
| `你的订阅API地址(需UrlEncode)` | 订阅流量面板的 API 地址 |

> ⚠️ 切勿把填好真实密码的版本推到公开仓库。

### rules/AI.list

自维护的 AI 应用分流规则（RULE-SET 纯列表格式），覆盖：

- Muse（muse.ai，另有社区反馈的 api.meta.ai，未经官方确认）
- ChatGPT / OpenAI、Google Gemini、Claude / Anthropic
- Microsoft Copilot、xAI Grok、Perplexity、Poe、Mistral
- Apple Intelligence / Siri（21 条社区规则）

在配置中引用：

```
RULE-SET,https://cdn.jsdelivr.net/gh/你的用户名/miles-rules@main/rules/AI.list,"AI Suite",extended-matching
```

模板中 `AI Suite` 那条远程规则旁已附好替换注释。

## 与 surge-modules 的关系

- `surge-modules` 仓库放的是 **Surge 模块**（`.sgmodule`，带 `#!name` 头、可直接在 Surge「模块」里安装）
- 本仓库放的是 **配置模板**（`.conf`）和 **RULE-SET 列表**（`.list`，在配置的 `[Rule]` 中引用）

两者互补：模块适合一键安装单功能，RULE-SET 适合编排进完整配置。
