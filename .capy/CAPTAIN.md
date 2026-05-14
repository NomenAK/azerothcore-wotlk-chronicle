# Captain context — azerothcore-wotlk-chronicle (fork)

## What this is

Fork of `mod-playerbots/azerothcore-wotlk` (which forks `azerothcore/azerothcore-wotlk`). Provides the AzerothCore worldserver / authserver / DB schemas baseline for the Chronicle project (WoW 3.3.5a narrative private server).

This fork exists to host Chronicle-specific patches that should NOT round-trip upstream (cross-faction baseline, specific module wiring, etc.). The fork must remain minimally divergent — most module functionality lives in separate sibling submodules under `modules/` (mod-playerbots, mod-ale, mod-autobalance, etc.).

Treat this repository as the core fork, not the gameplay feature surface. If a change can be implemented as a module, a Chronicle script, or a consumer-side compose/config override, keep it out of core.

## Branches

| Branch | Role | Notes |
|---|---|---|
| `Playerbot` | upstream sync (default branch) | Mirrors `mod-playerbots/azerothcore-wotlk@Playerbot` — pulled regularly |
| `chronicle/main` | Chronicle integration branch | Pinned by Chronicle's `azerothcore/` submodule (SHA `ef699bd7` as of 2026-05-14). Carries Chronicle-specific divergence on top of `Playerbot` |
| `master` | upstream-upstream sync | Mirrors `azerothcore/azerothcore-wotlk@master` — kept for reference / cherry-picks |
| Feature branches | `feat/wave*-modules`, `fix/*` | Per-PR work, target `chronicle/main` unless explicitly doing upstream-sync cleanup |

## Consumed by

- [`NomenAK/Chronicle`](https://github.com/NomenAK/Chronicle) — uses this fork as a submodule at `azerothcore/`, pinned to `chronicle/main`
- Chronicle Docker compose / smoke builds consume the submodule SHA, not a floating branch
- Local developer work may use this checkout directly for C++ investigation, but Chronicle integration should be validated through the consumer repo when relevant

## Upstream sync strategy

- Track upstream PRs via Chronicle's `playerbots-upstream-watch` automation (see `Chronicle/scripts/playerbots-upstream-watch.js`)
- Notable: [OMG-169](https://linear.app/neuromantes/issue/OMG-169) tracks upstream PR #168 (PlayerbotsDatabase as core) — to be adopted when stable
- Pull cadence: opportunistic, triggered by CVE alerts or feature needs (not on a fixed schedule)
- Conflict resolution: prefer keeping Chronicle divergence minimal; if upstream rewrites our patches, rebase Chronicle delta on top
- Sync direction is upstream → `Playerbot` → `chronicle/main`; do not backport Chronicle-only changes into `Playerbot` unless explicitly approved
- When evaluating upstream movement, inspect both AzerothCore core changes and playerbots-specific deltas before accepting a merge/rebase

## Fork policy

- Core patches must be rare, small, and documented in the PR body with why a module or config override was insufficient
- Prefer sibling module repositories for Chronicle gameplay features and isolated module configuration
- Keep fork-only files under clearly owned paths such as `.capy/` when the content is agent/process context
- Avoid broad formatting, whitespace, or generated-file churn; upstream rebases become expensive when noise accumulates
- Do not vendor generated assets, client data, map extracts, or build outputs into this repository
- If a patch is broadly useful to upstream, open it upstream first or keep it separate from Chronicle-private divergence

## Hard rules

1. **DO NOT modify upstream code** in `src/` unless the patch is Chronicle-specific and cannot live in a sibling module. Prefer module-based extensions in `modules/`.
2. **DO NOT modify `CLAUDE.md`** at the root — it is upstream-owned. Chronicle-specific context lives here in `.capy/CAPTAIN.md`.
3. **DO NOT delete or rename** files versioned by upstream — every diff creates merge friction. Add new files; do not edit shipped ones unless necessary.
4. **Commit messages** follow Conventional Commits (see `CLAUDE.md` §Commit Message Format): `Type(Scope/Subscope): Short description (max 50 chars)`. AC scopes: `Core`, `DB`. Modules: own scope.
5. **SQL updates** go in `data/sql/updates/pending_*` until PR merged (then move to `data/sql/updates/`). NEVER edit files outside `pending_*` for unmerged work.
6. **Never write secrets to `.env` files** — Capy env vars hydrate in memory.
7. **Do not touch `modules/` submodule contents** from this repo unless the task is explicitly about submodule pointer management.
8. **Do not merge draft cleanup PRs** without human review; this fork is a dependency for Chronicle runtime work.

## CI

- Upstream CI workflows run on `Playerbot` branch (AC matrix builds)
- Chronicle-specific CI runs from the Chronicle repo (the submodule's pinned SHA is what gets built by Chronicle's docker-compose smoke)
- For documentation/process-only changes, verify changed paths and skip expensive C++ builds unless requested
- For C++ or SQL changes, follow the root `CLAUDE.md` build/test guidance and document any skipped in-game validation

## Linear / coordination

Workspace: `neuromantes`. Team: `OMG`. Project: `Omega · Chronicle · AzerothCore`. Use `linear` CLI fallback. Never persist API keys to repo files.

Mention relevant Linear IDs in PR descriptions when the work maps to active planning, especially sync, module, and build-environment tickets. Keep PRs narrow enough that Chronicle can decide whether to consume the resulting SHA independently.

## Review checklist

- Confirm target branch: `Playerbot` for upstream-sync process cleanup, `chronicle/main` for Chronicle integration deltas
- Confirm `CLAUDE.md` is untouched
- Confirm `modules/` is untouched unless explicitly requested
- Confirm no secrets, local paths, build outputs, or generated DB dumps are staged
- Confirm `.gitignore` changes are fork-local and minimal
- Confirm PR body states whether Chronicle submodule pin needs updating after merge

## Related repos

- Chronicle (consumer): https://github.com/NomenAK/Chronicle
- mod-playerbots-chronicle (sibling fork, submodule under `modules/mod-playerbots` on upstream): https://github.com/NomenAK/mod-playerbots-chronicle
- Upstream AzerothCore: https://github.com/azerothcore/azerothcore-wotlk
- Upstream mod-playerbots fork base: https://github.com/mod-playerbots/azerothcore-wotlk
