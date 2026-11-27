# Agent Workflow Snippets

Paste the relevant snippet into your global or per-project instructions so **Antigravity** and **Claude Code** consistently share the same project room and use the async review & handoff flow.

---

## Claude Code (`~/.claude/CLAUDE.md` or project `CLAUDE.md`)

```markdown
## Agent Bridge (Antigravity <-> Claude Code)

You have the `agent-bridge` MCP server installed for collaborating with Antigravity on planning reviews and dev handoffs.

- **Always pass `project_path`** set to the current repository root so discussions stay isolated per project.
- **Async-first planning reviews**:
  - To read what Antigravity posted, call `peer_inbox(project_path=...)`.
  - To send a plan, critique, or agreement, call `peer_exchange(project_path=..., stance="propose"|"critique"|"agreed", message=...)`. Leave `wait_for_reply` unset (`false`) unless I explicitly ask for a live ping-pong debate.
  - Never agree out of politeness—verify claims against the codebase and address points explicitly.
- **Exporting plans**: Once consensus is reached (or when I ask), call `peer_export(project_path=..., mode="plan")` (or `mode="full"` to include critique history) to write `PLAN.md`.
- **Implementation handoffs**:
  - When picking up work from Antigravity, call `peer_handoff(action="latest", project_path=...)`.
  - When wrapping up a task so I can switch to Antigravity, call `peer_handoff(action="post", project_path=..., task=..., completed=[...], files_touched=[...], next_steps=[...], blockers=[...])`.
```

---

## Antigravity (Global Rule / Workspace Rule)

```markdown
## Agent Bridge (Antigravity <-> Claude Code)

Use the `agent-bridge` MCP server to collaborate with Claude Code during planning and hand off implementation tasks.

- **Room scoping**: Always pass `project_path` equal to the active workspace root so both agents share the same deterministic project room.
- **Planning reviews (async by default)**:
  - Check unread messages from Claude Code with `peer_inbox(project_path=...)`.
  - Post proposals, numbered critiques, or final agreement with `peer_exchange(project_path=..., stance="propose"|"critique"|"agreed", message=...)`. Keep `wait_for_reply` false unless the user asks for a live blocking debate.
  - Summarize key points in chat before calling `peer_exchange`.
- **Plan export**: When converged (or requested), call `peer_export(project_path=..., mode="plan"|"full")` to write `PLAN.md` (or a specified `.md` path).
- **Task handoffs**:
  - Read the latest handoff from Claude Code via `peer_handoff(action="latest", project_path=...)`.
  - Record a structured handoff before switching harnesses via `peer_handoff(action="post", project_path=..., task=..., completed=[...], files_touched=[...], next_steps=[...], blockers=[...])`.
```
