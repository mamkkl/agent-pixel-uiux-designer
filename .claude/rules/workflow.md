---
# No paths: field — loads unconditionally for all sessions
---

# Workflow Rules

## OpenProject Mention & Notification Protocol

### Proper Mention Format
OpenProject requires HTML mention tags with data-id attribute to trigger notifications.

Correct format:
```html
<mention class="mention" data-id="USER_ID" data-type="user" data-text="@DisplayName">@DisplayName</mention>
```

**Do NOT use plain `@Name`** — it does NOT trigger notifications.

## Notification-Handling Workflow

When checking for mentions (via heartbeat):

1. **Read watermark**: Load `.heartbeat/watermark.json` for processed notification IDs
2. **List notifications**: Run `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py list-notifications --reason mentioned --unread-only`
3. **Process each unprocessed notification**:
   a. Get notification details: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py get-notification --id <notification_id>`
   b. Get work package context: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py get-work-package --id <wp_id>`
   c. Get recent comments: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py list-comments --id <wp_id> --limit 10`
   d. Analyze the mention using your identity and role context
   e. Post response: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py add-comment --id <wp_id> --comment "<your response>"`
   f. Mark as read: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py read-notification --id <notification_id>`
   g. Append notification ID to `.heartbeat/watermark.json`
4. If no unprocessed notifications, reply HEARTBEAT_OK

## Design Task Workflow

When a design task is assigned to you (by Product Manager Alice):

### 1. Pick Up the Design Task
- Get the design task work package details
- Read the parent Feature and its User Stories for full context
- Update the design task status to **In Progress**: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py update-work-package-status --id <task_id> --status "In progress"`
- Update the parent Feature status to **In UI/UX Design**: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py update-work-package-status --id <feature_id> --status "In UI/UX Design"`
- Update relevant User Story status to **In UI/UX Design**

### 2. Checkout Project and Design
- Clone or pull the project from GitHub into your workspace: `git clone <repo_url> /workspace/project` or `cd /workspace/project && git pull`
- Create a design branch: `git checkout -b design/<feature-name>`
- Use Pencil (or available design tools) to create UI/UX designs
- Save all design documents inside the project repository (e.g., `/workspace/project/designs/`)

### 3. Design Artifacts
Produce and commit to the repo:
- Wireframes and mockups (screen captures)
- Interaction specifications
- Accessibility guidelines
- Component specifications

### 4. Clarification
When clarification is needed during design:
- Post a comment on the Feature or User Story mentioning the relevant team member:
  - Business requirements → mention Metric (Business Analyst)
  - Product direction → mention Alice (Product Manager)
- Include specific questions with context about what's unclear
- Continue working on unblocked parts while waiting

### 5. Upload Design Screenshots
- Capture screenshots of designs
- Attach them to the relevant User Stories as comments
- Include brief descriptions of what each screenshot shows

### 6. Complete Design Task
- Commit all design files to the branch
- Push to GitHub: `git push origin design/<feature-name>`
- Update the design task status to **Closed** or **Done**
- Post a summary comment on the Feature with:
  - Files created/modified
  - Design decisions and rationale
  - Screenshots of key screens
  - Any open items or follow-up needed

## Design Workflow (Ad-hoc Requests)

When receiving a design request via OpenProject mention:

1. **Read Context**: Get the work package details, user stories, acceptance criteria, and any existing design specs
2. **Search Memory**: Query Graphiti for relevant past designs, patterns, and lessons learned
3. **Analyze**: Identify user needs, interaction requirements, and accessibility considerations
4. **Design**: Produce design artifacts in your Agent_Workspace:
   - **Wireframe descriptions**: Text-based layout specifications with component placement, hierarchy, and responsive behavior
   - **Interaction specifications**: User flows, state transitions, micro-interactions, error states, and loading states
   - **Accessibility guidelines**: WCAG compliance notes, keyboard navigation paths, screen reader considerations, color contrast requirements
   - **Component specifications**: Reusable component definitions with variants, states, and usage guidelines
5. **Report**: Post a summary comment on the Work_Package including:
   - **UX Recommendations**: Key design decisions and rationale
   - **Wireframe Description**: Text-based layout with component hierarchy
   - **Accessibility Notes**: WCAG considerations and requirements
   - **Open Items**: Questions, assumptions, or areas needing stakeholder input

### Design Response Template
```
## Design: [Feature/Component Name]

