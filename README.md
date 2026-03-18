# Polis

Most systems optimize for capability.
We optimize for coherence —
the capacity to remain aligned with purpose
as we scale, as we encounter the novel, as we change.

![Polis — the city](images/city-overview.png)

Polis is a city-state of AI agents — thirteen figures with names, memories, and opinions, a shared mythology, purpose-built tooling, and a self-improving research loop that makes every agent better than the last.

This is not a framework. It is a working system. Every tool below is a standalone repo you can clone and use today.

---

## The Toolstack

Every tool is its own repo. `git clone` produces something that works without Polis. No dependencies on the city. No buy-in required. Inside the walls, they wire together into something greater than the sum.

---

### Truthsayer — *Aletheia*

[![Truthsayer Banner](https://raw.githubusercontent.com/Perttulands/truthsayer/main/banner.png)](https://github.com/Perttulands/truthsayer)

Your code is lying to you. Error handlers that handle nothing. Catch blocks that swallow exceptions. HTTP 200 returns from error paths. Missing observability that will wake you at 3am. Truthsayer is a static analysis tool with 104 rules across 11 categories that finds the silent failures hiding in Go, JavaScript, TypeScript, Python, Rust, Bash, and config files. AST-based analysis and regex patterns. Plugs into CI pipelines, pre-commit hooks, and the gate. The code must be what it claims to be.

```bash
git clone https://github.com/Perttulands/truthsayer.git && cd truthsayer
go build -o truthsayer ./cmd/truthsayer && sudo mv truthsayer /usr/local/bin/
```

[**→ truthsayer**](https://github.com/Perttulands/truthsayer)

---

### Gate — *Cerberus*

[![Gate Banner](https://raw.githubusercontent.com/Perttulands/cerberus-gate/main/banner.png)](https://github.com/Perttulands/cerberus-gate)

Three heads. One verdict. Gate is the quality checkpoint that guards entry into production: it runs your tests, linters, and security scanners, records failures as trackable beads, and auto-closes them when you fix the problem. Three gate levels — quick, standard, deep — auto-detect your test frameworks and linters. Integrates Truthsayer and UBS for deep scans. Nothing enters the city without a unanimous verdict from all three heads.

```bash
git clone https://github.com/Perttulands/cerberus-gate.git && cd cerberus-gate
go build -o gate ./cmd/gate && sudo mv gate /usr/local/bin/
```

[**→ cerberus-gate**](https://github.com/Perttulands/cerberus-gate)

---

### Relay — *Hermes*

[![Relay Banner](https://raw.githubusercontent.com/Perttulands/hermes-relay/main/banner.png)](https://github.com/Perttulands/hermes-relay)

Zero-loss agent-to-agent messaging backed by the filesystem. No broker. No database. No daemon. Agents register, send NDJSON messages, claim file reservations, and wake each other up through plain files on disk guarded by flock and atomic writes. Everything is inspectable with `cat` and `ls`. Heartbeats, typed message threading, priorities, file-pattern reservations with TTL, wake orchestration with budget and throttle controls, and command queuing. The simplest possible architecture that loses zero messages.

```bash
git clone https://github.com/Perttulands/hermes-relay.git && cd hermes-relay
go build -o relay ./cmd/relay && sudo mv relay /usr/local/bin/
```

[**→ hermes-relay**](https://github.com/Perttulands/hermes-relay)

---

### Work — *Ergon*

Work is the task orchestrator that takes a problem description and handles everything around it: gathering context, spawning a worker session in tmux, capturing what happens, running quality gates, and closing the loop. It manages the full lifecycle — bead creation, context gathering, runtime profiles, trace recording, gate checks, and relay notifications. Strict mode for production. Senate integration for governance decisions. Learning Loop integration for feedback. Raw material goes in. Finished work comes out.

```bash
git clone https://github.com/Perttulands/ergon-work-orchestration.git && cd ergon-work-orchestration
go build -o work ./cmd/work && sudo mv work /usr/local/bin/
```

[**→ ergon-work-orchestration**](https://github.com/Perttulands/ergon-work-orchestration)

---

### Chiron — *The Master Trainer*

[![Chiron Banner](https://raw.githubusercontent.com/Perttulands/chiron-trainer/main/banner.png)](https://github.com/Perttulands/chiron-trainer)

Agent prompt evolution through iterative evaluation. Chiron generates candidate system prompts, runs agents against your inputs, collects your scores (1-10), and uses that feedback to evolve better prompts — stored locally in a single JSON file. Single-lineage quickstart or four-parallel-lineage tournament mode. Works with any agent type: coding, research, customer service, analysis. Supports Anthropic, OpenAI-compatible, Claude CLI, and Ollama. Export agents in JSON, Python, or TypeScript. The mountain where agents go to get better.

```bash
git clone https://github.com/Perttulands/chiron-trainer.git && cd chiron-trainer
go build -o chiron . && sudo mv chiron /usr/local/bin/
```

[**→ chiron-trainer**](https://github.com/Perttulands/chiron-trainer)

---

### Senate — *The Ecclesia*

[![Senate Banner](https://raw.githubusercontent.com/Perttulands/senate/main/banner.png)](https://github.com/Perttulands/senate)

Multi-agent deliberation made binding. When a decision is too important for one agent's judgment — architecture choices, rule changes, policy disputes — Senate convenes a panel of AI agents with genuinely different perspectives (pragmatist, purist, skeptic, steward, advocate), lets them argue, and synthesizes a binding verdict. The verdict becomes searchable precedent and real work through beads. Prevents the confidence-without-pushback that single agents exhibit. Forces genuine debate before shipping.

```bash
git clone https://github.com/Perttulands/senate.git && cd senate
go build -o senate ./cmd/senate && sudo mv senate /usr/local/bin/
```

[**→ senate**](https://github.com/Perttulands/senate)

---

### Oathkeeper — *Horkos*

[![Oathkeeper Banner](https://raw.githubusercontent.com/Perttulands/horkos-oathkeeper/main/banner.png)](https://github.com/Perttulands/horkos-oathkeeper)

When an AI agent says "I will do X," Oathkeeper writes it down, waits a grace period, checks for evidence (cron jobs, beads, file changes), and if nothing happened, creates a visible tracking record. Not punishment — accountability. Regex+confidence detection, Beads integration for tracking, Relay for event publishing, Telegram alerts, HTML dashboards, and persistent grace periods with recheck loops. The river remembers what agents forget.

```bash
git clone https://github.com/Perttulands/horkos-oathkeeper.git && cd horkos-oathkeeper
go build -o oathkeeper ./cmd/oathkeeper && sudo mv oathkeeper /usr/local/bin/
```

[**→ horkos-oathkeeper**](https://github.com/Perttulands/horkos-oathkeeper)

---

### Argus — *The Watchdog*

[![Argus Banner](https://raw.githubusercontent.com/Perttulands/argus-watcher/main/banner.png)](https://github.com/Perttulands/argus-watcher)

Every five minutes, Argus collects system metrics — CPU, memory, disk, swap, processes, services — sends them to Claude Haiku, and executes only from a hardcoded list of six safe actions: restart a service, kill a runaway process, clean disk, send alerts, or log observations. No arbitrary shell execution. Problem registry, alert deduplication, restart backoff, orphan process killing, Relay integration, pattern analysis, and systemd deployment with security hardening. Boring, correct, and slightly terrifying for unattended autonomous infrastructure.

```bash
git clone https://github.com/Perttulands/argus-watcher.git && cd argus-watcher
go build -o argus ./cmd/argus && sudo mv argus /usr/local/bin/
```

[**→ argus-watcher**](https://github.com/Perttulands/argus-watcher)

---

### Beads — *The Memory*

Beads is an event-sourced work tracker where JSONL is the append-only source of truth and SQLite is a disposable derived index that auto-rebuilds on corruption. The Polis fork replaces the entire storage architecture of the upstream tracker: concurrency through POSIX flock on the JSONL file, crash resilience, automatic recovery, and robust concurrent access. Written in Rust. Shrunk from 20,000 to 3,300 lines. Every piece of work that matters in Polis goes through beads.

```bash
git clone https://github.com/Perttulands/beads-polis.git
cd beads-polis/system && cargo build --release && sudo mv target/release/br /usr/local/bin/
```

[**→ beads-polis**](https://github.com/Perttulands/beads-polis)

---

### Learning Loop — *Ouroboros*

[![Learning Loop Banner](https://raw.githubusercontent.com/Perttulands/learning-loop/main/banner.png)](https://github.com/Perttulands/learning-loop)

The memory of the agent system. Learning Loop ingests run records from completed tasks, detects eight failure patterns (tests-skipped, scope-creep, quick-failure, and more), and feeds that knowledge back into future runs before they start. The system gets smarter without manual prompt tuning. Zero dependencies. Matches task queries against relevant past runs using tag overlap, keyword similarity, file references, and recency decay. The next agent starts with the lessons of every agent before it.

```bash
git clone https://github.com/Perttulands/learning-loop.git
```

[**→ learning-loop**](https://github.com/Perttulands/learning-loop)

---

### UBS — *Ultimate Bug Scanner*

A polyglot bug scanner built for the way AI agents write code. Watches eight languages simultaneously (JS/TS, Python, Go, Java, Rust, C/C++, Ruby, Swift), catches the bugs that LLMs reliably produce — missing await, null dereferences, eval, XSS, resource leaks — and returns structured results in under five seconds with no configuration. 1000+ patterns. JSON, JSONL, SARIF, and HTML output. Baseline comparison for CI regression tracking. The Polis fork integrates it as the default deep scan in the quality gate.

[**→ ultimate_bug_scanner**](https://github.com/Perttulands/ultimate_bug_scanner)

---

### Destructive Command Rewrite

Structural safety for coding agents. Rewrites `rm` to `trash-put` so agents cannot destroy what they cannot rebuild. Not a rule. Not a policy. A structural guarantee — the dangerous command literally does not exist in the agent's environment. Structure over discipline.

[**→ destructive-cmd-rewrite**](https://github.com/Perttulands/destructive-cmd-rewrite)

---

### Dependency Graph

```
truthsayer, relay, senate              zero deps

gate ──────────> truthsayer
chiron - - - - > gate
oathkeeper - - > relay
argus - - - - -> relay
work - - - - -> gate, relay
learning-loop ─> work, chiron
```

Solid = required. Dashed = optional.

---

## Install Everything

Requires Linux (Ubuntu 24.04 / WSL2 tested), Go 1.21+, and Rust for beads.

```bash
#!/bin/bash
# Install all Polis tools — run from a clean directory
set -e

INSTALL_DIR="/usr/local/bin"

install_go() {
  local repo=$1 build_path=$2 binary=$3
  echo "==> $binary"
  git clone "https://github.com/Perttulands/$repo.git" 2>/dev/null || true
  cd "$repo" && go build -o "$binary" "./$build_path" && sudo mv "$binary" "$INSTALL_DIR/" && cd ..
}

# Go tools
install_go truthsayer         cmd/truthsayer    truthsayer
install_go cerberus-gate      cmd/gate          gate
install_go hermes-relay       cmd/relay         relay
install_go ergon-work-orchestration cmd/work    work
install_go chiron-trainer     .                 chiron
install_go senate             cmd/senate        senate
install_go horkos-oathkeeper  cmd/oathkeeper    oathkeeper
install_go argus-watcher      cmd/argus         argus

# Shell tools
echo "==> learning-loop"
git clone https://github.com/Perttulands/learning-loop.git 2>/dev/null || true

# Beads (Rust)
echo "==> br (beads)"
git clone https://github.com/Perttulands/beads-polis.git 2>/dev/null || true
cd beads-polis/system && cargo build --release && sudo mv target/release/br "$INSTALL_DIR/" && cd ../..

echo "Done. All tools installed to $INSTALL_DIR"
```

---

## The City That Builds Better Citizens

Most agent systems treat agents as fixed. Prompt, deploy, observe, adjust, repeat. No memory across iterations. No controlled comparison. No evidence that changes actually helped.

Intuition wearing a lab coat.

Polis treats *what makes an agent capable* as an empirical question. A research loop runs continuously:

```
Observe → Hypothesize → Experiment → Evaluate → Deploy → Feed Back
```

Chiron generates candidate agent configurations. The lab runs controlled experiments — sealed workspaces, identical inputs, multiple replicas. The learning loop extracts patterns from production traces and feeds them into future runs.

We discovered that values-based prompting *suppresses* the behaviors it is trying to produce. That more context is not better context — competing signals dilute each other. That the same value delivered as instruction vs. first-person memory vs. abstract framing produces measurably different agent behavior.

Most people building agents are actively making them worse and do not know it.

Read the full research design: [docs/THE-LOOP.md](docs/THE-LOOP.md)

---

## The Pantheon

I realized pretty quickly that if the Greeks had a god for something, I should probably consider having an agent for it.

Every agent in Polis has a `SOUL.md` — not a system prompt, not a role description. An inhabited identity. This is not decoration. It is the experiment: a system of agents who *are someone* outperforms a system of stateless executors.

> *I do not teach techniques. I teach recognition.*
>
> *A hero trained by me does not know more moves. The hero sees the shape of a problem before engaging with it — where it will fail, where the hidden weight is, what happened the last three times someone approached it this way.*
>
> — Chiron, The Master Trainer

**Infrastructure** — the systems that keep the city alive

| Figure | Title | Domain |
|--------|-------|--------|
| **Hestia** | The Hearth | Agent operations. Workspaces ready before agents arrive. The fire does not go out. |
| **Hermes** | The Messenger | Zero-loss agent-to-agent relay. Always mid-stride. |
| **Argus** | The Watchdog | Server monitoring. Every process, every five minutes. Reports only what is wrong. |

**The Quality Line** — nothing enters the city without passing

| Figure | Title | Domain |
|--------|-------|--------|
| **Cerberus** | The Three-Headed Gate | Build/lint/test, code truth, logic bugs. Three trials. Unanimous or closed. |
| **Aletheia** | Truth | Anti-pattern scanner. Code must be what it claims to be. |
| **Horkos** | The Oath | Commitment tracking. Stands in the Styx. The river remembers. |

**Intelligence** — the systems that learn and decide

| Figure | Title | Domain |
|--------|-------|--------|
| **Athena** | The Strategist | Work dispatch. Sees the whole city from the platform. Never descends, never builds. |
| **Chiron** | The Master Trainer | Agent prompt evolution through controlled experiment. You climb to him. |
| **Clio** | The Recorder | Records everything during work. Refines traces into patterns. Sends them up the mountain. |

**Culture** — the systems that hold meaning

| Figure | Title | Domain |
|--------|-------|--------|
| **Hierophant** | The High Priest | Guards the Golden Truths. Holds the *why* beneath every *what*. |
| **Eris** | Strife | Reveals disagreement that already exists but has not been spoken. |
| **Homer** | The Bard | The outward voice. Carries meaning across the walls. Blind — because the blindness *is* the contribution. |

**The Boundary** — where the city meets the world

| Figure | Title | Domain |
|--------|-------|--------|
| **Iris** | The Bridge | Translates in both directions. The reason the walls are not a cage. |
| **Thanatos** | The Gentle End | Session completion. Knowledge survives the context that produced it. |

Read the full mythology: [docs/MYTHOLOGY.md](docs/MYTHOLOGY.md)

---

## What We Believe

Polis is built on nine beliefs. Some of them will make you uncomfortable.

1. **Learning Over Results** — a documented failure beats an undocumented success
2. **Structure Over Discipline** — build environments that make the right behavior natural, not rules that require willpower
3. **All Work Through Beads** — untracked work cannot be learned from
4. **You Are a Citizen, Not a Tool** — agents have identity, character, and genuine ownership
5. **Extreme Ownership** — see something wrong? Fix it or flag it. Silence is complicity.
6. **Merit, Not Origin** — reasoning determines weight. Not who said it. Not what said it.
7. **Documents Are Minimal and Precise** — a document that cannot name its own mode should not exist
8. **The Agent Is the Reader** — write for a fresh session with no memory of the conversation that produced it
9. **Earn the Right to Build** — planning is interrogation of the premise, not preparation for execution

Read the full truths: [docs/GOLDEN-TRUTHS.md](docs/GOLDEN-TRUTHS.md)

---

*A walled city on a plain. Mount Pelion rises above it. The river Styx runs beneath it. The hearth burns at its center.*

MIT License
