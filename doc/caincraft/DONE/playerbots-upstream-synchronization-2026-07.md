# PlayerBots Upstream Synchronization — July 2026

## Status

Planned integration milestone. Do not merge directly into the active Caincraft
branch until the build, database update, and in-game validation pass.

## Objective

Bring Caincraft to the current official PlayerBots compatibility pair while
preserving Caincraft-specific work and local modifications.

### Target pair at planning time

| Component | Target |
| --- | --- |
| PlayerBots core | `mod-playerbots/azerothcore-wotlk`, branch `Playerbot`, current public head `c62dc73` (2026-07-03) |
| PlayerBots module | `mod-playerbots/mod-playerbots`, branch `master`, current public head `a119a6d` (2026-07-03) |

ChromieCraft/AzerothCore fixes are not included in this milestone unless they
are already contained in the official PlayerBots core target. They follow the
separate ChromieCraft adoption policy.

## Current Caincraft State

| Component | Current revision | Notes |
| --- | --- | --- |
| PlayerBots core | `c86a493` | Caincraft active `Playerbot` branch |
| PlayerBots module | `da3237fa` | Local cache matched its old remote reference, but not the current public head |

Pre-existing local work must be preserved before integration:

- root CMake edits in `modules/CMakeLists.txt` and
  `src/server/apps/CMakeLists.txt`;
- module edits for battleground eligibility and random-bot account creation;
- Caincraft documentation in `doc/caincraft`.

## Scope

### In scope

- Create recoverable backup references and integration branches.
- Fetch official PlayerBots core and module updates.
- Update both components as a tested pair.
- Resolve required compatibility conflicts without discarding Caincraft work.
- Build the server and run database updates only against a verified backup.
- Execute focused PlayerBots regression checks.
- Record results in the Caincraft changelog.

### Out of scope

- Bulk ChromieCraft/AzerothCore synchronization.
- New PlayerBots feature work beyond merge-conflict adaptations.
- Rewriting Caincraft-specific systems.
- Remote pushing until the verified result is reviewed and the Caincraft module
  fork remote is confirmed.

## Safety Plan

1. Capture Git status, remotes, and current revisions for both repositories.
2. Create immutable local backup branches before any merge/reset operation.
3. Preserve uncommitted work as explicit patch backups; do not discard it.
4. Perform the update on dedicated integration branches.
5. Back up all affected databases before allowing a newer worldserver to run
   migrations.
6. Build before starting the server.
7. Keep the active Caincraft branch untouched until acceptance checks pass.

## Integration Sequence

### A. Preserve and prepare

- Create root and module backup branches at their current heads.
- Export patches for all uncommitted Caincraft work.
- Create named root and module integration branches.
- Confirm the user-owned GitHub remotes for both the core and nested module.

### B. Synchronize the official PlayerBots pair

- Fetch `upstream/Playerbot` in the core repository.
- Fetch official `master` in the nested module repository.
- Merge the official PlayerBots core into the Caincraft integration branch.
- Update the module integration branch to the official module target.
- Resolve conflicts minimally and document each Caincraft-specific adaptation.

### C. Build and migrate safely

- Reconfigure only if the new core/module requires it.
- Build the affected targets on Windows.
- Start against database backups and allow the normal updater to complete.
- Capture updater output and stop if an unexpected migration failure occurs.

### D. Acceptance checks

- Worldserver starts without PlayerBots/module load errors.
- A fresh Hunter bot completes ranged opening, melee fallback, loot, follow,
  and basic recovery checks.
- A normal group forms, follows, resurrects, and completes a short combat run.
- Random bots can log in, join/leave a battleground queue, and avoid immediate
  errors.
- Existing Caincraft custom systems still load and compile.

### E. Delivery

- Update `doc/caincraft/CHANGELOG.md` with the exact final revisions and
  validation result.
- Commit the integration as a reviewable milestone.
- Push the core and module branches to the appropriate Caincraft GitHub forks
  only after their remotes are confirmed.

## Rollback

Rollback is by returning to the backup branches and restoring the database
backup. No destructive reset is required.

## Completion Criteria

- The core and module resolve to the chosen current official PlayerBots pair.
- Caincraft local work is preserved and either reapplied or explicitly deferred.
- Build and database updater complete successfully.
- Acceptance checks are recorded.
- Core/module remotes and push destinations are documented.
