# Adopting this setup in another project

`claude-init` is the reference Claude Code setup. This file is the full list of what to copy,
what to adapt, and what to leave out when applying it to a project, including projects that
already have their own notes (a long `CLAUDE.md`, plan files, an auto-memory directory).

The procedure is automated by the `/adopt-setup` skill (`adopt-setup/SKILL.md`, symlinked into
`~/.claude/skills/` so it runs from any project). The skill follows this file; when they
disagree, this file wins and the skill gets fixed.

## Principles

1. **Lose nothing.** Every fact in the project's existing Claude notes ends up somewhere in the
   new layout, or is deleted on purpose with the user's agreement. A coverage table proves it.
2. **Plan and get approval, back up, then write.** Nothing in the target changes before the
   user has approved the plan and a verified backup exists.
3. **Project rules beat template defaults.** Where the project's notes contradict a template
   agent (for example "art comes from the AI pipeline, not code" vs art-agent's "SVG or
   code-generated where possible"), the project rule wins and is written into that agent.
4. **Keep template text recognisable.** Agent bodies and rules stay as in the template, except
   for removing references to dropped agents (recorded in the manifest). Project specifics are
   appended under a `## This project` heading. That keeps a later re-sync mechanical.
5. **Always-loaded text stays short.** `CLAUDE.md` and unscoped `.claude/rules/*.md` load into
   every conversation. Long reference material goes to `docs/` with a one-line pointer.
6. **Bootstrap exception.** Normally only business-agent writes `docs/domain.md` and each agent
   writes its own doc. During adoption the skill fills them all once; after that, ownership
   applies.

## What is in the template

| Path | Kind | On adoption |
|---|---|---|
| `CLAUDE.md` | Project instructions | **Adapt** — see below |
| `.claude/settings.json` | Permissions | **Merge** with the target's |
| `.claude/rules/general.md` | Always-on rules | Copy verbatim |
| `.claude/rules/git.md` | Git guardrails | Copy verbatim (non-git target: see Git) |
| `.claude/rules/workflow.md` | Review gate | Copy; drop the `security-agent` line only if that agent is dropped |
| `.claude/rules/domain.md` | business-agent briefing flow | Copy verbatim |
| `.claude/agents/*.md` | 11 agents | **Select, then adapt** |
| `.claude/hooks/writes-only-in.sh` | Write guard for review-agent | Copy verbatim, keep it executable; needs `jq` and `realpath` |
| `.claude/skills/commit/` | `/commit` skill | Copy verbatim |
| `.claude/agent-memory/.gitkeep` | Agent memory root | Copy the `.gitkeep` only — never memory contents |
| `docs/{domain,ops,security,network}.md` | Owned docs (stubs) | **Fill** from existing notes; delete stubs of dropped agents |
| `.gitignore` | Ignore defaults | **Merge**, then check nothing real is ignored (see Git) |
| `.dockerignore` | Ignore defaults | Only if the target has a `Dockerfile`; merge |

The adoption also **creates** `.claude/adopted-from.md` in the target (see Manifest).

**Never copy:** `ADOPTING.md`, `adopt-setup/`, `.git/`, anything inside
`.claude/agent-memory/<agent>/`, `.claude/settings.local.json`, `CLAUDE.local.md`. These belong
to `claude-init` itself or to a machine.

## Where existing content goes

Read every source fully before routing: `CLAUDE.md`, `CLAUDE.local.md`, any `.claude/` content,
`AGENTS.md`, `.cursorrules`/`.cursor/rules`, `.github/copilot-instructions.md`, plan/notes
files referenced from them (e.g. `MULTIPLAYER.md`), and the auto-memory at
`~/.claude/projects/<absolute path, every non-alphanumeric replaced by '-'>/memory/`
(e.g. `-home-virgilio-claude-risk`).

| Existing content | Destination |
|---|---|
| One-line description of the project | `CLAUDE.md` title + first paragraph |
| How to run, build, test, lint, take screenshots | `CLAUDE.md` `## Commands` (the commands only) |
| Deploy, containers, environments, ports, backups of the running thing | `docs/ops.md` |
| Purpose, users, vocabulary, game/business rules, invariants | `docs/domain.md` |
| Requirements and decisions with dates | `docs/domain.md` (rules) — keep the date |
| Product-level open questions, things waiting on the user | `docs/domain.md` `## Open questions` |
| Known defects, TODOs, dead files for one area | The owning agent's doc (`docs/art.md`, `docs/architecture.md`, …); only product-level state goes to `domain.md` |
| Code map (which file does what) | `CLAUDE.md` `## Code map`, ≤ ~15 lines; more goes to `docs/architecture.md` |
| Subsystem design/implementation notes (geometry, draw order, protocols) | `docs/architecture.md`, one section per subsystem; pointer from the owning agent's doc |
| Debug/dev hooks (console APIs, test flags) | test-agent's `## This project`, and `## Commands` if used routinely |
| The user's standing rules for this project ("no paid services", "back up before big changes") | `.claude/rules/project.md` (always loaded) |
| Rules that only matter for some files | `.claude/rules/<topic>.md` with `paths:` frontmatter |
| Implementation constraints ("rules engine has no DOM code") | The owning agent's `## This project` (usually code-agent) |
| Taste, art direction, rejected designs and why | `docs/art.md` (owned by art-agent) or `docs/ux.md` (ux-agent) |
| Asset/build pipelines | The owning agent's doc (`docs/art.md`, `docs/ops.md`) |
| Large existing plan documents | Leave in place; link from **every** doc whose agent owns part of it |
| Security- or network-specific notes | `docs/security.md` / `docs/network.md` |
| Personal preferences that apply to every project | Suggest `~/.claude/CLAUDE.md`; move only if the user agrees |
| Auto-memory entries | Copy durable project facts missing from the docs into the right doc (e.g. a quality bar or rejected example remembered only in memory). See Auto-memory |
| Machine-local paths (tool binaries, mounts, sibling repos) | Single-user/single-machine project: keep them where they are used. Shared repo: move to `CLAUDE.local.md`. Ask |
| Secrets | Never in any committed file |

- **Split mixed bullets.** A bullet that carries a requirement plus two code rules goes to
  three places; the coverage table lists each piece.
- **One home per fact.** A section that fits two places goes to one, with a pointer from the
  other. Duplicated facts drift.
- **Name collisions.** If the old notes call an external tool by an agent's name ("the art
  agent" meaning a Docker image generator), rename the tool everywhere ("PixelArt generator")
  so it can't be confused with the Claude subagent.

### Auto-memory

The memory dir belongs to the user's machine, not the repo. Don't delete or rewrite entries.
After adoption, list entries that now point at the wrong place (e.g. "the pipeline list lives
in CLAUDE.md"). With the user's agreement, back the memory dir up and update just those
pointers; otherwise report them.

## Adapting each piece

### CLAUDE.md

Start from the template's `CLAUDE.md` and change:

- `# Project` → `# <Project name>` plus one paragraph: what it is, the stack, the one or two
  constraints that shape everything.
- Fill `## Commands` with real, tested commands. An empty section is a defect.
- Add `## Code map` if the project has one worth knowing.
- `### Agents` table: only the agents kept, plus any project-specific ones.
- Keep the `## Domain` and `## Claude Code layout` sections.
- **Remove `## Applying this setup elsewhere`** — it describes claude-init itself.
- Target: under ~100 lines. Everything else is a pointer.

### Agents — select

Decide per agent: keep, drop, or add. Evidence comes from the code, not guesses:

| Agent | Keep when |
|---|---|
| business-agent, code-agent, review-agent, test-agent | Always |
| security-agent | Almost always; drop only for code that never touches input, network, secrets or deps |
| db-agent | A database, persistent store, or data analysis exists or is planned |
| interface-agent | Public API, CLI, protocol, events or a library boundary others depend on |
| ux-agent | Anything a user sees or clicks |
| art-agent | Visual assets (sprites, icons, illustrations, branding) |
| ops-agent | Builds, containers, deploys, CI, services — even a single `Dockerfile` |
| network-agent | Anything served beyond localhost, tunnels, DNS, self-hosting |

Planned work counts: a project with a written multiplayer plan keeps network-agent even if no
server exists yet. When unsure, ask.

**Dropping an agent** means removing every reference to it: the `CLAUDE.md` table,
business-agent's briefing table, every other agent's "Not yours" line and hand-offs (e.g.
review-agent's "Hand security-sensitive findings to security-agent"), the owned `docs/` stub,
and `rules/workflow.md` if it is named there. `grep -rn '<agent-name>' CLAUDE.md .claude docs`
must come back empty (the manifest excepted). Record the drop in the manifest.

**Adding an agent** follows the template shape: frontmatter (`name`, `description`, `tools`,
`model: inherit`, `memory: project`), a scope list, a "Not yours" line where its ownership
overlaps another agent's, the domain.md read instruction and the memory line. Add it to the
`CLAUDE.md` table and business-agent's table, and record it in the manifest.

### Agents — adapt

Leave the template body unchanged apart from drop edits. Append:

```markdown
## This project

- Commands this agent runs (exact, tested).
- Paths it owns or must not touch.
- Project rules that override the generic body above, stated as overrides.
- The doc it owns, if any.
```

Typical overrides: art-agent told which generator/pipeline produces art; test-agent told
the real test and screenshot commands; ops-agent told the container and port; code-agent told
module boundaries that must stay pure (e.g. "rules engine has no DOM or Node-only code").

review-agent keeps its `hooks:` frontmatter exactly. Any other agent that should be read-only
except its memory gets the same hook with its own memory dir as the argument. If the target is
not a git repo, add to review-agent's `## This project`: "No git: review against the latest
backup in `<backup dir>` instead of `git diff`."

### docs/

Fill the owned docs from the routing table. Keep the template headings; add headings rather
than renaming them. Each doc keeps its `Owned by <agent>` line. Add `docs/art.md`,
`docs/ux.md`, `docs/architecture.md` only when there is real content for them.

### Rules

- Copy the four template rules.
- Project-wide user rules → `.claude/rules/project.md`, written as short imperatives with the
  reason when the reason isn't obvious.
- File-scoped rules → their own file with frontmatter:

  ```markdown
  ---
  paths:
    - "art/**"
    - "assets/**"
  ---
  ```

### settings.json

Merge, don't replace:

- Keep every existing `allow`/`ask`/`deny` entry and other keys.
- Add the template's `deny` entries for `.env` and `secrets/`.
- Add `allow` entries for the project's routine, read-only or self-contained commands found in
  `## Commands` (e.g. `Bash(node tools/sim.js:*)`). Nothing destructive, nothing that pushes.
- Validate with `jq . .claude/settings.json`.

### Git

- Target is a git repo: if the working tree is dirty, stop and ask — don't branch over
  uncommitted work. Otherwise work on a branch (`chore/claude-setup`); never commit unless the
  user asks — then via `/commit`.
- Target is **not** a git repo: `rules/git.md` and `/commit` assume git. Ask the user whether
  to `git init`. If they decline, still copy them (they apply once git exists) and say so.
- Merge `.gitignore` before any first commit so secrets and `settings.local.json` are covered.
  The template ignores `bin/ build/ out/ target/ tmp/ logs/ *.so *.db` among others, which can
  be real source in some projects. Check: `git ls-files -ci --exclude-standard` (existing repo)
  or `git status --ignored --short` after `git init`; remove any entry that hides files the
  project needs.

### Manifest

Write `.claude/adopted-from.md` in the target:

```markdown
# Adopted from claude-init

- Template: /home/virgilio/claude/claude-init @ <commit hash> (<date>)
- Agents dropped: db-agent (no database; config is localStorage)
- Agents added: —
- Extra docs: docs/art.md, docs/architecture.md
- Backup: /home/virgilio/claude/backups/<file>.tar.gz
```

Its presence is what marks a project as already adopted.

## Verification checklist

All must pass before the adoption is called done:

- [ ] Backup exists and `tar -tzf` lists it.
- [ ] Coverage table: every section and bullet of each old source → destination `file:line`, or
      "dropped, user agreed". Mixed bullets show each piece.
- [ ] `grep -rn` for each dropped agent name returns nothing outside the manifest.
- [ ] Every agent named in any file exists in `.claude/agents/`.
- [ ] `jq . .claude/settings.json` succeeds.
- [ ] Hook works both ways (`command -v jq realpath` first):
      `echo '{"tool_input":{"file_path":"/etc/x"}}' | CLAUDE_PROJECT_DIR=$PWD .claude/hooks/writes-only-in.sh .claude/agent-memory/review-agent` exits **2**, and
      `echo '{"tool_input":{"file_path":".claude/agent-memory/review-agent/x.md"}}' | CLAUDE_PROJECT_DIR=$PWD .claude/hooks/writes-only-in.sh .claude/agent-memory/review-agent` exits **0**.
- [ ] `.gitignore` hides no project file (see Git).
- [ ] Every command in `## Commands` was run once (or marked "not run: <reason>").
- [ ] No empty template sections or `<!-- -->` placeholders left; no `## Applying this setup elsewhere`.
- [ ] `CLAUDE.md` under ~100 lines.
- [ ] `.claude/adopted-from.md` written.
- [ ] Stale auto-memory pointers listed (or updated with the user's agreement).
- [ ] An independent reviewer checked the result; findings fixed or accepted by the user.
- [ ] Claude Code restarted in the target so new agents and hooks load.

## Re-syncing later

When `claude-init` changes, re-run `/adopt-setup` in the target. A project is a re-sync when
`.claude/adopted-from.md` exists — not merely because `.claude/` exists.

- Files copied verbatim (rules, hook, `/commit`): take the template's version.
- Agent bodies: take the template's version, re-apply the drop edits listed in the manifest,
  keep each `## This project` section as it is.
- `CLAUDE.md` and `docs/`: never replaced wholesale. Carry over only changed template sections
  (e.g. `## Claude Code layout`), by hand.
- Show the diff to the user before writing, back up as for a first adoption, and update the
  manifest's commit hash.
