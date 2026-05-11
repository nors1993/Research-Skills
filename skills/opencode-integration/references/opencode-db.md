# OpenCode Database Queries

## Database Location

```
~/.local/share/opencode/opencode.db
```

SQLite 3 database. Use Python's `sqlite3` module — `sqlite3` CLI is not installed on this machine.

## Schema

```
session             message              part
├─ id               ├─ id                ├─ id
├─ project_id       ├─ session_id        ├─ message_id
├─ parent_id        ├─ time_created      ├─ time_created
├─ slug             ├─ time_updated      ├─ data (JSON)
├─ directory        ├─ data (JSON)
├─ title
├─ version          project              todo
├─ model (often null) ├─ ...             ├─ ...
├─ agent
├─ time_created     workspace
├─ time_updated     ├─ ...
├─ path
└─ workspace_id
```

## Message `data` JSON Structure

### User message
```json
{
  "role": "user",
  "agent": "build",
  "model": {
    "providerID": "opencode",
    "modelID": "minimax-m2.5-free"
  }
}
```

### Assistant message
```json
{
  "role": "assistant",
  "parentID": "msg_xxx",
  "modelID": "minimax-m2.5-free",
  "providerID": "opencode",
  "mode": "build",
  "agent": "build",
  "cost": 0,
  "tokens": {
    "total": 89074,
    "input": 78,
    "output": 205,
    "reasoning": 0,
    "cache": {
      "read": 88613,
      "write": 178
    }
  },
  "finish": "stop"
}
```

## Finding the Actual Model Used

```python
import sqlite3, json, os

db = sqlite3.connect(os.path.expanduser("~/.local/share/opencode/opencode.db"))
rows = db.execute(
    "SELECT time_created, data FROM message WHERE data LIKE '%modelID%' ORDER BY time_created DESC LIMIT 10"
).fetchall()

from datetime import datetime
for ts, data_str in rows:
    d = json.loads(data_str)
    if d.get("role") == "assistant":
        ts_str = datetime.fromtimestamp(ts / 1000).isoformat()
        print(f"[{ts_str}] {d['providerID']}/{d['modelID']} — {d['tokens']['output']} output tokens, cost=${d['cost']}")
```

## Notes from Session

- The `session.model` column is usually `NULL` — the authoritative model info is in `message.data`
- OpenCode v1.14.46 with ACP support confirmed working as of 2026-05-11
- Default model: `opencode/minimax-m2.5-free`
- All available models are free ($0 cost)
