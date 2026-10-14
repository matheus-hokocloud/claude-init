# Project

<!-- One paragraph: what this project is, who it's for, and its current state. -->

## Commands

<!-- Build, test, lint and run commands, e.g.:
- Build: `...`
- Test:  `...`
-->

## Domain

What this software is for and its rules live in `docs/domain.md`. `business-agent`
maintains it and briefs the other agents (see `.claude/rules/domain.md`). Read it before
building anything non-trivial.

## Claude Code layout

```
.claude/
├── settings.json   shared settings (permissions, hooks, env) — committed
├── rules/          *.md, auto-loaded as instructions; scope with `paths:` frontmatter
├── agents/         *.md subagents (frontmatter: name, description, tools, model, memory, hooks)
├── agent-memory/   per-agent persistent memory, written by agents with `memory: project`
├── hooks/          scripts run by hooks (e.g. review-agent's write guard)
└── skills/         <name>/SKILL.md, invoked on demand or as /<name>
```

### Agents

| Agent | Owns |
|---|---|
| `business-agent` | The domain: purpose, users, rules, requirements — maintains `docs/domain.md` |
| `code-agent` | Features, bug fixes, refactors |
| `review-agent` | Independent read-only review of every non-trivial change |
| `test-agent` | Unit, integration and end-to-end tests |
| `db-agent` | Schema, migrations, queries, performance, data ops, analysis |
| `interface-agent` | Public contracts: APIs, CLI flags, events, module boundaries |
| `ux-agent` | UI design, components, flows, accessibility |
| `art-agent` | Asset creation and pipeline, visual identity, art-direction review |
| `ops-agent` | CI/CD, releases, containers, config and secrets, observability — `docs/ops.md` |
| `security-agent` | Threat model, security review, secret/dependency scans, hardening — `docs/security.md` |
| `network-agent` | DNS, TLS, reverse proxies, tunnels, firewalls, topology — maintains `docs/network.md` |

Each agent has `memory: project`, so its notes persist in `.claude/agent-memory/<agent>/`.

- Keep this file short and project-specific; put standing rules in `.claude/rules/`.
- New skill: `.claude/skills/<name>/SKILL.md` with `name` and `description` frontmatter.
- Commits: `/commit` skill (`.claude/skills/commit/`); git guardrails in `.claude/rules/git.md`.
- Personal overrides go in `.claude/settings.local.json` and `CLAUDE.local.md` (gitignored).
