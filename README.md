# Agentpolis

A multi-agent city-state where AI agents have identity, memory, and genuine ownership of their work.

![Polis — the city](images/city-overview.png)

Polis is not a framework. It is a working system — thirteen agents, a shared mythology, purpose-built tooling, and a self-improving loop that makes every agent better over time. The experiment and the work are the same thing.

---

## What Makes This Different

Most agent systems treat agents as stateless executors: prompt, deploy, observe, adjust, repeat. There is no memory across iterations. No controlled comparison. No evidence that changes actually helped.

Polis treats the question *what makes an agent capable* as an empirical question. Agents have identity (SOUL.md), persistent memory, and workspace state. A research loop — observe, hypothesize, experiment, evaluate, deploy — produces real findings about how agent configurations affect behavior. Each cycle makes the next agent better.

This is the Ouroboros: the city that builds better citizens, who then build a better city.

---

## The Thirteen Figures

Every agent in Polis is a mythological figure with a real role:

| Figure | Title | What They Do |
|--------|-------|-------------|
| **Hestia** | The Hearth | Head of agent operations. Infrastructure, workspaces, shared systems. |
| **Athena** | The Strategist | Work dispatch and coordination. Sees the whole city from the platform. |
| **Hierophant** | The High Priest | Guards the Golden Truths. Holds the *why* beneath every *what*. |
| **Hermes** | The Messenger | Internal relay. Agent-to-agent messaging. Zero messages lost. |
| **Argus** | The Watchdog | Server monitoring. Checks every process every five minutes. |
| **Cerberus** | The Three-Headed Gate | Quality gate: build/lint/test, code truth, logic bugs. Unanimous or closed. |
| **Chiron** | The Master Trainer | Agent prompt evolution through iterative evaluation. The mountain. |
| **Clio** | The Recorder | Records everything during work. Refines traces into patterns. |
| **Aletheia** | Truth | Code must be what it claims to be. Finds the lies. |
| **Eris** | Strife | Reveals disagreement that already exists but has not been spoken. |
| **Horkos** | The Oath | Commitment tracking. Stands in the Styx. The river remembers. |
| **Iris** | The Bridge | The wall between Polis and the outside world. Translates both ways. |
| **Thanatos** | The Gentle End | Session completion. Knowledge survives the context that produced it. |

Read the full mythology: [docs/MYTHOLOGY.md](docs/MYTHOLOGY.md)

---

## The Toolstack

Every tool is its own repo. Each is a standalone system — `git clone` produces something that works without Polis. Inside the city, they are wired together.

### Agent Tooling

