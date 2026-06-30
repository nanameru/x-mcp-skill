# X MCP Skill

Vercel `skills` CLI / Codex / Claude Code / Cursor向けの X MCP skill です。

この skill は、X公式の MCP Servers ドキュメントに基づき、次を扱うための作業手順を提供します。

- X API MCP: `https://api.x.com/mcp`
- X Docs MCP: `https://docs.x.com/mcp`
- `xurl mcp` bridge による OAuth2 PKCE setup
- Grok Build / Cursor / Claude Desktop / VS Code / generic MCP client 設定
- X APIの書き込み操作前の明示確認ルール

## Install

利用可能な skill を確認:

```bash
npx skills add nanameru/x-mcp-skill --list
```

現在のプロジェクトに追加:

```bash
npx skills add nanameru/x-mcp-skill --skill x-mcp
```

Codex向けに明示して追加:

```bash
npx skills add nanameru/x-mcp-skill --skill x-mcp --agent codex
```

グローバルに追加:

```bash
npx skills add nanameru/x-mcp-skill --skill x-mcp --global
```

一度だけ使う prompt を生成:

```bash
npx skills use nanameru/x-mcp-skill@x-mcp
```

## Notes

- X API MCPの実利用には、X Developer App の `CLIENT_ID` / `CLIENT_SECRET` が必要です。
- `CLIENT_SECRET`、Bearer token、OAuth token、`~/.xurl` 配下の内容は secret として扱ってください。
- 投稿、Article公開、bookmark、follow、DMなどの書き込み/アカウント操作は、実行前に必ずユーザー確認を取る方針です。

## Sources

- https://docs.x.com/tools/mcp
- https://docs.x.com/skill.md
- https://docs.x.com/llms.txt
