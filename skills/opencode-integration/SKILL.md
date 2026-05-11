---
name: opencode-integration
description: Use OpenCode as a coding subagent via ACP protocol. Write and debug code through OpenCode's free models (minimax-m2.5-free, etc.), query its session database, and verify which model actually ran.
---

# OpenCode Integration

OpenCode is an AI coding CLI installed via npm. It supports **ACP (Agent Client Protocol)** and can be invoked as a subagent through Hermes `delegate_task`.

## Trigger

- User asks to use OpenCode, connect to OpenCode, or write code with OpenCode
- User mentions `opencode` CLI
- Task involves delegating coding work to a local agent

## Invocation via ACP

Use `delegate_task` with ACP transport:

```
delegate_task(
  goal="<task description>",
  acp_command="opencode",
  acp_args=["acp"],
  toolsets=["terminal", "file"]
)
```

OpenCode will run as a subagent in its own process, using its locally configured model, and return results.

## OpenCode Details

- **Install path**: `~/.npm-global/bin/opencode`
- **Version**: check with `opencode --version`
- **Help**: `opencode --help`

### Available Free Models

```
opencode/big-pickle
opencode/minimax-m2.5-free          ← default
opencode/nemotron-3-super-free
opencode/ring-2.6-1t-free
```

List models: `opencode models`

## CRITICAL PITFALL: Model Tracking

The `delegate_task` return object includes a `model` field — **this is Hermes's own model (e.g., deepseek-v4-pro), NOT the model OpenCode actually used.**

To find the actual model OpenCode used, query its SQLite database:

```python
import sqlite3, json
db = sqlite3.connect(os.path.expanduser("~/.local/share/opencode/opencode.db"))
rows = db.execute("SELECT data FROM message ORDER BY time_created DESC LIMIT 5").fetchall()
for r in rows:
    d = json.loads(r[0])
    if d.get("role") == "assistant":
        print(f"provider: {d['providerID']}, model: {d['modelID']}")
```

Or from the terminal via Python one-liner:
```bash
python3 -c "
import sqlite3, json
db = sqlite3.connect('$HOME/.local/share/opencode/opencode.db')
rows = db.execute(\"SELECT data FROM message WHERE data LIKE '%modelID%' ORDER BY time_created DESC LIMIT 3\").fetchall()
for r in rows:
    d = json.loads(r[0])
    if 'modelID' in d:
        print(f\"{d.get('role','?')}: provider={d.get('providerID','?')}, model={d['modelID']}\")
"
```

### Database Schema

- **Database path**: `~/.local/share/opencode/opencode.db`
- **Key tables**: `session`, `message`, `part`, `project`, `todo`
- **`message` table**: `id`, `session_id`, `time_created`, `time_updated`, `data` (JSON)
- **`data` JSON fields** (assistant messages): `role`, `modelID`, `providerID`, `mode`, `agent`, `tokens`, `cost`, `finish`
- **`session` table**: includes `model` column (often null; the real model is in message data)

### Token / Cost

OpenCode's free models have `cost: 0`. Tokens are logged in the message `data.tokens` JSON.

## Verify OpenCode is Working

1. `opencode --version` — should return version number
2. `opencode models` — should list available models
3. Run a trivial task via `delegate_task` with ACP to confirm end-to-end connectivity

## Common Commands Reference

| Command | Purpose |
|---------|---------|
| `opencode --version` | Version check |
| `opencode --help` | Help |
| `opencode models` | List available models |
| `opencode acp` | Start ACP server (used by delegate_task) |
| `opencode agent list` | List configured agents |
| `opencode db path` | Print database path |
| `opencode session` | Session management |

## Reference Files

- `references/opencode-db.md` — Database schema, query recipes, message JSON structure