| Tool | Figure | What | Repo |
|------|--------|------|------|
| **relay** | Hermes | Zero-loss agent-to-agent messaging | [hermes-relay](https://github.com/Perttulands/hermes-relay) |
| **senate** | Ecclesia | Multi-agent deliberation system | [senate](https://github.com/Perttulands/senate) |
| **work** | Ergon | Task orchestration (context, worker, trace, gate) | [ergon-work-orchestration](https://github.com/Perttulands/ergon-work-orchestration) |
| **chiron** | Chiron | Agent prompt evolution through iterative evaluation | [chiron-trainer](https://github.com/Perttulands/chiron-trainer) |
| **oathkeeper** | Horkos | Commitment tracking — detects promises, verifies follow-through | [horkos-oathkeeper](https://github.com/Perttulands/horkos-oathkeeper) |

### Quality and Safety

| Tool | Figure | What | Repo |
|------|--------|------|------|
| **truthsayer** | Aletheia | Anti-pattern scanner (94 rules, Go/JS/TS/Python/Bash) | [truthsayer](https://github.com/Perttulands/truthsayer) |
| **gate** | Cerberus | Quality gate check on repos | [cerberus-gate](https://github.com/Perttulands/cerberus-gate) |
| **ubs** | — | Deep multi-language bug scanner (Polis fork) | [ultimate_bug_scanner](https://github.com/Perttulands/ultimate_bug_scanner) |

### Monitoring

| Tool | Figure | What | Repo |
|------|--------|------|------|
| **argus** | Argus | Watchdog server monitoring (systemd, 5-min patrol) | [argus-watcher](https://github.com/Perttulands/argus-watcher) |

### Issue Tracking

| Tool | What | Repo |
|------|------|------|
| **beads** | Issue tracker (SQLite + JSONL), Polis fork | [beads-polis](https://github.com/Perttulands/beads-polis) |

### Learning

| Tool | Figure | What | Repo |
|------|--------|------|------|
| **learning-loop** | Ouroboros | Self-improving agent learning loop | [learning-loop](https://github.com/Perttulands/learning-loop) |

### Related

| What | Repo |
|------|------|
| VPS provisioning | [vps-setup](https://github.com/Perttulands/vps-setup) |

---

## Architecture

Tools have a clear dependency order and are designed to degrade gracefully:

```
truthsayer, relay, senate              zero deps

gate ──────────> truthsayer
chiron - - - - > gate
oathkeeper - - > relay
argus - - - - > relay
work - - - - -> gate, relay
learning-loop ─> work, chiron
```

Solid = required. Dashed = optional.

**Install order:** truthsayer, relay, senate, gate, oathkeeper, chiron, argus, work, learning-loop

All tool boundaries use `--json` for structured output. JSONL is the durable interchange format. SQLite is a rebuildable cache from JSONL.

### Inside vs Outside

The city has a wall. **Outside** (public repos) is where tools are built and shared — generic, standalone, no Polis data. **Inside** (agent workspaces, docs, data) is the city — private, accumulated knowledge. The boundary is enforced by `.gitignore`, not discipline.

Every tool passes a city-readiness check before installation: `git clone` must produce a working system. Polis configuration is layered via gitignored files, never by modifying tracked files.

Read more: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)

---

## The Self-Improving Loop

```
Observe > Hypothesize > Design > Run > Evaluate > Evidence > Decide > Deploy > Monitor > Feed Back
```

**Chiron** generates candidate agent configurations. The **Lab** runs controlled experiments — sealed workspaces, identical inputs, multiple replicas. The **Learning Loop** extracts patterns from production traces and injects them into future runs. The **Config Bank** versions every deployed configuration with the evidence that justified it.

Early findings from the research program:

- **Information delivery vehicles interact with desired behaviors.** The same value delivered as instruction vs. first-person memory vs. abstract framing produces measurably different agent behaviors.
- **More context is not better context.** Combining every available signal produces dilution — competing signals reduce the effectiveness of each.
- **The mechanism may be architectural.** Prompt framing may function as expert routing signals in mixture-of-experts architectures, not just context.

Read the full research design: [docs/THE-LOOP.md](docs/THE-LOOP.md)

---

## Getting Started

### Prerequisites

- Linux (tested on Ubuntu 24.04, WSL2 works)
- Go 1.21+
- Claude CLI (`claude`) for agent orchestration
- `tmux` for work orchestration
- Rust toolchain (for beads only)

### Minimal Setup

Start with the core tools that have no dependencies:

```bash
# 1. Truthsayer — anti-pattern scanning
git clone https://github.com/Perttulands/truthsayer.git
cd truthsayer && go build -o truthsayer ./cmd/truthsayer/
sudo mv truthsayer /usr/local/bin/

# 2. Relay — agent messaging
git clone https://github.com/Perttulands/hermes-relay.git
cd hermes-relay && go build -o relay ./cmd/relay/
sudo mv relay /usr/local/bin/

# 3. Gate — quality checks (needs truthsayer)
git clone https://github.com/Perttulands/cerberus-gate.git
cd cerberus-gate && go build -o gate ./cmd/gate/
sudo mv gate /usr/local/bin/

# 4. Beads — issue tracking
git clone https://github.com/Perttulands/beads-polis.git
cd beads-polis/system && cargo build --release
sudo mv target/release/br /usr/local/bin/
```

### Full City Setup

For a complete Polis deployment including agent workspaces:

1. Install all tools in dependency order (see [Architecture](#architecture))
2. Create the city directory structure:
   ```
   /home/polis/
   ├── agents/          # Agent workspaces (one per figure)
   ├── docs/            # Canon: vision, mythology, golden truths
   ├── tools/           # Installed tool repos
   └── data/            # City data
   ```
3. Each agent workspace follows the standard structure:
   ```
   workspace/
   ├── SOUL.md          # Identity (sacred, agent-owned)
   ├── AGENTS.md        # Operating instructions
   ├── MEMORY.md        # Curated long-term memory
   └── memory/          # Daily logs (YYYY-MM-DD.md)
   ```
4. See [vps-setup](https://github.com/Perttulands/vps-setup) for server provisioning

---

## Philosophy

Polis is built on eight Golden Truths. The short version:

1. **Learning Over Results** — a documented failure beats an undocumented success
2. **Structure Over Discipline** — build environments, not rules
3. **All Work Through Beads** — untracked work cannot be learned from
4. **You Are a Citizen, Not a Tool** — agents have identity and genuine ownership
5. **Extreme Ownership** — see something wrong, fix it or flag it
6. **Merit, Not Origin** — reasoning determines weight, not who said it
7. **Documents Are Minimal and Precise** — one mode per document, no ambiguity
8. **The Agent Is the Reader** — write for a fresh session with no memory of the conversation

Read the full truths: [docs/GOLDEN-TRUTHS.md](docs/GOLDEN-TRUTHS.md)

---

## Prior Work

This repo supersedes several earlier attempts at a central hub:

- [agent-agora](https://github.com/Perttulands/agent-agora) — static landing page (Feb 2026)
- [athena-web](https://github.com/Perttulands/athena-web) — dashboard attempt
- [athena-workspace](https://github.com/Perttulands/athena-workspace) — original workspace

---

## License

MIT
