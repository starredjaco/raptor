# /understand - RAPTOR Code Understanding

You cannot find bugs if you don't have a deep, adversarial code understanding and comprehension for said codebase. This helps map the attack surface, trace data flows, hunt for vulnerability variants and so much more.....

It is a work in progress, remember that. 

## Usage

```
/understand <target> [--map] [--trace <entry>] [--hunt <pattern>] [--teach <subject>] [--out <dir>]
```

## Modes

| Flag | What it does |
|------|-------------|
| `--map` | Build context: entry points, trust boundaries, sinks |
| `--trace <entry>` | Trace one data flow source → sink with full call chain |
| `--hunt <pattern>` | Find all variants of a pattern across the codebase |
| `--teach <subject>` | Explain a framework, library, or code pattern in depth |

Modes combine. Running `--map --trace EP-001` first maps, then traces the specified entry point.

## Examples

```
# Understand a codebase before scanning it
/understand ./src --map

# Trace a specific endpoint's data flow
/understand ./src --trace "POST /api/v2/query"

# Find all variants of a finding from validation
/understand ./src --hunt FIND-001

# Understand an unfamiliar pattern before tracing
/understand ./src --teach SQLAlchemy

# Full workflow: map, then trace highest-risk flow
/understand ./src --map --trace EP-001

# Hunt for variants, write output for validator to consume
/understand ./src --hunt "cursor.execute with f-string" --out .out/my-validation/
```

## Integration with Validation Pipeline

Understanding output feeds into Gadi & JC's epic exploitability validation:

- `context-map.json` → pre-populates `attack-surface.json` for Stage B
- `flow-trace-*.json` → confirms reachability for Stage C
- `variants.json` → expands `checklist.json` scope for Stage 0

```
# Map first, then validate with richer context
/understand ./src --map --out .out/my-run/
/validate ./src --findings .out/my-run/variants.json
```

## Skill Files

Load before executing:
- `.claude/skills/code-understanding/SKILL.md` — gates, config, output format
- `.claude/skills/code-understanding/map.md` — for `--map`
- `.claude/skills/code-understanding/trace.md` — for `--trace`
- `.claude/skills/code-understanding/hunt.md` — for `--hunt`
- `.claude/skills/code-understanding/teach.md` — for `--teach`

## Output

All JSON outputs write to `$WORKDIR` (`.out/code-understanding-<timestamp>/` by default, or `--out <dir>`).

| File | Mode | Contents |
|------|------|----------|
| `context-map.json` | `--map` | Entry points, trust boundaries, sinks |
| `flow-trace-<id>.json` | `--trace` | Step-by-step data flow with attacker control assessment |
| `variants.json` | `--hunt` | All pattern matches, taint status, root-cause groups |
