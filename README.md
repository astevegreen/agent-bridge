# agent-bridge

Local-first MCP bridge between **Antigravity** and **Claude Code** for low-friction collaborative planning reviews, Markdown plan exports, and lightweight development task handoffs—with opt-in live ping-pong debate mode.

> Based on [`subamanis/agent-bridge`](https://github.com/subamanis/agent-bridge) (MIT License, Copyright (c) 2026 Petros Papatheodorou).

## Why This Exists

When planning a project or feature, having both Antigravity and Claude Code review and critique each other's architecture produces a stronger build plan—without requiring complex synchronous multi-agent orchestration. Once implementation begins, you can alternate between Antigravity and Claude Code on individual tasks while keeping clean handoff context.

## Key Features

- **Async-First Mailbox (`peer_exchange` + `peer_inbox`)**: `peer_exchange` defaults to `wait_for_reply: false` so messages queue immediately without blocking your chat turn. Switch to the other harness whenever you're ready and call `peer_inbox()` to read unread proposals or critiques.
- **Project-Scoped Rooms (`project_path`)**: Pass your workspace path to automatically walk up to the nearest `.git` root and derive a deterministic room ID (`<repo-slug>-<hash6>`). Both harnesses land in the exact same room even if one passes a subdirectory, while state stays neatly in the central `data/` directory.
- **On-Demand Markdown Export (`peer_export`)**: Export the agreed plan (`mode: "plan"`), the full plan + critique transcript (`mode: "full"`), or the latest dev handoff (`mode: "handoff"`) directly to `<project_path>/PLAN.md`, `docs/plans/<topic>.md`, or `HANDOFF.md` (with atomic writes and `.md`/`.markdown` safety guardrails).
- **Task Handoff Notes (`peer_handoff`)**: Record structured implementation handoffs (`task`, `completed`, `files_touched`, `next_steps`, `blockers`, `notes`) stored separately from the planning transcript so resetting a planning session never wipes active dev handoffs.
- **Opt-In Live Debate (`wait_for_reply: true` / `peer_wait`)**: Need a live back-and-forth ping-pong debate? Pass `wait_for_reply: true` to `peer_exchange` or call `peer_wait()`—including heartbeat renewals, unread-inbox guards, wrong-turn protection, and standby background listener support.
- **Zero Dependencies**: Pure Node.js (`server.cjs`, `setup.cjs`), cross-platform (Windows & Linux/macOS).

## Installation

Register `agent-bridge` in both `~/.gemini/config/mcp_config.json` (Antigravity) and `~/.claude.json` (Claude Code):

```bash
node setup.cjs
node setup.cjs --check
```

Other CLI commands:

```bash
node server.cjs --selftest              # Run full automated test suite
node server.cjs --rooms                 # List all active project rooms
node server.cjs --status --project .    # Inspect room state for a repo
node server.cjs --reset --project .     # Archive & reset planning messages (preserves handoffs)
node setup.cjs --uninstall              # Remove from Antigravity & Claude Code configs
```

## MCP Tools

All tools accept optional `project_path` (recommended) or explicit `room` to scope discussions per repository.

| Tool | Purpose |
| :--- | :--- |
| `peer_exchange` | Send a `propose`, `critique`, or `agreed` message. Non-blocking by default (`wait_for_reply: false`); pass `wait_for_reply: true` for live debate. |
| `peer_inbox` | Instantly check and consume unread peer messages without blocking. |
| `peer_export` | Render and write Markdown (`mode: "plan" \| "full" \| "handoff"`) to `PLAN.md`, `HANDOFF.md`, or a custom `.md` path. |
| `peer_handoff` | `post`, `latest`, `history`, or `clear` structured dev task handoff notes between Antigravity and Claude Code. |
| `peer_wait` | Opt-in blocking wait for live ping-pong debates (with `timeout_sec` and `standby` support). |
| `peer_status` | Inspect room state/transcript (`status`), list all rooms (`list_rooms`), or archive & reset planning messages (`reset`). |

## Typical Workflows

### 1. Async Planning Review (Default)
1. In **Antigravity**: *"Draft a build plan for this repo and send it to Claude via `peer_exchange` with `project_path`."*
2. In **Claude Code**: *"Check `peer_inbox` for this repo, critique the plan against the codebase, and reply via `peer_exchange`."*
3. Repeat until one agent sends `stance: "agreed"`.
4. Run `peer_export(mode: "plan")` (or `mode: "full"`) to write `PLAN.md` into the repo.

### 2. Alternating Harnesses During Implementation
1. When finishing a task in **Claude Code**: *"Leave a `peer_handoff` note summarizing what we built and what's next."*
2. When picking up in **Antigravity**: *"Check `peer_handoff(action: "latest")` for this repo and continue with the next step."*

See [docs/agent-workflows.md](docs/agent-workflows.md) for copy-ready instruction snippets you can paste into `CLAUDE.md` and Antigravity rules.

## Environment Variables

- `AGENT_BRIDGE_DATA_DIR`: Override the room state directory (default: `./data`).
- `AGENT_BRIDGE_ROLE`: Default agent identity (`antigravity` | `claude`).
- `ANTIGRAVITY_CONFIG_DIR`: Override `~/.gemini/config` path in `setup.cjs`.
- `CLAUDE_CONFIG_FILE`: Override `~/.claude.json` path in `setup.cjs`.

## License

MIT License — see [LICENSE](LICENSE). Originally created by Petros Papatheodorou (`subamanis/agent-bridge`), customized by `astevegreen`.
