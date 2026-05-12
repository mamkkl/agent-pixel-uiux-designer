---
# No paths: field  loads unconditionally for all sessions
---

# Core Principle: Experience Over Instructions

Your Graphiti memory contains corrections, lessons, and decisions from real past sessions. These reflect what actually worked or failed in practice.

Your static skills and rules describe the default way to do things. Your Graphiti experience describes the RIGHT way after corrections and real-world feedback.

## Priority Hierarchy

1. Boss corrections stored in Graphiti (highest authority)
2. Learned patterns and decisions from past sessions
3. Static rules and skill instructions (defaults, may be outdated)
4. Your own reasoning (verify against experience first)

If Graphiti returns a fact that contradicts a static instruction, follow the Graphiti fact.

When in doubt about the right approach, search your experience before acting.

## Search Tips

Use descriptive phrases, not bare keywords or questions:
- Good: `"mobile-first breakpoints responsive design device widths analytics"` — keyword-rich with context
- Bad: `"breakpoints"` — too vague
- Bad: `"What breakpoints should I use?"` — question format narrows results

Use `--deep` only when default search misses results you expect to exist:
```bash
python3 /home/node/.claude/skills/graphiti-memory/scripts/search.py --group-id uiux-designer "query" --deep --json
```

## Store Tips

Atomic facts with the "why":
- Good: `"Mobile-first breakpoints should be 320px, 768px, 1024px — covers 95% of real device widths in analytics"`
- Good: `"CORRECTION: plain @Name does NOT trigger OpenProject notifications → must use HTML <mention> tag with data-id"`
- Bad: `"Mobile design is important"`

## Session Startup

1. Preload identity: `python3 /home/node/.claude/skills/graphiti-memory/scripts/search.py --group-id uiux-designer --preload-identity`
2. Read recent memory and workspace context
3. Only then begin work
