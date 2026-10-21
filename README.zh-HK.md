# 靈析 Global · AI 助手插件

[English](README.md) · **繁體中文** · [简体中文](README.zh-CN.md)

在你的 AI 助手（Claude、Codex 及其他支援 MCP 的助手）中，直接處理**靈析 Global**（香港 / 國際版）機構的工作。只要說出你的需要：

- 「列出這個月的活動和報名人數。」
- 「建立一份義工招募表，收集姓名、手提電話和想參加的場次，先存為草稿。」
- 「幫我找陳先生的聯絡方式。」

AI 助手會一步步引導你，每一步都告訴你目前的狀態，並只會做你的靈析帳號有權限做的事。

## 安裝：最簡單的方法

把這句話傳給你的 AI 助手：

> 請閱讀 https://mcp.lxi.hk/mcp/install.md ，幫我安裝靈析 Global。

它會按你使用的工具完成安裝，並帶你完成登入。

## 手動安裝

| 你使用的工具 | 安裝方法 |
| --- | --- |
| **Claude Code** | `claude plugin install lingxi-global --marketplace lingxi/lingxi-global-skills` |
| **Claude 桌面版 / 網頁版** | 自訂 → 插件 → 新增 → 新增市場 → 輸入 `lingxi/lingxi-global-skills` → 安裝 **LingXi Global** |
| **Codex、Cursor 等其他助手** | 執行 `npx skills add lingxi/lingxi-global-skills`，再在工具的 MCP 設定中新增 `https://mcp.lxi.hk/mcp/lingxi` |

如果你的 Claude Code 版本不支援 `--marketplace`，請改為執行：

```bash
claude plugin marketplace add lingxi/lingxi-global-skills
claude plugin install lingxi-global@lingxi-global
```

## 登入

第一次使用時，AI 助手會請你連接靈析帳號：

1. 開啟靈析頁面後，輸入機構後台地址（例如 `yourorg.admin.lxi.hk`）；如果你已在同一瀏覽器登入後台，這一步會自動略過。
2. 如有需要，登入機構後台。
3. 確認頁面顯示的機構和帳號正確，然後按「同意授權」。
4. 回到 AI 助手，告訴它「好了」。

## 保持最新

有新版本時，AI 助手會提醒你。如要自動更新：

- **Claude Code**：`/plugin` → Marketplaces → `lingxi-global` → **Enable auto-update**
- **Claude 桌面版**：為 `lingxi-global` 市場開啟自動同步

立即更新：執行 `claude plugin update lingxi-global@lingxi-global`，或對 AI 助手說：「請閱讀 https://mcp.lxi.hk/mcp/install.md ，幫我更新靈析 Global。」

## 你的資料與權限

- AI 助手使用你自己的靈析帳號和權限，看不到也改不了你沒有權限的內容。
- 建立、發佈、提交審核和代填答卷前，都會先徵得你確認；機構的審批流程照常適用。
- 你可以隨時在 AI 助手的連接器或 MCP 設定中中斷連接。

## 更多

- 關於靈析 MCP：https://mcp.lxi.hk/mcp/introduction
- 各版本更新內容：[CHANGELOG](plugins/lingxi-global/skills/lingxi-global/CHANGELOG.md)
