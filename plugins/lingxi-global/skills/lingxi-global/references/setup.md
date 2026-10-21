# Connect, reconnect and update

Use this when LingXi tools are not available, the authorization has expired, or the skill needs updating. Give the user only the steps for the app they are using, one short numbered list at a time, in their language. Ask which app they use only if you cannot tell. Wait for them to finish before checking again.

LingXi Global server URL: `https://mcp.lxi.hk/mcp/lingxi`

## Connect

### Claude Code (terminal)

- **Plugin installed** (`lingxi-global`): the connection is already configured; it only needs signing in.
  1. Type `/mcp`.
  2. Choose the LingXi Global server and select **Authenticate**.
  3. Complete "Sign in to LingXi" below in the browser window that opens.
- **Plugin not installed:** recommend installing it, because it also keeps this skill up to date:
  ```bash
  claude plugin install lingxi-global --marketplace lingxi/lingxi-global
  ```
  If they prefer to add only the connection:
  ```bash
  claude mcp add --transport http lingxi-global https://mcp.lxi.hk/mcp/lingxi
  ```
  Then `/mcp` → LingXi Global → **Authenticate**. If you can run commands, offer to run the command for them.

### Claude web app or Claude desktop app (chat)

1. Open **Settings → Connectors**.
2. Choose **Add custom connector**.
3. Name: `LingXi Global`. URL: `https://mcp.lxi.hk/mcp/lingxi`.
4. Select **Connect**, then complete "Sign in to LingXi" below.

If the option is not available, their Claude plan or their organization's settings may not allow custom connectors; on Team or Enterprise plans, an owner may need to add it for the organization.

### Claude desktop app (Cowork)

1. Open **Customize → Plugins → Add → Add marketplace** and enter `lingxi/lingxi-global`.
2. Install **LingXi Global**.
3. When asked to connect, complete "Sign in to LingXi" below.

### Other AI apps (ChatGPT, Codex, Cursor, WorkBuddy and others)

In the app's settings for MCP servers, connectors or apps, add a **remote MCP server** (HTTP / streamable HTTP):

- Name: `LingXi Global`
- URL: `https://mcp.lxi.hk/mcp/lingxi`
- Authentication: OAuth (sign in when asked)

Then complete "Sign in to LingXi" below. If the app only accepts a fixed API key or token, tell the user it cannot connect to LingXi Global yet.

## Sign in to LingXi (same for every app)

1. A LingXi page opens and asks for the organization's admin address, for example `yourorg.admin.lxi.hk`. If the user recently signed in to their LingXi admin console in the same browser, this step is skipped automatically.
2. If they are not signed in, sign in to the admin console as usual.
3. On the authorization page, check that the **organization** and **account** shown are correct, then select **Agree** (同意授權). To use a different organization, use the switch link on that page.
4. Return to the AI app and say "done".

After they say done, check for the LingXi tools again (SKILL.md §1.1). If they still do not appear, ask them to start a new conversation.

## Reconnect (authorization expired or wrong organization)

- Claude Code: `/mcp` → LingXi Global → **Re-authenticate** (or **Clear authentication**, then **Authenticate** to switch organization).
- Claude web/desktop app: **Settings → Connectors → LingXi Global → Disconnect**, then **Connect** again.
- Other apps: disconnect and connect the LingXi Global server again in the app's MCP settings.

Then repeat "Sign in to LingXi".

## Update this skill

Offer the update method that matches how the user installed it. If you can run commands, offer to run it after the user agrees.

| Installed with | Update |
| --- | --- |
| Claude Code plugin | `claude plugin update lingxi-global@lingxi-global`, then `/reload-plugins` or start a new session |
| Claude desktop app (Cowork) plugin | **Customize → Plugins → LingXi Global → Check for updates** |
| `npx skills` | `npx skills add lingxi/lingxi-global` (installs the latest version over the old one) |
| Any app | Ask the AI: "Please read https://mcp.lxi.hk/mcp/install.md and update LingXi Global for me." |

To get updates automatically in Claude Code: `/plugin` → **Marketplaces** → `lingxi-global` → **Enable auto-update**.

## Common problems

| Problem | What to tell the user |
| --- | --- |
| "Cannot find this organization's admin address" | Check the address in the browser bar of their LingXi admin console (it ends with `.admin.lxi.hk`) and enter that. |
| Connected to the wrong organization | Follow "Reconnect" and use the switch link on the authorization page. |
| Signed in, but a feature says no permission | Their account lacks that module permission; their organization administrator can grant it in the admin console. |
| The authorization page says the request expired | Start the connection again from the AI app; each request is valid for a short time. |
