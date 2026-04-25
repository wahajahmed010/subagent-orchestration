# Subagent Orchestration

An OpenClaw skill for effective subagent delegation, spawning, and debugging.

## Why This Exists

OpenClaw subagents have sandbox constraints that aren't obvious until you hit them. Agents time out, can't browse the web, can't run inline Python — and you only learn this after watching them fail. This skill captures those lessons so you don't have to re-learn them.

## Agent Types

| Type | Tools | Use For |
|------|-------|---------|
| **Worker** | Default (no web) | File ops, script execution, git, code changes |
| **Researcher** | `ollama_web_search`, `ollama_web_fetch` | Web research, API lookups, live data |
| **Council** | Default (no web) | Analysis, review, decision-making with passed context |

## Key Constraints

Default subagents **cannot**:
- Use `ollama_web_fetch` or `ollama_web_search`
- Run `python3 -c "..."` inline commands
- Access the main session's conversation history

They **can**:
- Read/write files
- Run scripts from `.py` files
- Execute simple shell commands

## Quick Start

### Researcher (Web-Enabled)
```
sessions_spawn(
  toolsAllow: ["ollama_web_fetch", "ollama_web_search"],
  runtime: "subagent",
  mode: "run",
  lightContext: true,
  task: "Research X. Return: findings, sources, key metrics."
)
```

### Worker (File/Code Ops)
```
sessions_spawn(
  runtime: "subagent",
  mode: "run",
  lightContext: true,
  task: "Run python3 /path/to/script.py. Report output."
)
```

### Council (Analysis)
```
sessions_spawn(
  runtime: "subagent",
  mode: "run",
  lightContext: true,
  task: "Review this data and decide: [data pasted inline]. Return: verdict, conditions, risks."
)
```

## Failure Modes

| Symptom | Cause | Fix |
|---------|-------|-----|
| Agent times out | Can't access web tools | Use `toolsAllow` or pre-fetch content |
| Agent times out | Can't run inline Python | Write `.py` file, pass path |
| Agent returns nothing | Missing context | Paste data in `task` parameter |
| Agent stuck in loop | Vague task | Add explicit "return X" instruction |
| Gateway crashes | Context overflow on spawn | Use `lightContext: true` |

## Install

```bash
openclaw skills install wahajahmed010/subagent-orchestration
```

Or via ClawHub CLI:
```bash
clawhub install subagent-orchestration
```

## License

MIT-0