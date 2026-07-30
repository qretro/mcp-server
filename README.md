# QRetro MCP Server

Your team's retrospectives, available to any MCP client — read boards, summaries, health trends and open action items, run planning poker, and push agreed estimates back to your tracker.

[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-com.qretro%2Fretro-2f3437)](https://registry.modelcontextprotocol.io/v0/servers?search=qretro)
[![Transport](https://img.shields.io/badge/transport-streamable--http-2f3437)](https://qretro.com/docs/mcp)
[![Auth](https://img.shields.io/badge/auth-OAuth%202.1%20%7C%20token-2f3437)](https://qretro.com/docs/mcp)

> This repository holds the public manifest and documentation for QRetro's **hosted** MCP server. The server runs at `https://mcp.qretro.com` — there is nothing to install, build or self-host. Product source lives elsewhere.

## Connect

One address, no token. The client brings you to QRetro to sign in, shows you what is being asked for, and keeps the access it is granted.

**Claude Code**

```bash
claude mcp add --transport http qretro https://mcp.qretro.com
```

**Claude (web and desktop)** — Settings → Connectors → Add custom connector, paste `https://mcp.qretro.com`.

**Cursor, VS Code and other clients** — add the server by URL in the client's MCP settings, with no token field. On the first request the client opens the QRetro sign-in page itself.

### With an access token

For automations, CI, and clients that accept nothing but a fixed header. Create one in Settings → API Keys; a token can be bound to a single team.

```json
{
  "mcpServers": {
    "qretro": {
      "url": "https://mcp.qretro.com",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" }
    }
  }
}
```

## What you can ask

- Why has our ROTI dropped two sprints in a row?
- List every action item still open on the Backend team and who owns it.
- Summarise Friday's retrospective for the two people who missed it.
- Find every mention of flaky tests across our retrospectives.
- Open a poker game for the next sprint and import its tasks.
- Push the estimates we agreed in poker back to Jira.

## Tools

Names follow `domain.scope.action`. Two domains: `retro` for retrospectives, `poker` for planning poker.

### Retrospectives — read

| Tool | What it does |
| --- | --- |
| `retro.teams.list` | Your teams, with the number of finished and unfinished boards in each |
| `retro.team.members.list` | Team roster with each person's permission — the source of user IDs for assigning work |
| `retro.boards.list` | A team's retrospectives, newest first; filter by period or finished only |
| `retro.boards.search` | Keyword search across summaries, action items and participant cards |
| `retro.actions.list` | Open action items across every retrospective at once, overdue first |
| `retro.board.messages.list` | Participant cards by template column, with sentiment, category and votes |
| `retro.board.summary.get` | Summary of a board and its participants |
| `retro.board.actions.list` | Action items of one board with status, priority and assignee |
| `retro.board.insights.list` | Semantic clusters and the follow-ups the system suggests |
| `retro.board.health.get` | Score per category on a 0–10 scale, alignment, turnout, trend across previous retros |
| `retro.board.roti.get` | ROTI with distribution and historical trend |

### Planning poker — read

| Tool | What it does |
| --- | --- |
| `poker.sources.list` | Which issue trackers a team has connected and what each can do |
| `poker.iterations.list` | Sprints of a connected tracker, active and upcoming |
| `poker.games.list` | A team's games, newest first, with task counts and how many already carry an estimate |
| `poker.game.get` | One game: scale, progress, the task on the table, and the join link |
| `poker.game.tasks.list` | Tasks with estimate, individual votes, tracker key and sync state |

### Write — requires `mcp:write`

| Tool | What it does |
| --- | --- |
| `retro.actions.create` | Create an action item on a board |
| `retro.actions.update` | Update text, priority, due date or assignee |
| `retro.actions.complete` | Mark an action item completed |
| `retro.board.suggested_actions.promote` | Turn a suggestion into an agreement, keeping its wording and the theme it came from |
| `retro.board.suggested_actions.reject` | Dismiss a suggestion |
| `retro.board.messages.update` | Edit a message authored by the current user |
| `poker.games.create` | Open a game with a ready-made or custom scale |
| `poker.game.tasks.add` | Add tasks by hand, for work outside a tracker |
| `poker.game.tasks.import` | Pull a whole sprint, or search results, from a connected tracker |
| `poker.game.task.select` | Put a task on the table for everyone in the game |
| `poker.game.task.reveal` | Reveal the cards and compute the estimate from the votes cast |
| `poker.game.task.sync` | Write the agreed estimate back to Jira or Linear |

### Delete — requires `mcp:delete`

| Tool | What it does |
| --- | --- |
| `retro.board.messages.delete_own` | Delete a message you authored |

Two things the poker tools deliberately cannot do: set an estimate directly — it only ever comes from revealed votes — and vote on your behalf.

## Prompts

Clients that support MCP prompts pick these up as ready-made commands; in Claude Code they appear under `/`.

- **`analyze-retro`** — one retrospective end to end: summary, clusters, agreements, health and ROTI, finishing with key themes, risks and what to change next time. Takes `board_id`.
- **`team-health`** — where scores and ROTI are heading across a team's recent retrospectives, how many agreements actually get closed, what keeps repeating. Takes `team_id`.

## Permissions

Access is bounded twice over: by what the client was granted, and by the teams you belong to. A client can never reach a team you are not in.

| Scope | What it allows | Signing in | Token |
| --- | --- | --- | --- |
| `mcp:read` | Read retrospectives, messages, action items, metrics, poker games | yes | yes |
| `mcp:write` | Create and update action items, messages and poker games | yes | yes |
| `mcp:delete` | Delete your own messages | no | yes |

Tools a client has no permission for are not offered to it at all. Permissions match the interface exactly: a client can do only what you could do yourself on the same board.

Revoke at any time in Settings → API Keys — "Connected applications" for sign-ins, the token list for tokens.

## Manifest

[`server.json`](server.json) is the manifest published to the official MCP Registry as `com.qretro/retro`. Domain ownership is verified by DNS.

## Links

- [Setup guide](https://qretro.com/docs/mcp)
- [What the server does](https://qretro.com/mcp)
- [QRetro](https://qretro.com) — free online retrospective tool for agile teams
- [Registry entry](https://registry.modelcontextprotocol.io/v0/servers?search=qretro)
