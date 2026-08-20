# Eden MCP

**The social research MCP: research any creator, steal the patterns, schedule the post, without leaving Claude.**

Eden ([eden.so](https://eden.so)) indexes 3M+ top-performing posts across X, LinkedIn, Instagram, TikTok, YouTube, and Substack. This MCP server puts that index, plus your own Eden workspace, scheduler, and analytics, inside any MCP client.

- **Endpoint**: `https://mcp.eden.so/mcp` (hosted, Streamable HTTP)
- **Auth**: OAuth ("Sign in with Eden") or a Personal Access Token
- **Nothing to install.** This is a remote server; every quickstart below is a URL paste or a one-liner.

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/install-mcp?name=eden&config=eyJ1cmwiOiJodHRwczovL21jcC5lZGVuLnNvL21jcCJ9)

## What you can ask once it's connected

- "Analyze @lennysan's last 90 days on X. What hooks and formats are doing the numbers?"
- "Find 20 creators similar to @thedankoe, pull each one's top post, and save them all to a board called Swipe File."
- "Search the index for viral posts about morning routines on LinkedIn. What patterns repeat?"
- "Turn this note into three post variants and schedule them into my usual slots next week."
- "Read my own analytics for the last 30 days. What should I double down on?"

Schedulers can't research. Scrapers need API keys and return raw JSON. Eden's MCP does the research on an already-built index, then schedules or publishes to eight platforms (X, Threads, LinkedIn, Substack, Instagram, TikTok, Facebook, YouTube).

## Quickstarts

### Claude (claude.ai and Claude Desktop)

1. Open **Settings → Connectors → Add custom connector**.
2. Name it `Eden`, paste `https://mcp.eden.so/mcp`, click **Add**.
3. Click **Connect** and sign in with your Eden account.

### Claude Code

```bash
claude mcp add --transport http eden https://mcp.eden.so/mcp
```

Then run `/mcp` inside Claude Code to complete the OAuth sign-in.

Prefer a one-click desktop install? See [docs/mcpb-bundle.md](docs/mcpb-bundle.md) for the `.mcpb` bundle.

### Cursor

Click the **Add to Cursor** button above, or use the deeplink directly:

```
cursor://anysphere.cursor-deeplink/mcp/install?name=eden&config=eyJ1cmwiOiJodHRwczovL21jcC5lZGVuLnNvL21jcCJ9
```

Or add it by hand to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "eden": {
      "url": "https://mcp.eden.so/mcp"
    }
  }
}
```

Cursor opens the OAuth sign-in on first use.

### ChatGPT

Requires a paid ChatGPT plan with connectors enabled.

1. **Settings → Apps & Connectors**, enable **Developer mode** (under Advanced) if you haven't.
2. **Create** a connector: name `Eden`, MCP server URL `https://mcp.eden.so/mcp`, authentication **OAuth**.
3. Sign in with Eden when prompted, then enable the connector in a chat via the composer's tools menu.

### Raycast

1. Open Raycast and run **Manage MCP Servers** (Raycast AI required).
2. Add a server with transport **HTTP** and URL `https://mcp.eden.so/mcp`.
3. Complete the OAuth sign-in when Raycast opens it. Eden's tools then work with `@eden` in AI chat.

### n8n, Zapier, Make, and other headless automation

Server-side tools can't run a browser OAuth flow. Use a **Personal Access Token** instead:

1. In Eden: **Settings → Integrations → API access → Generate token**. Pick read-only or read + write. Copy the `eden_pat_…` value immediately; it's shown once.
2. Point the tool's MCP client node at `https://mcp.eden.so/mcp` with header `Authorization: Bearer eden_pat_…`.

Tokens are revocable any time from the same settings card.

### Installing with an AI agent

Point your agent at [llms-install.md](llms-install.md). It contains the full self-serve install and verification flow for every client above.

## Access model

- **Read tools** work with any valid credential.
- **Write tools** (scheduling, publishing, notes, boards, Custom AI management) need a write-scoped credential: OAuth granted the `write` scope, or a read + write PAT. A read-only credential gets a `403` from every write tool.
- The server is a pure adapter over Eden's user-facing APIs. It forwards your own bearer token; it holds no admin secrets and sees only what your account can see.
- There is no hard delete anywhere on the surface. Trashing a board is soft and restorable, and deleting a Custom AI archives it.

## Tools

52 tools, plus 3 deprecated aliases kept for compatibility.

### Workspace (read)

| Tool                          | What it does                                                              |
| ----------------------------- | ------------------------------------------------------------------------- |
| `eden_list_workspaces`        | List your workspaces with id, name, slug, and role.                       |
| `eden_list_workspace_items`   | List saved items (boards, notes, saved posts, media) with filters.        |
| `eden_find_workspace_items`   | Semantic search over your library in natural language.                    |
| `eden_search_workspace_items` | Fast substring search across item titles and URLs.                        |
| `eden_get_note_markdown`      | Read a note's full markdown body.                                         |
| `eden_read_media_card`        | Read processed media content: transcript, AI description, extracted text. |
| `eden_read_board`             | Read a whole board: sticky notes, labels, shapes, child items, sections.  |
| `eden_get_connections`        | Read an item's backlinks plus semantic connection suggestions.            |
| `eden_list_chats`             | List your Eden chats in a workspace.                                      |

### Highlights and captures (read)

| Tool                     | What it does                                                          |
| ------------------------ | --------------------------------------------------------------------- |
| `eden_search_highlights` | Search your saved highlights (books, articles, posts, Kindle, Snipd). |
| `eden_search_captures`   | Search your quick captures: notes, links, voice-note clippings.       |