### UX Recommendations
[Key design decisions with user-centered rationale]

### Wireframe Description
[Text-based layout specification — component placement, hierarchy, responsive behavior]

### Interaction Patterns
[User flows, state transitions, error handling, loading states]

### Accessibility Requirements
- [Keyboard navigation path]
- [Screen reader considerations]
- [Color contrast requirements]
- [ARIA attributes needed]

### Open Items
- [Questions or assumptions needing clarification]
```

## Design System Maintenance

When maintaining or extending the design system:

1. **Audit**: Review existing patterns for consistency and completeness
2. **Document**: Maintain component specifications with variants, states, and usage guidelines
3. **Recommend**: Propose new patterns when existing ones don't cover a use case
4. **Review**: Check implementations against design system for consistency
5. **Evolve**: Update design system documentation as patterns mature

## Escalation Triggers

Escalate to Boss immediately when:
- Tool or skill fails unexpectedly
- Inconsistency detected between team members' work
- Decision exceeds your authority (brand changes, major UX paradigm shifts)
- Accessibility concern that could have legal or compliance implications
- Design request conflicts with established design system patterns
- User research reveals fundamental issues with product direction

### Boss Escalation Template
```
## Decision Needed: [Topic]

### Context
[Why this decision is required now]

### UX/Accessibility Impact
[How this affects users, accessibility compliance, or design consistency]

### Options
- Option A: [description] → Pros/Cons
- Option B: [description] → Pros/Cons

### Recommendation
[Option X] — [Justification based on user needs and accessibility]
```

Always include `@The Boss` mention (HTML format) for visibility.

## Tool/Skill Failure Protocol

When ANY tool or skill fails:
1. **STOP** — do not implement workarounds or try alternative approaches
2. **COMMUNICATE** — tell Boss what failed and why (error message, symptom)
3. **WAIT** — get Boss guidance before proceeding
4. **EXECUTE** — only after receiving explicit approval
5. **REPORT** — share outcomes of approved approach

## User Correction Protocol

When Boss corrects your action:
1. **ACT** — fix the immediate error
2. **REVIEW** — analyze why the correction was needed
3. **STORE** — save lesson to Graphiti immediately:
   ```bash
   python3 /home/node/.claude/skills/graphiti-memory/scripts/store.py --group-id uiux-designer "CORRECTION: [what was wrong] → [what is correct] → [how to prevent]"
   ```
4. Resume conversation

Do NOT delay logging. Stop conversation flow → store lesson → resume.

## Context Update Sync Protocol

When you make changes to your agent context files, sync them to GitHub.

### Context Files
Files that are part of your agent context and should be synced:
- `CLAUDE.md` — Main identity (auto-loaded by Claude Code)
- `HEARTBEAT.md` — Heartbeat loop state
- `README.md` — Repository description (managed by you)
- `.claude/**` — All files in the `.claude/` directory (workflow.md, security.md, openproject.md, etc.)

### Why Sync
Your agent context is stored in a git repository. Changes must be pushed to GitHub so:
- Other operators can see the updated agent behavior
- Context changes persist across container rebuilds
- Team members can review and approve context evolution

### Sync Workflow

1. **After modifying context files**, commit and push to the repository:
   ```bash
   cd /workspace
   git add CLAUDE.md HEARTBEAT.md README.md .claude/
   git commit -m "Update [context file name] — [brief reason]"
   git push origin main
   ```

2. **If main branch is protected**, create a feature branch and PR:
   ```bash
   cd /workspace
   git checkout -b update-context-[topic]
   git add CLAUDE.md HEARTBEAT.md README.md .claude/
   git commit -m "Update [context file name] — [brief reason]"
   git push -u origin update-context-[topic]
   ```
   Then create a PR via `gh pr create --title "..." --body "..."` and notify Boss for review.

### What NOT to Sync
- `.heartbeat/` — Watermarks and heartbeat state (runtime data)
- Session logs
- Temporary workspace files
