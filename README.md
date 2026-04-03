# Hook Hub 🏢

A Claude Code project demonstrating advanced hook management and organization — centralizing, structuring, and automating hooks for large-scale or team-based workflows.

## What is Hook Hub? 💡

Hook Hub builds on Claude Code's hooks system to provide a centralized framework for managing multiple hooks across different lifecycle events. Instead of scattering hook scripts throughout a project, Hook Hub organizes them into a structured hub that is easy to maintain, extend, and share across teams.

## Key Concepts 🔑

### Claude Code Hooks

Claude Code hooks are shell commands that execute automatically in response to specific lifecycle events:

| Event | When It Fires |
|-------|--------------|
| `PreToolUse` | Before Claude executes any tool |
| `PostToolUse` | After Claude executes any tool |
| `Notification` | When Claude sends a notification |
| `Stop` | When Claude finishes a response |
| `SubagentStop` | When a subagent finishes |

Hooks are configured in `~/.claude/settings.json` (user-level) or `.claude/settings.json` (project-level).

### What Hook Hub Adds

- **Centralized hook scripts** — all hooks live in one `hooks/` directory
- **Dispatcher pattern** — a single entry point routes events to the right handler
- **Modular handlers** — individual scripts for each concern (logging, notifications, validation, etc.)
- **Shared utilities** — common helpers (timestamps, formatting, alerting) reused across hooks

## Project Structure 📁

```
.
├── hooks/
│   ├── dispatcher.sh          # Main entry point — routes events to handlers
│   ├── pre_tool/
│   │   ├── validate_input.sh  # Validates tool inputs before execution
│   │   └── log_request.sh     # Logs all tool requests
│   ├── post_tool/
│   │   ├── log_result.sh      # Logs tool results and durations
│   │   └── notify_slack.sh    # Sends Slack notifications on key events
│   ├── stop/
│   │   └── summarize.sh       # Generates a session summary on stop
│   └── utils/
│       ├── colors.sh          # Terminal color helpers
│       ├── timestamp.sh       # ISO timestamp generator
│       └── notify.sh          # Cross-platform desktop notifications
├── .claude/
│   └── settings.json          # Claude Code hook configuration
└── README.md
```

## How It Works 🤔

1. **Claude Code fires a lifecycle event** (e.g., `PostToolUse`).
2. **The dispatcher receives the event** via stdin as JSON.
3. **The dispatcher routes** to the appropriate handler(s) in `hooks/post_tool/`.
4. **Each handler** processes the event independently — logging, notifying, or validating.

### Example: `.claude/settings.json`

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "bash hooks/dispatcher.sh post_tool"
          }
        ]
      }
    ],
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "bash hooks/dispatcher.sh stop"
          }
        ]
      }
    ]
  }
}
```

### Example: `hooks/dispatcher.sh`

```bash
#!/bin/bash
# Reads the event JSON from stdin, routes to handlers in hooks/<event_type>/

EVENT_TYPE="$1"
EVENT_JSON=$(cat)

HOOKS_DIR="$(dirname "$0")/$EVENT_TYPE"

if [ -d "$HOOKS_DIR" ]; then
  for handler in "$HOOKS_DIR"/*.sh; do
    [ -f "$handler" ] && echo "$EVENT_JSON" | bash "$handler"
  done
fi
```

## Getting Started ▶️

1. **Clone the repository and check out this branch:**
   ```bash
   git clone https://github.com/emarco177/claude-code-crash-course.git
   cd claude-code-crash-course
   git checkout project/hookhub
   ```

2. **Walk through the commits to learn step by step:**
   ```bash
   git log --oneline --reverse
   ```

3. **Run Claude Code in the project directory** — hooks fire automatically as you work.

## Prerequisites 🛠️

- Claude Code CLI installed and authenticated
- `bash` (v4+)
- Git

## Learning Path 📚

Follow the commits in order to see how Hook Hub is built up incrementally:

1. Bare project with a single inline hook
2. Extracting hooks into dedicated scripts
3. Introducing the dispatcher pattern
4. Adding shared utilities
5. Building multi-event handlers (logging, Slack, desktop notifications)
6. Organizing hooks into a full hub structure

## Related Branches 🔗

| Branch | Topic |
|--------|-------|
| `project/hooks-notifications` | Intro to hooks — sound notifications and basic event triggers |
| `project/hookhub` | *(this branch)* Advanced hook management with the Hub pattern |

## License 📄

Apache License 2.0 — see the [LICENSE](LICENSE) file for details.
