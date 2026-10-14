---
name: adopt-setup
description: Apply the claude-init reference setup (agents, rules, hooks, docs, settings) to a project and consolidate its existing Claude notes into it. Use when the user asks to adopt, apply or re-sync the claude-init setup.
disable-model-invocation: true
argument-hint: "[target-dir, default: current project]"
---

# Adopt the claude-init setup

Template: `/home/virgilio/claude/claude-init`. Target: `$ARGUMENTS`, or the current project
directory if empty. Stop if the target is the template itself.

**Read `/home/virgilio/claude/claude-init/ADOPTING.md` first and follow it.** It defines what
is copied, what is adapted, where existing content goes, and the verification checklist. This
skill is the order of operations.

## 1. Survey (read-only)

- List the target tree (skip `node_modules`, `.git`, build output). Check whether it is a git
  repo; if it is and the working tree is dirty, stop and ask before going further.
- Read in full every existing instruction source listed in ADOPTING.md "Where existing content
  goes", including the auto-memory dir
  `~/.claude/projects/<absolute path, every non-alphanumeric replaced by '-'>/memory/`.
- Note what the code shows: languages, Dockerfile/compose, database, served ports, assets,
  tests, CI. This is the evidence for agent selection.
- If `.claude/adopted-from.md` exists, this is a **re-sync**: follow ADOPTING.md "Re-syncing
  later" instead of a first adoption. Record the template's current commit with
  `git -C /home/virgilio/claude/claude-init rev-parse --short HEAD`.

## 2. Plan, then ask

Write the plan to the scratchpad and show it to the user:

1. Agents: keep / drop / add, one line of evidence each.
2. Coverage table: every section and bullet of each existing source → destination file (per
   ADOPTING.md), with mixed bullets split.
3. Template defaults the project overrides, and in which agent or rule they will be written.
   Name collisions to rename (external tools called "agent").
4. settings.json, `.gitignore`, `.dockerignore` changes.
5. Open decisions — ask with AskUserQuestion: agents you're unsure about, `git init` for a
   non-git target, machine-local paths, personal preferences that might belong in
   `~/.claude/CLAUDE.md`.

Do not write to the target until the user approves the plan.

## 3. Back up

Before any write: `tar -czf /home/virgilio/claude/backups/<name>-<YYYYmmdd-HHMMSS>-before-claude-init.tar.gz`
of the target's `CLAUDE.md`, `CLAUDE.local.md`, `.claude/`, `docs/`, `.gitignore`,
`.dockerignore` and every other file the plan edits (only those that exist). Verify with
`tar -tzf`. For a git repo, also create the branch `chore/claude-setup`.

## 4. Apply

Copy, adapt and merge as ADOPTING.md describes. Merge into existing files; never overwrite a
file you have not read. Write `.claude/adopted-from.md`. Write the new `CLAUDE.md` last, once
every fact from the old one has a home.

## 5. Verify and review

- Run every item of ADOPTING.md "Verification checklist" and record real results.
- Then get an independent review. The target's new agents are not loaded in this session, so
  spawn a **general-purpose** agent and give it:
  - the path to the target's `.claude/agents/review-agent.md`, to follow as its instructions;
  - the coverage table and the backup path — for a non-git target, tell it to extract the
    backup into the scratchpad and compare old sources with the new files (there is no
    `git diff`);
  - explicit limits: **no edits and no writes of any kind** — it has neither the write-guard
    hook nor agent memory.

  Ask it to check: content lost or duplicated, dangling agent references, contradictions
  between rules, agents and docs. Fix findings or put them to the user.

## 6. Report

Tell the user: backup path, agents kept/dropped/added, where the old content went (the
coverage table, or a link to it), checklist results, stale auto-memory pointers, anything
skipped, and that Claude Code must be restarted in the target for the new agents and hooks to
load. Do not commit unless asked; then use `/commit`.
