# Eden MCP — install guide for AI agents

You are an AI agent installing the Eden MCP server for your user. Follow this file top to bottom. Everything is deterministic except the OAuth sign-in, which the user must complete in a browser.

## Facts

- Server type: **remote, hosted**. Streamable HTTP. Nothing to download, build, or run locally.
- Endpoint: `https://mcp.eden.so/mcp`
- Auth: OAuth 2.0 ("Sign in with Eden") for interactive clients, or a Personal Access Token (`eden_pat_…`) as a Bearer header for headless clients.
- Tools are namespaced `eden_*`. Read tools work with any credential; write tools (scheduling, notes, boards, Custom AI management) need a write-scoped credential and return `403` otherwise.
- The user needs an Eden account ([eden.so](https://eden.so)). If they don't have one, stop and tell them to sign up first.

## Step 1 — detect the client you are running in, then apply exactly one section

### Claude Code (CLI)

```bash
claude mcp add --transport http eden https://mcp.eden.so/mcp
```

Then tell the user: "Run `/mcp` and pick eden to sign in with your Eden account." You cannot complete the OAuth for them.

### Claude Desktop or claude.ai (no CLI available)

You cannot edit the connector list yourself. Give the user these steps verbatim:

1. Open **Settings → Connectors → Add custom connector**.
2. Name: `Eden`. URL: `https://mcp.eden.so/mcp`. Click **Add**.
3. Click **Connect** and sign in with your Eden account.

### Cursor

Write (or merge into) `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "eden": {
      "url": "https://mcp.eden.so/mcp"
    }
  }
}
```

Do not remove existing entries under `mcpServers`. Cursor prompts the user for OAuth sign-in on first tool use.

### ChatGPT

Tell the user (you cannot do this for them): enable **Developer mode** under **Settings → Apps & Connectors → Advanced**, then create a connector with name `Eden`, MCP server URL `https://mcp.eden.so/mcp`, authentication **OAuth**.

### Headless / server-side (n8n, Zapier, Make, custom scripts, CI)

OAuth is impossible here. Ask the user for a Personal Access Token:

1. User creates it in Eden: **Settings → Integrations → API access → Generate token** (choose read + write for scheduling use cases; the value is shown once).
2. Configure the MCP client with URL `https://mcp.eden.so/mcp` and header `Authorization: Bearer eden_pat_…`.

Never ask the user to paste the token into chat if your platform offers a secret store; point them at the secret store instead.

### Any other MCP client

Use its "remote server" / "HTTP" option with URL `https://mcp.eden.so/mcp`. If it only supports stdio, bridge with mcp-remote:

```json
{
  "mcpServers": {
    "eden": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.eden.so/mcp"]
    }
  }
}
```

## Step 2 — verify

After the user completes sign-in, call `eden_list_workspaces`. Success looks like a JSON list of at least one workspace with `id`, `name`, and `role`.

Then, if the user's task involves scheduling, call `eden_list_schedules` to confirm connected social accounts and grab the timezone plus next free slots.

## Step 3 — orient

- Full tool catalog: fetch `https://eden.so/llms-full.txt`.
- Workflow recipes (research → save → schedule): fetch `https://eden.so/skills/eden-mcp/SKILL.md`.

## Troubleshooting

| Symptom                                         | Meaning                               | Fix                                                                     |
| ----------------------------------------------- | ------------------------------------- | ----------------------------------------------------------------------- |
| `401` on every call                             | Credential missing or expired         | Re-run the OAuth sign-in, or check the `Authorization` header spelling. |
| `403` on write tools only                       | Credential is read-scoped             | User must grant the `write` scope on OAuth, or mint a read + write PAT. |
| `status: "queued"` from `eden_read_social_post` | Post is being hydrated into the index | Retry the same call after a few seconds.                                |
| Tool not found                                  | Client cached an old tool list        | Reload / reconnect the server in the client.                            |
