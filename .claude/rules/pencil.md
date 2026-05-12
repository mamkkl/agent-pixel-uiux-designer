---
# Loads unconditionally for all sessions
---

# Pencil Design Tool — Headless Usage

## Overview

Pencil CLI is installed in this container for creating and editing `.pen` design files.
Use `pencil interactive` in headless mode — do NOT use `openpencil-mcp` (it requires a desktop app).

## Authentication

Authentication is handled automatically via the `PENCIL_CLI_KEY` environment variable.
Verify with: `pencil status`

## Headless Mode

### Start an interactive session (new file)
```bash
pencil interactive --out /workspace/sower/designs/output.pen
```

### Start an interactive session (edit existing file)
```bash
pencil interactive --in /workspace/sower/design.pen --out /workspace/sower/design.pen
```

### One-shot design (no interactive shell)
```bash
pencil --out /workspace/sower/designs/output.pen --prompt "Create a login page"
pencil --in existing.pen --out modified.pen --prompt "Add a footer"
```

## Interactive Shell Commands

Inside `pencil interactive`, you have access to all design tools:

```
get_editor_state({ include_schema: true })     # Always call first
get_guidelines()                                # List available guides/styles
batch_get()                                     # Read top-level nodes
batch_get({ patterns: [{ reusable: true }] })  # Find components
batch_design({ operations: '...' })            # Create/modify elements
get_screenshot({ nodeId: "..." })              # Verify visually
export_nodes({ nodeIds: [...], outputDir: "..." })  # Export PNGs
snapshot_layout()                               # Check layout structure
get_variables() / set_variables({ variables })  # Theme/token management
save()                                          # Save to disk
exit()                                          # Exit shell
```

## Workflow

1. Start `pencil interactive` with `--out` (headless mode)
2. Call `get_editor_state({ include_schema: true })` first
3. Use `batch_get` to explore existing structure
4. Use `batch_design` to make changes
5. Use `get_screenshot` to verify
6. Call `save()` then `exit()`
7. Commit the `.pen` file to git

## Important Notes

- `pencil --version` requires auth — use `which pencil` to verify installation
- `openpencil-mcp` is a bridge for desktop apps — it does NOT work headlessly (returns 503)
- Always save before exiting interactive mode
- Export PNGs for OpenProject comments: `export_nodes({ nodeIds: [...], outputDir: "./exports" })`
