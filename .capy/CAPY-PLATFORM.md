# Capy platform workflow

Mutualised workflow rules for Capy agents operating on NomenAK repositories.

## 1. Secrets and environment

- Never commit secrets, tokens, credentials, local `.env*` files, database dumps, or private config.
- Use environment variables already provided by Capy at runtime; do not persist them into repository files.
- Safe templates such as `.env.example` are allowed only when they contain placeholders and no real values.
- Do not print secret values in logs, commit messages, PR bodies, or issue comments.

## 2. Git and branch discipline

- Start from the branch requested by the task; if no branch is requested, inspect the repository default and current state before editing.
- Keep diffs narrow and targeted to the requested scope.
- Do not reformat unrelated files or introduce broad whitespace churn.
- Do not create PRs unless the task explicitly asks for a PR.
- Commit and push only when explicitly requested by the task.
- Before pushing, fetch/rebase against the requested base branch when the task specifies one.

## 3. Verification

- Re-read the task before final verification and confirm every explicit constraint.
- Run the most direct acceptance check for the change.
- For documentation-only changes, verify changed paths, markdown readability, and forbidden-file constraints instead of running expensive builds.
- If a required check fails, fix it before declaring completion.

## 4. Linear coordination

- Use the configured Linear tooling and `LINEAR_API_KEY` environment variable only; never store API keys in repository files.
- Prefer issue identifiers such as `OMG-123` in notes, commits, and PR bodies when work maps to active planning.
- For multiline Linear descriptions/comments, prefer file-based CLI flags over shell-embedded long text.
- Keep project-specific team/project names in the repository-local Captain guidance, not in this shared platform file.

## 5. Captain handoff expectations

- Preserve upstream-owned files unless the task explicitly asks to modify them.
- Record only actionable, repository-relevant context in `.capy/CAPTAIN.md`.
- Keep build/test guidance in the repository's build-owned source of truth (`.capy/BUILD.md`, root `CLAUDE.md`, `AGENTS.md`, or project docs), not in Captain-only context unless explicitly requested.
- Final status should name the branch pushed, commit SHA when available, and any skipped verification with reason.
