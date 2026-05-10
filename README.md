# bludit-mcp

MCP server for [Bludit CMS](https://www.bludit.com). Lets any MCP-compatible AI client (Claude Desktop, Gemini CLI, Cursor, Zed, Windsurf, Continue.dev, and others) read and write Bludit pages over the HTTP API.

## Tools

| Tool | Description | Auth |
|------|-------------|------|
| `list_pages` | List pages with filters (`published`, `draft`, `static`, `sticky`, `scheduled`, `untagged`) and pagination. | API token |
| `get_page` | Get a single page by key. | API token |
| `create_page` | Create a new page. Returns the full page object. | API + auth |
| `edit_page` | Edit a page. Pass only the fields you want to change. | API + auth |
| `delete_page` | Delete a page by key. | API + auth |

## Requirements

- Node.js 18+ (uses the built-in `fetch`).
- Bludit 3.21.2+ with the API plugin enabled. Earlier versions return legacy HTTP codes that this server doesn't expect.
- An API token (Settings > API plugin) and, for write tools, a per-user authentication token from a user with the **admin** role.

## Configuration

Three environment variables:

| Variable | Required | Description |
|----------|----------|-------------|
| `BLUDIT_URL` | yes | Site root, e.g. `https://www.example.com`. No trailing slash needed. |
| `BLUDIT_API_TOKEN` | yes | API token from the API plugin settings. |
| `BLUDIT_AUTH_TOKEN` | only for write tools | The admin user's authentication token. Read-only setups can omit this. |

## Setup

The server entry below works in every MCP-compatible client. Only the **config file path** and the **JSON top-level key** vary.

```json
{
  "bludit": {
    "command": "npx",
    "args": ["-y", "bludit-mcp"],
    "env": {
      "BLUDIT_URL": "https://www.example.com",
      "BLUDIT_API_TOKEN": "your-api-token",
      "BLUDIT_AUTH_TOKEN": "your-admin-auth-token"
    }
  }
}
```

Wrap it under the right top-level key for your client:

| Client | Config file | Top-level key |
|--------|-------------|---------------|
| Claude Desktop (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json` | `mcpServers` |
| Claude Desktop (Windows) | `%APPDATA%\Claude\claude_desktop_config.json` | `mcpServers` |
| Claude Desktop (Linux) | `~/.config/Claude/claude_desktop_config.json` | `mcpServers` |
| Claude Code | `~/.claude.json` (user) or `.mcp.json` (project) | `mcpServers` |
| Gemini CLI | `~/.gemini/settings.json` or `.gemini/settings.json` (project) | `mcpServers` |
| Cursor | `~/.cursor/mcp.json` (user) or `.cursor/mcp.json` (project) | `mcpServers` |
| Windsurf | `~/.codeium/windsurf/mcp_config.json` | `mcpServers` |
| Zed | `~/.config/zed/settings.json` | `context_servers` |
| Continue.dev | `~/.continue/config.json` | `mcpServers` (under `experimental`) |
| Goose | `~/.config/goose/config.yaml` | `extensions` |

Paths can change as clients evolve; check the client's MCP docs if a path doesn't match. The community list at [modelcontextprotocol.io/clients](https://modelcontextprotocol.io/clients) is kept current.

Restart the client and ask: *"List my draft pages on Bludit"* or *"Create a draft titled 'Hello' with the content 'first post'"*.

### Claude Code (CLI shortcut)

Claude Code can register the server without editing JSON:

```bash
claude mcp add bludit npx -y bludit-mcp \
  --env BLUDIT_URL=https://www.example.com \
  --env BLUDIT_API_TOKEN=... \
  --env BLUDIT_AUTH_TOKEN=...
```

## Local development

```bash
git clone git@github.com:bludit/bludit-mcp.git
cd bludit-mcp
npm install
BLUDIT_URL=http://localhost:8000 \
BLUDIT_API_TOKEN=... \
BLUDIT_AUTH_TOKEN=... \
npm start
```

The server speaks MCP over stdio, so running it directly will block waiting for a client. Point any MCP client at it with `command: "node"` and the absolute path to `src/index.js`:

```json
{
  "mcpServers": {
    "bludit-dev": {
      "command": "node",
      "args": ["/absolute/path/to/bludit-mcp/src/index.js"],
      "env": { "...": "..." }
    }
  }
}
```

For fast iteration without a real client, use the [MCP Inspector](https://github.com/modelcontextprotocol/inspector):

```bash
BLUDIT_URL=http://localhost:8000 \
BLUDIT_API_TOKEN=... \
BLUDIT_AUTH_TOKEN=... \
npx @modelcontextprotocol/inspector node src/index.js
```

## Troubleshooting

- **`Bludit API 401: Invalid API token`**: check `BLUDIT_API_TOKEN`. Tokens are visible at *Admin > Plugins > API*.
- **`Bludit API 401` on write tools**: the auth token belongs to a non-admin user, or the user has been disabled. Bludit invalidates tokens for disabled users.
- **`BLUDIT_AUTH_TOKEN is required for write operations`**: configure the env var, or only use read tools.
- **Tools missing in the client**: confirm the config is valid and that you fully restarted the client. Each client logs MCP traffic in a different place; for Claude Desktop on macOS the bludit-mcp log lives at `~/Library/Logs/Claude/mcp-server-bludit.log`. See the [troubleshooting docs](https://docs.bludit.com/en/mcp/troubleshooting) for paths per client.

## License

MIT. See [LICENSE](LICENSE).
