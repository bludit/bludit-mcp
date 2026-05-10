# bludit-mcp

MCP server for [Bludit CMS](https://www.bludit.com). Lets any MCP-compatible AI client (Claude Desktop, Gemini CLI, Cursor, Zed, Windsurf, Continue.dev, and others) read and write Bludit pages over the HTTP API.

## Requirements
- Node.js 18+.
- Bludit 3.21.2+ with the API plugin enabled.
- Bludit's API token.
- Bludit's user AUTH token.

## Environment variables

| Variable | Required | Description |
|----------|----------|-------------|
| `BLUDIT_URL` | yes | Site root, e.g. `https://www.example.com`. No trailing slash needed. |
| `BLUDIT_API_TOKEN` | yes | API token from the API plugin settings. |
| `BLUDIT_AUTH_TOKEN` | yes | The user's authentication token. |

## MCP Configuration

The server entry below works in every MCP-compatible client. Only the **config file path** and the **JSON top-level key** vary.

```json
{
  "bludit": {
    "command": "npx",
    "args": ["-y", "bludit-mcp"],
    "env": {
      "BLUDIT_URL": "https://www.example.com",
      "BLUDIT_API_TOKEN": "your-api-token",
      "BLUDIT_AUTH_TOKEN": "your-user-auth-token"
    }
  }
}
```

## Example: Claude Code (CLI shortcut)

Claude Code can register the server without editing JSON:

```bash
claude mcp add bludit npx -y bludit-mcp \
  --env BLUDIT_URL=https://www.example.com \
  --env BLUDIT_API_TOKEN=... \
  --env BLUDIT_AUTH_TOKEN=...
```

## Example: Claude Desktop

Open Claude Destkop, go to Settings, go to Developer, click on the button Edit Config, and edit the file `claude_desktop_config.json`, you should add the MCP configuration like this.

```json
{
  "mcpServers": {
    "bludit": {
      "command": "npx",
      "args": ["-y", "bludit-mcp"],
      "env": {
        "BLUDIT_URL": "https://www.example.com",
        "BLUDIT_API_TOKEN": "506c904a5b2fd92rc90c0c7139ffa716ddc26780d49431eca66ad4fv",
        "BLUDIT_AUTH_TOKEN": "tb60c24xasd3r98c39b5abaecv2be82d69104"
      }
    }
  },
  "preferences": {
    "coworkWebSearchEnabled": true,
    "coworkScheduledTasksEnabled": true,
    "ccdScheduledTasksEnabled": true,
    "epitaxyPrefs": {
      "starred-local-code-sessions": [],
      "starred-cowork-spaces": [],
      "starred-session-groups": [],
      "dframe-local-slice": {
        "pinnedOrder": [],
        "customGroupAssignments": {},
        "customGroupOrder": {}
      }
    }
  }
}
```

Open the Claude Desktop Developer section.
<img width="1312" height="912" alt="Screenshot 2026-05-10 at 23 16 31" src="https://github.com/user-attachments/assets/d8914d5c-6cfb-438d-b179-316ba326b424" />

Setup the MCP, after the file is save, restart the Claude Desktop application.
<img width="1192" height="795" alt="Screenshot 2026-05-10 at 23 16 49" src="https://github.com/user-attachments/assets/f89da75d-b99b-42dd-a72a-7b01d5fe60da" />

After you restarted the Claude Desktop you should see the Bludit MCP in Running state.
<img width="1312" height="912" alt="Screenshot 2026-05-10 at 23 26 00" src="https://github.com/user-attachments/assets/fb3a6ddc-f089-4ebf-b771-5ea52a0f0d19" />

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
