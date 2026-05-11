# bludit-mcp

MCP server for [Bludit CMS](https://www.bludit.com). Lets any MCP-compatible AI client (Claude Desktop, Gemini CLI, Cursor, Zed, Windsurf, Continue.dev, and others) read and write Bludit pages over the HTTP API.

## Requirements

- Node.js 18+.
- Bludit 3.22.0+ with the API plugin enabled.
- Bludit's API token.
- Bludit's user AUTH token.

## Environment variables

| Variable | Required | Description |
|----------|----------|-------------|
| `BLUDIT_URL` | yes | Site root, e.g. `https://www.example.com`. No trailing slash needed |
| `BLUDIT_API_TOKEN` | yes | Admin Panel → Plugins → API → Settings → API token |
| `BLUDIT_AUTH_TOKEN` | yes | The user's authentication token. Admin Panel → Users → admin → Security tab → Token |

## Install it on Claude Desktop

### 0. Install Claude Desktop on your PC.

- https://code.claude.com/docs/en/desktop

### 1. Open the Developer settings

In Claude Desktop, go to **Settings → Developer** and click **Edit Config**, then edit the file `claude_desktop_config.json`.

<img width="1312" height="912" alt="Claude Desktop" src="https://github.com/user-attachments/assets/337ab3df-5767-4668-9d26-cfed13babbc1" />

<img width="1312" height="912" alt="Claude Desktop Developer settings" src="https://github.com/user-attachments/assets/d8914d5c-6cfb-438d-b179-316ba326b424" />

### 2. Add the Bludit entry and save

Add the Bludit server inside the `mcpServers` block, then save the file and restart Claude Desktop.

```json
{
  "mcpServers": {
    "bludit": {
      "command": "npx",
      "args": ["-y", "bludit-mcp"],
      "env": {
        "BLUDIT_URL": "https://www.example.com",
        "BLUDIT_API_TOKEN": "...",
        "BLUDIT_AUTH_TOKEN": "..."
      }
    },
    ...
  },
  "preferences": {
    "coworkScheduledTasksEnabled": true,
    ...
  }
}
```

<img width="1192" height="795" alt="Editing claude_desktop_config.json" src="https://github.com/user-attachments/assets/f89da75d-b99b-42dd-a72a-7b01d5fe60da" />

### 3. Verify the server is running

After restarting Claude Desktop, the Bludit MCP should appear in the **Running** state.

<img width="1312" height="912" alt="Bludit MCP shown as Running" src="https://github.com/user-attachments/assets/fb3a6ddc-f089-4ebf-b771-5ea52a0f0d19" />

### 4. Try it out

Ask any question related to pages. You can create, edit, delete, or just list them.

- List existing pages: *What pages do I currently have on my blog?*
- Brainstorm + Create: *I'm a travel writer and I need a catchy title and intro paragraph for a post about slow travel in rural Spain. Create a new draft page with it.*
- Edit / refine: *That intro is good but too formal. Rewrite it with a more casual, personal tone and update the page.*
- Delete: *That was just a draft, I don't need it anymore — delete the 'Salt and Sun' page.*

## Install on Claude Code CLI

```bash
claude mcp add bludit \
  -e BLUDIT_URL=https://www.example.com \
  -e BLUDIT_API_TOKEN=... \
  -e BLUDIT_AUTH_TOKEN=... \
  -- npx -y bludit-mcp
```

Check if the MCP is running
```bash
claude mcp list

...
bludit: npx -y bludit-mcp - ✓ Connected
```

## Install on Gemini CLI

```
gemini mcp add bludit npx -y bludit-mcp \
  -e BLUDIT_URL=https://www.example.com \
  -e BLUDIT_API_TOKEN=... \
  -e BLUDIT_AUTH_TOKEN=...
```

Check if the MCP is running
```
gemini mcp list

Configured MCP servers:
✓ bludit: npx -y bludit-mcp (stdio) - Connected
```

## Tools

| Tool | Description | Auth |
|------|-------------|------|
| `list_pages` | List pages with filters (`published`, `draft`, `static`, `sticky`, `scheduled`, `untagged`) and pagination. | API token |
| `get_page` | Get a single page by key. | API token |
| `create_page` | Create a new page. Returns the full page object. | API + auth |
| `edit_page` | Edit a page. Pass only the fields you want to change. | API + auth |
| `delete_page` | Delete a page by key. | API + auth |

## License

MIT. See [LICENSE](LICENSE).
