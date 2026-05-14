# Captain context — azerothcore-wotlk-chronicle (fork)

> Voir aussi [.capy/CAPY-PLATFORM.md](./CAPY-PLATFORM.md).

## What this is

Fork of `mod-playerbots/azerothcore-wotlk` (itself forked from `azerothcore/azerothcore-wotlk`). It provides the AzerothCore `worldserver` / `authserver` / DB baseline consumed by Chronicle for the WoW 3.3.5a narrative private-server stack.

This repository is the core fork, not the main gameplay feature surface. Keep Chronicle-specific core divergence rare and prefer sibling modules, Chronicle scripts, or consumer-side compose/config overrides whenever possible.

Root `CLAUDE.md` is upstream-owned build/code guidance. `.capy/CAPTAIN.md` is the fork-local complement for Captain agents.

## Branches

| Branch | Role | Notes |
|---|---|---|
| `Playerbot` | Default branch / upstream sync | Tracks `mod-playerbots/azerothcore-wotlk@Playerbot`; use for sync/process cleanup unless a task says otherwise. |
| `chronicle/main` | Chronicle integration | Pinned by Chronicle's `azerothcore/` submodule; carries Chronicle-specific delta on top of `Playerbot`. |
| `master` | Upstream-upstream reference | Tracks `azerothcore/azerothcore-wotlk@master` for reference/cherry-picks. |
| Feature branches | Task work | Narrow branches for docs/fixes/features; target `Playerbot` or `chronicle/main` according to task scope. |

## Consumed by

- `NomenAK/Chronicle` consumes this fork via submodule `azerothcore/`, pinned to `chronicle/main` by SHA.
- Chronicle Docker compose and smoke validation consume the pinned submodule SHA, not a floating branch.
- Validate Chronicle integration downstream in `NomenAK/Chronicle` when a change affects runtime, modules, configs, SQL, or C++ behavior.

## Upstream sync strategy

- Watch upstream through Chronicle's `playerbots-upstream-watch` automation.
- `OMG-169` tracks adoption/evaluation of upstream PlayerBotsDatabase-as-core movement.
- Cadence is opportunistic: CVEs, needed features, build/runtime fixes, or explicit human request; no blind fixed-schedule sync.
- Sync direction is upstream → `Playerbot` → `chronicle/main`.
- Resolve conflicts by minimizing Chronicle divergence; rebase/rework Chronicle delta on top of upstream where practical.
- Do not backport Chronicle-only changes into `Playerbot` unless explicitly approved.

## Fork policy

- Keep divergence minimal and easy to rebase.
- Prefer modules and consumer-side overrides over core patches.
- Keep fork-local agent/process files under `.capy/`.
- Avoid broad formatting, whitespace, generated-file, or vendored-asset churn.
- If a change is broadly useful, prefer upstream contribution or an isolated patch over Chronicle-private core drift.

## Hard rules

1. **DO NOT modify upstream `src/`** unless the patch is explicitly requested, Chronicle-specific, and cannot live in a sibling module.
2. **DO NOT modify root `CLAUDE.md`**; it is upstream-owned. Keep Captain context here.
3. **DO NOT delete, rename, or rewrite shipped upstream files** unless necessary for the task.
4. **Commit messages use Conventional Commits** per root `CLAUDE.md`.
5. **SQL updates go under `data/sql/updates/pending_*`** until the upstream PR/merge process moves them; do not edit existing non-pending SQL for unmerged work.
6. **Never write secrets to `.env*` files**; see `.capy/CAPY-PLATFORM.md` §1.
7. **Do not touch `modules/` submodule contents** unless the task explicitly concerns submodule pointer management.
8. **No merge of draft cleanup PRs** without human review.
9. **Do not create `.capy/BUILD.md`** for this fork; build guidance stays in root `CLAUDE.md` and Chronicle consumer documentation.

## CI

- Upstream CI applies on `Playerbot` for AzerothCore/playerbots build matrix coverage.
- Chronicle integration validation happens downstream through Chronicle smoke/build flows against the pinned submodule SHA.
- For docs/process-only changes, verify changed paths and markdown; skip expensive C++ builds unless requested.
- For C++/SQL/runtime changes, follow root `CLAUDE.md` and document any skipped in-game validation.

## Linear / coordination

- Team/project context: `Omega · Chronicle · AzerothCore`.
- Keep active sync/build/module work tied to relevant `OMG-*` issues when applicable.
- Generic Linear workflow and secret handling lives in `.capy/CAPY-PLATFORM.md` §4.

## Review checklist

- Target branch matches task/base (`Playerbot` for upstream-sync/process work; `chronicle/main` for Chronicle integration deltas unless instructed otherwise).
- `CLAUDE.md` is untouched.
- No `.capy/BUILD.md` was created.
- `modules/` is untouched unless explicitly requested.
- No secrets, `.env*`, local paths, build outputs, generated DB dumps, or vendored artifacts are staged.
- Diff is narrow; no broad whitespace churn.
- PR/body/handoff states whether Chronicle must update its submodule pin after merge.

## Related repos

- Chronicle consumer: `NomenAK/Chronicle`
- Playerbots module sibling fork: `NomenAK/mod-playerbots-chronicle`
- Upstream AzerothCore: `azerothcore/azerothcore-wotlk`
- Upstream playerbots core fork: `mod-playerbots/azerothcore-wotlk`
