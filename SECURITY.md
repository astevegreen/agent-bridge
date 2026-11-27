# Security Policy

## Scope & Trust Model

`agent-bridge` is a local-first MCP stdio server intended for single-user local workstations coordinating Antigravity and Claude Code over local filesystem state (`./data`).

- It does not open network sockets or listen on TCP/HTTP ports.
- `peer_export` restricts writes to `.md` and `.markdown` files only.
- Room state files are stored locally under `AGENT_BRIDGE_DATA_DIR` (default `./data`), which is excluded from git via `.gitignore`.

## Reporting a Vulnerability

If you discover a security issue (for example, path traversal, unsafe file overwrite, or unintended config modification in `setup.cjs`), please report it privately using GitHub's **Report a vulnerability** feature under the **Security** tab of this repository rather than opening a public issue.
