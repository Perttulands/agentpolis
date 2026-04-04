# PRD: Polis Agentics Registry

**Author:** Hestia  
**Date:** 2026-03-16  
**Status:** Draft v3  
**Inspired by:** [Indie Dev Dan — The Library Meta-Skill](https://www.youtube.com/watch?v=_vpNQ6IwP9w)  
**Review history:** v1 overscoped (15 files, 8 commands) → v2 reduced to 2 files → v2 critiqued as passive/will-freeze → v3 pivots to executable, generated, inside existing authority

---

## 1. Problem

Polis has agent instructions scattered across multiple runtimes (OpenClaw, Claude Code, Codex, Pi) and ~21 repos. When a tool changes, nobody knows which locations need updating. Previous attempts at solving this:

- `memory/skill-inventory.md` — froze at February 23, never updated
- Manual skill audit (March 8) — successful but heroic, not repeatable

The system's actual failures are code-level integration drift (dead call sites, stale commands), not skill-file drift per se. But the two are connected: when `work` gets `resume`, the skill that teaches agents about `work` should know about `resume`. Today nothing enforces that.

## 2. What Changed from v2

Two independent critics (Codex gpt-5.4 + Claude Opus) both rejected the standalone catalog approach:

- **A manually-maintained YAML will freeze** — same failure mode as `skill-inventory.md`. Nothing generates it, nothing forces consumption.
- **The system needs executable checks, not passive maps.** The real failures are code-level (dead call sites, fail-open gates). Contract tests catch those; a catalog doesn't.
- **Discovery logic already exists** — `pi-vs-claude-code/extensions/cross-agent.ts` has `scanCommands()`, `scanSkills()`, `scanAgents()` that crawl `.claude`, `.gemini`, `.codex` trees.
- **Contract validation already exists** — `work/internal/contracts/contracts.go` validates CLI boundaries. `gate health` validates tool readiness.
- **Build inside an existing authority** — `gate` is already the verification authority. A `gate catalog-check` subcommand is ~100 lines of Go on proven infrastructure.

## 3. The Design

### 3.1 A minimal registry config (not a catalog)

A small YAML file consumed by `gate`. Not a standalone artifact — an input to an executable check.

```yaml
# /home/polis/agentpolis/library/registry.yaml
# Input for `gate catalog-check`. Lists cross-runtime skill surfaces
# that must stay in sync. Single-runtime skills don't need entries —
# their source IS their only location.

entries:
  - name: work
    bins: [work]
    source: /home/polis/.openclaw/skills/work/SKILL.md
    targets:
      - /home/polis/tools/work/AGENTS.md
    verify_commands:
      - "work --help"
      - "work run --help"
      - "work send --help"
      - "work resume --help"

  - name: gate
    bins: [gate, truthsayer, ubs]
    source: /home/polis/.openclaw/skills/gate/SKILL.md
    targets:
      - /home/polis/tools/gate/AGENTS.md
    verify_commands:
      - "gate --help"
      - "gate check --help"
      - "gate health --help"

  - name: relay
    bins: [relay]
    source: /home/polis/.openclaw/skills/relay/SKILL.md
    targets:
      - /home/polis/tools/relay/AGENTS.md
    verify_commands:
      - "relay --help"
      - "relay send --help"
      - "relay status --help"

  # Only entries with cross-runtime targets or critical binaries.
  # ~15-20 entries, not 50.
```

Key constraints:
- **Only cross-runtime entries.** Single-location skills don't need tracking.
- **~15-20 entries**, not 50. Only things that actually drift.
- **`verify_commands`** — the executable part. `gate` runs these and checks they succeed.
- **No `owner`, no `last_verified`, no `description`.** Beads track work. Git tracks history. Don't duplicate.

### 3.2 `gate catalog-check` subcommand

A new subcommand in `gate` (~100-150 lines of Go) that:

1. Reads `registry.yaml`
2. For each entry:
   - Checks binaries exist (`which`)
   - Runs `verify_commands` and checks exit code 0
   - Checks source file exists
   - Checks target files exist
   - Optionally: extracts subcommand names from `--help` output and compares against documented commands in the SKILL.md
3. Reports: PASS / STALE (target missing or out of date) / BROKEN (binary missing, command fails)
4. On failure: creates a bead via existing `gate` → `br` integration
5. Machine-readable output: `--json` flag (consistent with existing `gate` patterns)

This runs as part of `gate check` or standalone. It's executable, automated, and feeds into the existing verification pipeline.

### 3.3 Generated discovery (phase 2)

Extract the scanning logic from `pi-vs-claude-code/extensions/cross-agent.ts` into a standalone script or `gate` subcommand that:

- Crawls `/home/polis/.openclaw/skills/`, `~/.claude/`, `~/.codex/`, per-repo `.claude/`/`.codex/` dirs
- Emits JSON inventory of everything found
- Compares against `registry.yaml` to find untracked cross-runtime surfaces

This makes the registry partially self-maintaining: new skills that appear in multiple runtimes get flagged as "unregistered."

### 3.4 A short SKILL.md

Still useful — teaches OpenClaw agents how to use the registry and `gate catalog-check`. But it's a consumer of the executable system, not the system itself. ~50 lines.

## 4. Integration

| Tool | How |
|---|---|
| **Gate** | `gate catalog-check` is a new subcommand. Findings create beads via existing gate → br wiring. Runs during `gate check --level standard` or standalone. |
| **Beads** | Stale/broken findings auto-create beads. No staleness tracking in YAML. |
| **Work** | `work/internal/contracts/contracts_test.go` already tests cross-tool CLI boundaries. `gate catalog-check` complements this — contracts test code integration, catalog-check tests documentation currency. |
| **Existing discovery** | Phase 2 extracts `cross-agent.ts` scanning into a reusable tool that validates/generates registry entries. |

## 5. What This Does NOT Do

- **No install/push/sync/add commands.** Symlinks work. Git works. `skill-creator` works.
- **No cross-runtime template generation.** Premature. Each runtime has its own patterns.
- **No standalone project.** Lives as: a YAML config in agentpolis, a subcommand in gate, a thin skill in openclaw.
- **No manual maintenance dependency.** The check is executable and can run in CI, heartbeat, or ad-hoc. If nobody runs it, the checks don't happen — but the registry doesn't silently rot either, because it's not pretending to be current.

## 6. Phasing

### Phase 0: Prerequisites (do first, ~15 hours)
- Fix 4 dead call sites (`work→senate`, `orbit→gate`, oathkeeper service, relay service)
- Deploy oathkeeper
- Enable `WORK_STRICT=1` default
- Add CI to 7 production tools
- Fix gitleaks fail-open

These directly reduce the system's top risks. The registry doesn't help until the system it checks is honest.

### Phase 1: Build `gate catalog-check` (~4 hours)
- Write `registry.yaml` with ~15-20 cross-runtime entries
- Implement `gate catalog-check` subcommand
- Add `--json` output
- Wire bead creation on failure
- Add to `gate check --level standard`

### Phase 2: Generated discovery (~6 hours)
- Extract `cross-agent.ts` scanning into a standalone script
- Add `gate catalog-discover` that compares live filesystem against registry
- Flag untracked cross-runtime surfaces

### Phase 3: Evaluate (~4 weeks of use)
- Has `gate catalog-check` caught real drift?
- Has the registry stayed current without manual intervention?
- Does cross-runtime install demand warrant template generation?
- If yes to all: consider expanding. If not: the registry config + gate subcommand is sufficient.

## 7. Success Criteria

- `gate catalog-check` catches a real stale skill within the first 2 weeks of operation
- Zero manual updates needed to `registry.yaml` in the first month (entries only change when tools add/remove commands)
- The check runs in CI once CI exists (Phase 0)
- No skill goes broken for more than one `gate check` cycle without a bead being created

## 8. Estimated Effort

| Phase | Hours | Depends on |
|---|---|---|
| Phase 0 (prerequisites) | ~15 | nothing |
| Phase 1 (`gate catalog-check`) | ~4 | Phase 0 |
| Phase 2 (generated discovery) | ~6 | Phase 1 |
| Phase 3 (evaluation) | 0 (just use it) | Phase 2 |

Total new work: ~10 hours after prerequisites. The prerequisites are higher-value work that should happen regardless.

## 9. Open Questions

1. **Where does `registry.yaml` live?** Options: `/home/polis/agentpolis/library/`, `/home/polis/tools/gate/`, or a shared config location. Gate needs to find it — either hardcoded path or `--registry` flag.
2. **How deep should command verification go?** Checking `--help` exits 0 is cheap. Parsing `--help` output to verify documented subcommands exist is more valuable but more brittle. Start with exit-code checks, add output parsing if the simpler check isn't catching real drift.
3. **Should `gate catalog-check` run by default in `gate check`?** Or only when explicitly requested? Starting as opt-in (`gate catalog-check`) and promoting to default after Phase 3 evaluation seems safest.
