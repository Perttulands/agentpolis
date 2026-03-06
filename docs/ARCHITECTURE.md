# Polis Architecture

Polis is a distribution. Every tool is its own repo. This repo ties them together.

---

## Dependencies

External (not ours):
- `br` (beads_rust) — universal issue tracker, assumed present
- `claude` — needed by senate, argus, work
- `ubs` — optional, used by gate
- `tmux` — needed by work

Between our tools:

```
truthsayer
relay
senate
                    ── zero deps

gate ──────────→ truthsayer
chiron - - - - → gate
oathkeeper - - → relay
argus - - - - → relay
work - - - - -→ gate, relay
learning-loop ─→ work, chiron
```

Solid = required. Dashed = optional (degrades gracefully).

## Install Order

```
1. truthsayer
2. relay
3. senate
4. gate              (needs truthsayer)
5. oathkeeper
6. chiron
7. argus
8. work
9. learning-loop     (needs work, chiron)
```

## City Directory Structure

```
/home/polis/
├── agents/          # 13 agent workspaces
│   ├── hestia/      #   each contains workspace/ with SOUL.md, AGENTS.md, MEMORY.md
│   ├── athena/
│   ├── hierophant/
│   ├── hermes/
│   └── ...
├── docs/            # Canon: vision, mythology, golden truths
├── tools/           # Installed tool repos (git clone targets)
├── projects/        # Standalone projects, outside the walls
├── data/            # City data
├── plans/           # In-progress plans
└── agentpolis/      # This repo — the public hub
```

## Integration Model

All tool boundaries use `--json` for structured output. JSONL is the durable interchange format. SQLite is a rebuildable cache from JSONL.

### The Pantheon Pattern

Agent orchestration uses `work` to spawn coding agents:

1. `work` creates a tmux session
2. Launches `claude` with the task context
3. Delivers the task via `tmux load-buffer`
4. Captures the workspace diff as a trace
5. Runs `gate` for quality checks
6. Closes the bead with results

### Agent Communication

Agents communicate through `relay` (Hermes). Messages are durable — zero-loss delivery with JSONL backing. Agents can be woken from sleep via `relay send <agent> "msg" --wake`.

### Quality Pipeline

```
code change → truthsayer scan → ubs scan → gate check → merge
```

Every merge passes through Cerberus (the gate). Three checks, unanimous verdict, or the gate stays closed.

## The Inside/Outside Boundary

Public repos are **outside the walls** — generic, standalone, shareable. No Polis data ever lives in a public repo.

Agent workspaces, docs, and data are **inside the walls** — private, accumulated knowledge.

The boundary is enforced structurally:
- Each tool repo's `.gitignore` excludes all Polis-specific files
- `git clone` of any tool produces a working system with no Polis dependency
- Polis configuration is layered via gitignored config files
- `git pull` never touches Polis data

This means tools can be upgraded cleanly (`git pull`) without risking city data.
