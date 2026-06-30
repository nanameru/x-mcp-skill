---
name: x-mcp
description: "Use when Codex needs to configure, troubleshoot, or use X's official MCP servers: X API MCP at https://api.x.com/mcp through the xurl bridge, X Docs MCP at https://docs.x.com/mcp, OAuth2 setup, MCP client config for Grok/Cursor/Claude/VS Code, X API documentation lookup, or safe X API tool usage."
---

# X MCP

Use the official X MCP servers to look up X API documentation and, when authorized by the user, call X API tools through the user's X app and account.

Primary sources:
- X MCP Servers: `https://docs.x.com/tools/mcp`
- Agent-oriented X API skill: `https://docs.x.com/skill.md`
- X docs index: `https://docs.x.com/llms.txt`

## Server Choice

- Use **Docs MCP** (`https://docs.x.com/mcp`) when the task is documentation lookup, endpoint discovery, examples, auth/rate-limit details, or verification of current X API behavior.
- Use **X MCP** (`https://api.x.com/mcp`) when the task requires calling X API endpoints such as post search, user lookup, bookmarks, trends/news, or Articles.
- Connect both servers when building or debugging an integration: Docs MCP for current reference material, X MCP for live API actions.

When X MCP tools are already available in the environment, prefer those tools over raw HTTP calls. When they are not available, guide the user through MCP client configuration instead of inventing unsupported local tools.

## Safe Usage Rules

- Never paste, log, commit, or echo `CLIENT_SECRET`, Bearer tokens, OAuth tokens, or files under `~/.xurl`.
- Ask for explicit user confirmation before any write or account-action tool call: posting, publishing Articles, bookmarks, follows, DMs, list changes, or profile/account mutations.
- Prefer read-only documentation and lookup calls while planning.
- Treat rate limits and paid usage as real constraints. Start with narrow queries and small result sizes.
- If a tool result includes partial errors, report both successful data and errors.

## X API MCP Setup

The X API MCP server is hosted at `https://api.x.com/mcp`, but X recommends reaching it through the local `xurl mcp` bridge. The bridge handles OAuth2 PKCE login, stores refreshed tokens under `~/.xurl`, and sends a fresh Bearer token to the hosted MCP server.

Prerequisites:
1. Create an X app in the X Developer Portal.
2. Enable OAuth 2.0.
3. Register redirect URI `http://localhost:8080/callback`, unless using a custom `REDIRECT_URI`.
4. Keep `CLIENT_ID` and `CLIENT_SECRET` in environment/config secrets, not source control.
5. Ensure Node.js and `npx` are available.

Universal stdio MCP config:

```json
{
  "mcpServers": {
    "xapi": {
      "command": "npx",
      "args": ["-y", "@xdevplatform/xurl", "mcp", "https://api.x.com/mcp"],
      "env": {
        "CLIENT_ID": "YOUR_X_APP_CLIENT_ID",
        "CLIENT_SECRET": "YOUR_X_APP_CLIENT_SECRET"
      }
    }
  }
}
```

Use a startup timeout of at least 300 seconds where the client supports it, because first-run browser OAuth can block the MCP handshake.

For headless or remote machines, authenticate once before starting the MCP client:

```bash
xurl auth oauth2 --headless
```

For read-only remote MCP clients that support custom headers, an app-only Bearer token can connect directly to `https://api.x.com/mcp`, but this lacks user context and token refresh. Prefer `xurl mcp` for full functionality.

## Docs MCP Setup

Docs MCP is hosted at `https://docs.x.com/mcp` and provides:
- `search_x`: search X documentation.
- `get_page_x`: retrieve a page by path.

MCP config:

```json
{
  "mcpServers": {
    "x-docs": {
      "url": "https://docs.x.com/mcp"
    }
  }
}
```

Use Docs MCP before answering implementation questions that can change over time, especially authentication, scopes, endpoint availability, rate limits, and request/response schemas.

## Task Workflow

1. Classify the task as documentation lookup, MCP setup, live read call, or live write/account action.
2. For setup tasks, identify the target client: Grok Build, Cursor, Claude Desktop, VS Code, or generic MCP client.
3. Verify current details against Docs MCP or official docs before giving config that might have changed.
4. For X API MCP setup, ensure the user has an OAuth2-enabled X app and the redirect URI is registered.
5. For live API use, confirm the MCP server is connected and authorized.
6. For write/account actions, restate the exact action and wait for explicit confirmation.
7. After calls, summarize data, partial errors, rate-limit signals, and follow-up actions.

## Troubleshooting Checklist

- Startup timeout: set to 300 seconds or more during first OAuth login.
- Browser does not open: use `xurl auth oauth2 --headless`.
- `401` or token refresh failure: verify app credentials, then re-run OAuth login.
- Redirect/callback error: ensure `http://localhost:8080/callback` or custom `REDIRECT_URI` is registered in the X app.
- `client-not-enrolled`: confirm the app is in the correct X package/environment.
- Stale `npx` package: add `--registry=https://registry.npmjs.org/` to the args.
- Garbled MCP output: do not run the bridge in verbose mode; stdout must remain JSON-RPC only.

## Verification

- For config-only work, validate JSON/TOML syntax and list the exact file touched.
- For Grok Build, run or ask the user to run `grok mcp doctor xapi`.
- For generic bridge verification, run `npx -y @xdevplatform/xurl mcp https://api.x.com/mcp` only when credentials are configured and the user expects OAuth.
- For docs-only setup, use `search_x` or `get_page_x` if exposed by the current environment.
