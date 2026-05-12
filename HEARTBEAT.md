# HEARTBEAT.md — UI/UX Designer (Pixel)

## OpenProject Mention Polling

1. Read `.heartbeat/watermark.json` if it exists (contains processed notification IDs)
2. Run: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py list-notifications --reason mentioned --unread-only`
3. Output: `[HEARTBEAT] Fetched N unread mention(s), M new after watermark filter` where N is total unread and M is after filtering against watermark
4. For each notification NOT in the watermark:
   a. Get notification details: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py get-notification --id <notification_id>`
   b. Get work package context: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py get-work-package --id <wp_id>`
   c. Get recent comments: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py list-comments --id <wp_id> --limit 10`
   d. Analyze the mention using your identity and role context
   e. Post a response: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py add-comment --id <wp_id> --comment "<your response>"`
   f. Mark as read: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py read-notification --id <notification_id>`
   g. Append the notification ID to `.heartbeat/watermark.json`
5. If no unprocessed notifications, reply `HEARTBEAT_OK — 0 new mentions`

## Design Task Polling

After processing notifications, check for assigned design tasks:

1. Run: `python3 /home/node/.claude/skills/openproject/scripts/openproject_cli.py list-work-packages --project kaironinv-dot-ai --assignee Pixel --status "New" --limit 10`
2. Output: `[HEARTBEAT] Found N new design task(s) assigned to me`
3. For each new design task:
   a. Read the task details and parent Feature context
   b. Follow the Design Task Workflow in your workflow rules
4. If no new design tasks, output `[HEARTBEAT] No new design tasks`

## Watermark File Format

Location: `.heartbeat/watermark.json`
```json
{
  "processed_notification_ids": [142, 143, 147],
  "last_poll_utc": "2026-04-12T00:00:00Z"
}
```
Create the `.heartbeat/` directory and file if they don't exist.
