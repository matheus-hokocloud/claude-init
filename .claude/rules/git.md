# Git

## Commits

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<optional scope>): <subject>

<body: what changed and why, wrapped at 72>
```

- **Types:** `feat`, `fix`, `refactor`, `perf`, `test`, `docs`, `build`, `ci`, `chore`.
  Breaking changes add `!` after the type and a `BREAKING CHANGE:` footer.
- **Subject:** imperative, lowercase, no trailing period, at most 72 characters.
- **Body:** explain why, not how; skip it only when the subject says everything.
- One logical change per commit. Don't mix a refactor with a behavior change.

## Before committing

- Commit only when asked. Review is done (see `workflow.md`) and checks pass.
- Stage files by name; never `git add -A` blindly. Read `git diff --staged` first.
- Never commit secrets, `.env` files, credentials, or local settings.

## History

- Work on a branch; don't commit straight to `main` once it has history.
- Never force-push, rewrite or amend commits that are already pushed, and never skip
  hooks (`--no-verify`), unless the user explicitly asks.
