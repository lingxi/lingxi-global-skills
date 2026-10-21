# LingXi Global for AI assistants

**English** · [繁體中文](README.zh-HK.md) · [简体中文](README.zh-CN.md)

Work with your **LingXi Global** (靈析 Hong Kong / international edition) organization from your AI assistant — Claude, Codex and other assistants that support MCP. Just say what you need:

- "Show me this month's activities and how many people registered."
- "Create a volunteer sign-up form with name, mobile and preferred session. Keep it as a draft."
- "Find Mr Chan's contact details."

The assistant guides you step by step, shows the current status after each step, and only does what your LingXi account is allowed to do.

## Install — the easiest way

Send this sentence to your AI assistant:

> Please read https://mcp.lxi.hk/mcp/install.md and install LingXi Global for me.

It will install the right pieces for your app and walk you through signing in.

## Install manually

| Your app | How |
| --- | --- |
| **Claude Code** | `claude plugin install lingxi-global --marketplace lingxi/lingxi-global-skills` |
| **Claude desktop / web app** | Customize → Plugins → Add → Add marketplace → `lingxi/lingxi-global-skills` → install **LingXi Global** |
| **Codex, Cursor and other assistants** | `npx skills add lingxi/lingxi-global-skills`, then add the MCP server `https://mcp.lxi.hk/mcp/lingxi` in your app's MCP settings |

If your Claude Code version does not support `--marketplace`, run:

```bash
claude plugin marketplace add lingxi/lingxi-global-skills
claude plugin install lingxi-global@lingxi-global
```

## Sign in

The first time you use it, your assistant asks you to connect your LingXi account:

1. A LingXi page opens. Enter your organization's admin address (for example `yourorg.admin.lxi.hk`) — skipped automatically if you are already signed in to your admin console.
2. Sign in to the admin console if asked.
3. Check the organization and account shown, then select **Agree**.
4. Go back to your assistant and say "done".

## Stay up to date

Your assistant tells you when a newer version is available. To update automatically:

- **Claude Code:** `/plugin` → Marketplaces → `lingxi-global` → **Enable auto-update**
- **Claude desktop app:** turn on automatic sync for the `lingxi-global` marketplace

Update now: `claude plugin update lingxi-global@lingxi-global`, or ask your assistant: "Please read https://mcp.lxi.hk/mcp/install.md and update LingXi Global for me."

## Your data and permissions

- The assistant uses your own LingXi account and permissions. It cannot see or change anything you cannot.
- Creating, publishing, submitting for approval and submitting answers always need your confirmation, and your organization's approval rules still apply.
- You can disconnect at any time from your assistant's connector or MCP settings.

## More

- About LingXi MCP: https://mcp.lxi.hk/mcp/introduction
- Changes in each version: [CHANGELOG](plugins/lingxi-global/skills/lingxi-global/CHANGELOG.md)
