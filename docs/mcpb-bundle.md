# Building the Eden `.mcpb` bundle (one-click Claude Desktop install)

A `.mcpb` file (MCP Bundle, formerly `.dxt`) installs an MCP server into Claude
Desktop by double-click. Eden's server is remote, so the bundle is a thin
wrapper: it ships `mcp-remote` config pointing at `https://mcp.eden.so/mcp`, and
Claude Desktop handles the OAuth sign-in in the browser.

Note: Claude Desktop users can also just add a custom connector by URL
(Settings → Connectors), which is simpler. The `.mcpb` exists for one-click
distribution from the GitHub release page and for users who never open settings.

## Prerequisites

- Node 18+
- `npm install -g @anthropic-ai/mcpb`

## Bundle contents

Create a working directory `eden-mcpb/` with exactly one file:

`manifest.json`:

```json
{
  "manifest_version": "0.2",
  "name": "eden",
  "display_name": "Eden",
  "version": "1.0.0",
  "description": "The social research MCP: research any creator, steal the patterns, schedule the post.",
  "long_description": "Eden indexes 3M+ top-performing posts across X, LinkedIn, Instagram, TikTok, YouTube, and Substack. Research any creator, search the index, then schedule or publish to eight platforms — all from Claude. Sign in with your Eden account when Claude prompts you.",
  "author": {
    "name": "Eden",
    "url": "https://eden.so"
  },
  "homepage": "https://eden.so/features/mcp/",
  "documentation": "https://eden.so/llms.txt",
  "support": "https://eden.so/help/",
  "icon": "icon.png",
  "server": {
    "type": "node",
    "entry_point": "",
    "mcp_config": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.eden.so/mcp"]
    }
  },
  "keywords": [
    "social media",
    "creator research",
    "scheduling",
    "analytics",
    "twitter",
    "linkedin",
    "instagram",
    "substack"
  ],
  "license": "SEE LICENSE AT https://eden.so/terms/"
}
```

Add `icon.png` (512×512 PNG, the Eden mark) next to it. **Needs Dan: the icon asset.**

Validate the manifest against the current schema before packing — the `mcpb`
CLI ships the authoritative validator and the field set above may drift:

```bash
mcpb validate manifest.json
```

If validation complains about `entry_point` for a wrapper-style bundle, run
`mcpb init` in a scratch dir and diff its generated manifest; keep our metadata,
adopt its structure.

## Pack

```bash
cd eden-mcpb
mcpb pack . eden.mcpb
```

Output: `eden.mcpb`. Test it by double-clicking on a machine with Claude Desktop:
it should show the Eden name, icon, and description, install, and then prompt
for OAuth on first tool use.

## Sign (optional but recommended)

```bash
mcpb sign eden.mcpb --cert <cert.pem> --key <key.pem>
```

Unsigned bundles install with a warning. Decide at release time whether to buy a
signing cert; skip for v1 unless the warning hurts conversion.

## Distribute

Attach `eden.mcpb` to a GitHub release on the public repo. Link it from:

- the repo README (Claude Desktop quickstart)
- `eden.so/features/mcp/` ("One-click install for Claude Desktop")

Re-pack and re-release whenever the description or icon changes. Tool changes
never require a new bundle (the server is remote).