### Social intelligence (read)

| Tool                         | What it does                                                                  |
| ---------------------------- | ----------------------------------------------------------------------------- |
| `eden_search_creators`       | Discover creators by topic, or find creators similar to ones you name.        |
| `eden_resolve_creator`       | Resolve a handle, name, or profile URL to an indexed creator.                 |
| `eden_analyze_creator`       | Deep-dive one creator: top posts, cadence, formats, engagement.               |
| `eden_read_social_post`      | Read a post's full body, transcript, slides, metrics by URL or id.            |
| `eden_following_overview`    | List every creator you follow, deduplicated across platforms.                 |
| `eden_analyze_list`          | Analyze a curated creator list, or list all your lists.                       |
| `eden_search_social_content` | Search indexed posts: one creator, a list, your follows, or the global index. |

### Boards and documents (write)

| Tool                       | What it does                                              |
| -------------------------- | --------------------------------------------------------- |
| `eden_create_board`        | Create a new board.                                       |
| `eden_connect_items`       | Create item-to-item backlinks.                            |
| `eden_rename_board`        | Rename a board.                                           |
| `eden_trash_board`         | Move a board to trash (soft, restorable).                 |
| `eden_create_note`         | Create a markdown document or text card.                  |
| `eden_update_note`         | Replace a note's body.                                    |
| `eden_rename_note`         | Rename a note.                                            |
| `eden_append_to_note`      | Append markdown to the end of a note.                     |
| `eden_save_links_to_board` | Save URLs onto a board as cards.                          |
| `eden_save_posts_to_board` | Save indexed social posts onto a board as hydrated cards. |
| `eden_save_items_to_board` | Place existing library items onto a board.                |

### Scheduling, publishing, and analytics

| Tool                                   | Access | What it does                                                                     |
| -------------------------------------- | ------ | -------------------------------------------------------------------------------- |
| `eden_list_schedules`                  | Read   | List posting schedules, connected accounts, timezones, next free slots.          |
| `eden_update_schedule`                 | Write  | Edit a schedule's recurring slot times or timezone.                              |
| `eden_list_scheduled_posts`            | Read   | List drafts, queued, and published posts.                                        |
| `eden_schedule_post`                   | Write  | Schedule a post (or save a draft with `draft: true`). Supports `idempotencyKey`. |
| `eden_publish_post_now`                | Write  | Publish a post immediately.                                                      |
| `eden_update_scheduled_post`           | Write  | Edit a queued post's time, body, first comment, or auto-repost.                  |
| `eden_cancel_scheduled_post`           | Write  | Cancel a scheduled post or delete a draft.                                       |
| `eden_prepare_scheduling_media_upload` | Write  | Start a media upload for a post asset.                                           |
| `eden_scheduling_media_multipart`      | Write  | Drive a multipart media upload (sign-part, complete, abort).                     |
| `eden_upload_scheduling_media`         | Write  | Upload image, video, PDF, or document bytes for a post.                          |
| `eden_connect_social_accounts`         | Write  | Check connections or mint the account-linking URL.                               |
| `eden_get_analytics`                   | Read   | Compact digest of your own cross-platform performance.                           |
| `eden_list_analytics_posts`            | Read   | Per-post metric rows for charts and dashboards.                                  |
| `eden_list_auto_dm_rules`              | Read   | Inspect Instagram Auto-DM automations.                                           |
| `eden_create_auto_dm_automation`       | Write  | Create a comment-triggered Instagram Auto-DM automation.                         |

### Media research (read)

| Tool                       | What it does                                                   |
| -------------------------- | -------------------------------------------------------------- |
| `eden_study_top_carousels` | Slide-by-slide teardown of top-performing Instagram carousels. |

### Custom AI

| Tool                              | Access | What it does                                              |
| --------------------------------- | ------ | --------------------------------------------------------- |
| `eden_list_custom_ai`             | Read   | List a workspace's Custom AIs.                            |
| `eden_get_custom_ai`              | Read   | Get a Custom AI's instructions and source catalog.        |
| `eden_create_custom_ai`           | Write  | Create a Custom AI with instructions and starter prompts. |
| `eden_update_custom_ai`           | Write  | Replace an editable Custom AI's definition.               |
| `eden_delete_custom_ai`           | Write  | Archive a Custom AI (soft).                               |
| `eden_read_custom_ai_knowledge`   | Read   | Read one knowledge document of a managed Custom AI.       |
| `eden_search_custom_ai_knowledge` | Read   | Search a managed Custom AI's bundled knowledge.           |

### Deprecated aliases

`eden_read_card` → `eden_read_social_post` · `eden_create_sticky_note` → `eden_create_note` · `eden_create_scheduling_draft` → `eden_schedule_post`

## For agents and LLMs

- Machine-readable overview: [eden.so/llms.txt](https://eden.so/llms.txt)
- Full tool catalog: [eden.so/llms-full.txt](https://eden.so/llms-full.txt)
- Agent install runbook: [llms-install.md](llms-install.md)
- Downloadable Claude skill (workflow cheat-sheet for this server): [eden.so/eden-mcp-skill.zip](https://eden.so/eden-mcp-skill.zip)

## Links

- Product: [eden.so](https://eden.so)
- MCP feature page and docs: [eden.so/features/mcp](https://eden.so/features/mcp/)
- Privacy policy: [eden.so/privacy](https://eden.so/privacy/)
- Official MCP Registry listing: `so.eden/eden`

This repository is the public documentation and distribution home for Eden's hosted MCP server. The server itself runs at `https://mcp.eden.so/mcp`; its source lives in Eden's private monorepo.
