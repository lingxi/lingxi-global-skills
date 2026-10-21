# 灵析 Global · AI 助手插件

[English](README.md) · [繁體中文](README.zh-HK.md) · **简体中文**

在你的 AI 助手（Claude、Codex 及其他支持 MCP 的助手）中，直接处理**灵析 Global**（香港 / 国际版）机构的工作。只要说出你的需要：

- “列出这个月的活动和报名人数。”
- “建立一份义工招募表，收集姓名、手机号码和想参加的场次，先存为草稿。”
- “帮我找陈先生的联系方式。”

AI 助手会一步步引导你，每一步都告诉你当前的状态，并且只会做你的灵析账号有权限做的事。

## 安装：最简单的方法

把这句话发给你的 AI 助手：

> 请阅读 https://mcp.lxi.hk/mcp/install.md ，帮我安装灵析 Global。

它会按你使用的工具完成安装，并带你完成登录。

## 手动安装

| 你使用的工具 | 安装方法 |
| --- | --- |
| **Claude Code** | `claude plugin install lingxi-global --marketplace lingxi/lingxi-global-skills` |
| **Claude 桌面版 / 网页版** | 自定义 → 插件 → 添加 → 添加市场 → 输入 `lingxi/lingxi-global-skills` → 安装 **LingXi Global** |
| **Codex、Cursor 等其他助手** | 执行 `npx skills add lingxi/lingxi-global-skills`，再在工具的 MCP 设置中添加 `https://mcp.lxi.hk/mcp/lingxi` |

如果你的 Claude Code 版本不支持 `--marketplace`，请改为执行：

```bash
claude plugin marketplace add lingxi/lingxi-global-skills
claude plugin install lingxi-global@lingxi-global
```

## 登录

第一次使用时，AI 助手会请你连接灵析账号：

1. 打开灵析页面后，输入机构后台地址（例如 `yourorg.admin.lxi.hk`）；如果你已在同一浏览器登录后台，这一步会自动跳过。
2. 如有需要，登录机构后台。
3. 确认页面显示的机构和账号无误，然后点“同意授权”。
4. 回到 AI 助手，告诉它“好了”。

## 保持最新

有新版本时，AI 助手会提醒你。如需自动更新：

- **Claude Code**：`/plugin` → Marketplaces → `lingxi-global` → **Enable auto-update**
- **Claude 桌面版**：为 `lingxi-global` 市场开启自动同步

立即更新：执行 `claude plugin update lingxi-global@lingxi-global`，或对 AI 助手说：“请阅读 https://mcp.lxi.hk/mcp/install.md ，帮我更新灵析 Global。”

## 你的数据与权限

- AI 助手使用你自己的灵析账号和权限，看不到也改不了你没有权限的内容。
- 建立、发布、提交审核和代填答卷前，都会先征得你的确认；机构的审批流程照常适用。
- 你可以随时在 AI 助手的连接器或 MCP 设置中断开连接。

## 更多

- 关于灵析 MCP：https://mcp.lxi.hk/mcp/introduction
- 各版本更新内容：[CHANGELOG](plugins/lingxi-global/skills/lingxi-global/CHANGELOG.md)
