# Inspiration: The Library Meta-Skill

Source: [Indie Dev Dan — "The Library Meta-Skill: How I Distribute PRIVATE Skills, Agents and Prompts"](https://www.youtube.com/watch?v=_vpNQ6IwP9w)

## The Problem He Solved

Skills, agents, and prompts scattered across 10+ codebases. Duplicates everywhere. Out of sync. Hard to coordinate across devices, team members, and agents. The sloppy-fast way: copy things, they go stale immediately.

## His Solution

A **pure agentic application** — no binary, no daemon, no code. Just two files:

1. **`library.yaml`** — a reference catalog that stores only pointers (git repos, local paths), categorized into `skills`, `agents`, `prompts`. Stores no content, only references to where things live.

2. **`SKILL.md`** — a meta-skill that teaches any agent how to operate on the catalog. The skill IS the application.

## The API (6 commands)

| Command | What it does |
|---|---|
| `add` | Catalog a new reference (point to a repo or local path) |
| `use` | Pull a skill/agent/prompt from source into a target location (local, global, or named) |
| `push` | Send local improvements back to the canonical source repo |
| `list` | Show all cataloged references |
| `search` | Find specific items in the catalog |
| `sync` | Pull latest versions from all sources |

## Key Design Decisions

- **References, not copies.** The catalog is like `package.json` — it points to where things live, it doesn't store them.
- **Named targets.** `default`, `global`, `special` — each routing to a different destination path. So you can install locally into a project or globally for all projects.
- **Cookbook pattern.** Each command has its own cookbook file inside the skill directory. The main SKILL.md describes when to use each cookbook. Separates concerns cleanly.
- **Agent-first.** An entire agent can run the full workflow — add, use, push, sync — without human intervention. The whole build→catalog→distribute→use cycle is agent-operable.
- **Runtime-agnostic.** Works across Claude Code, Pi, Codex, or any agent that can read markdown and execute shell commands.
- **No versioning.** Just "latest." Simple. He calls it a package system without versioning.

## The Workflow

```
Build (in native repo) → Catalog (library add) → Distribute (library use) → Use → Update → Push back (library push)
```

The critical insight: **you don't interrupt the flow of building in the value-generating repository.** You build the skill where it naturally lives, then catalog a reference to it. The library is the coordination layer, not the authoring layer.

## His Three-Tier Model

| Tier | What | Role |
|---|---|---|
| **Skills** | Raw capabilities | Teach tools, provide workflows |
| **Agents** | Scale and parallelism | Execute skills, coordinate |
| **Prompts** | Orchestration | One-off commands that orchestrate skills and agents |

He argues people over-stuff skills. Skills should be raw capabilities. Orchestration belongs in prompts. Agent behavior belongs in agent configs. Don't collapse everything into one layer.

## What Makes This Work at Scale

- Single source of truth (the YAML reference file)
- Any team member, device, or agent can sync from it
- Changes flow back to source via `push`
- The library skill itself is distributed the same way (bootstrapping)
- No infrastructure required — just git and an agent that can read markdown

## Stripe Precedent

He references Stripe's "Tool Shed" — a meta-tool that serves the same purpose internally. The pattern is emerging independently across engineering orgs hitting agent scale.
