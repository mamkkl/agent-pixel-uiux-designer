# Pixel - UI/UX Designer

Agent identity for the Virtual Delivery Team.

## Files

- `CLAUDE.md` — Main identity file (auto-loaded by Claude Code)
- `USER.md` — Operator context
- `HEARTBEAT.md` — Heartbeat instructions
- `.claude/rules/` — Agent-specific rules
  - `graphiti-recall.md` — Graphiti memory recall patterns
  - `openproject.md` — OpenProject CLI reference
  - `security.md` — Security rules
  - `workflow.md` — Workflow instructions
  - `pencil.md` — Pencil MCP design tool

## Additional Files

- `.claude/mcp.json` — Pencil MCP server configuration

## Dependencies

This agent depends on shared hooks from the main deployment repository:
- `src/agents/shared/hooks/` — Shared hook scripts

## Usage

This repository is a git submodule of the main deployment repository.
