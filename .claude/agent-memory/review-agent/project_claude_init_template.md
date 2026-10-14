---
name: project-claude-init-template
description: claude-init is a reference Claude Code setup applied to other projects via /adopt-setup + ADOPTING.md; review adoption docs against git ls-files and a real target (e.g. ~/claude/risk)
metadata:
  type: project
---

claude-init has no app code: it is the template (agents, rules, hooks, docs stubs, settings). `ADOPTING.md` + `adopt-setup/SKILL.md` (symlinked to ~/.claude/skills/) apply it to other projects. First real target: `/home/virgilio/claude/risk` (NEON RISK, not a git repo, long CLAUDE.md, MULTIPLAYER.md, auto-memory under ~/.claude/projects/-home-virgilio-claude-risk/memory).

**Why:** review here means checking instructions, not running tests — there is no test suite.
**How to apply:** walk a real target's notes through ADOPTING's routing table; check that the
template's own CLAUDE.md sections that are about claude-init itself are excluded from copying.

Recurring problem seen 2026-10-13: verification steps for fail-closed hooks only test the
"blocked" path, which a broken hook (missing jq/realpath) also passes — always ask for a
positive (allowed-path, exit 0) check too. See [[feedback-review-scope]] if added later.
