# ChromieCraft Benchmark and Adoption Policy

## Purpose

ChromieCraft is Caincraft's primary external benchmark for AzerothCore
stability, gameplay correctness, and content quality. Its public issue tracker
provides a valuable record of player-observed problems, triage, content-phase
classification, and fixes that have reached AzerothCore.

ChromieCraft is a benchmark and a source of evidence. It is not a source tree
to merge wholesale into the PlayerBots core.

## Source Repositories

| Source | Role in Caincraft work |
| --- | --- |
| [chromiecraft/chromiecraft](https://github.com/chromiecraft/chromiecraft) | Bug tracker, issue triage, player-facing validation, and project-by-bracket progress. |
| [chromiecraft/azerothcore-wotlk](https://github.com/chromiecraft/azerothcore-wotlk) | Read-only public AzerothCore mirror used to identify the published core revision. |
| [azerothcore/azerothcore-wotlk](https://github.com/azerothcore/azerothcore-wotlk) | Upstream source of generic core and database fixes. |
| [mod-playerbots/azerothcore-wotlk](https://github.com/mod-playerbots/azerothcore-wotlk) | Compatibility baseline for Caincraft's PlayerBots-specific core branch. |

ChromieCraft's own contribution guidance directs reproducible generic bugs to
the main AzerothCore tracker and marks their issue ready only after the
upstream fix reaches their server. This makes its tracker especially useful for
identifying validated, player-relevant AzerothCore work.

## Current Baseline

On 2026-07-22, the published ChromieCraft mirror reference is
`8037e4c719d288a28a411925b6270646101d89ba` (2026-06-18). Caincraft's active
Playerbot core is `c86a49335617dedb5dfd8ca8a152f93d87f64e1c` (2026-05-18).

The histories have diverged:

| Comparison | Commits unique to that side |
| --- | ---: |
| ChromieCraft reference | 280 |
| Caincraft Playerbot branch | 549 |

This is not a simple "Caincraft is behind ChromieCraft" count. The PlayerBots
core carries its own compatibility work, so each candidate fix requires an
intentional compatibility decision.

## Adoption Principles

### Prefer upstream fixes over server-specific copies

If ChromieCraft identifies a problem and the fix has reached AzerothCore,
evaluate the upstream commit first. This preserves provenance and makes future
updates easier.

### Use ChromieCraft as validation evidence

For every candidate, record the ChromieCraft issue, relevant AzerothCore issue
or pull request, the observed in-game behaviour, and the target Caincraft test
scenario.

### Preserve PlayerBots compatibility

Do not merge or rebase broad ChromieCraft/AzerothCore history directly into the
Playerbot branch. Bring in one validated fix or a tightly related series only
after confirming the PlayerBots core and module still build and behave
correctly.

### Prioritize the solo-player journey

Favor stability and content fixes that improve the route Caincraft players
actually take: starter zones, quests, class mechanics, travel, loot, grouping,
dungeons, and selected raids.

### Respect different server goals

ChromieCraft is a public progressive realm; Caincraft is a custom
single-player-with-bots experience. Do not adopt economy, progression,
population, PvP, anti-cheat, or operational changes simply because they are
appropriate for ChromieCraft.

## Candidate Tiers

| Tier | Candidate | Default decision |
| --- | --- | --- |
| 1 | A merged AzerothCore fix that ChromieCraft has validated in relevant content | Evaluate for direct, isolated integration. |
| 2 | A ChromieCraft report linked to an open AzerothCore issue | Use as a reproduction and test case; do not implement blindly. |
| 3 | A ChromieCraft-only module, configuration, policy, or operational feature | Evaluate against Caincraft's single-player goals; usually adapt rather than copy. |
| 4 | A broad branch update, rebase, or unrelated batch of commits | Defer until there is a dedicated compatibility plan and test window. |

## Candidate Adoption Workflow

1. **Find the evidence**
   - Start with a player-observed Caincraft failure or a ChromieCraft issue in
     relevant level/content brackets.
   - Capture links, expected behaviour, reproduction steps, and the applicable
     ChromieCraft/AzerothCore revision.

2. **Classify ownership**
   - Decide whether the issue is PlayerBots AI, generic core/data, configuration,
     or a combined issue.
   - A bot that makes a bad decision is not automatically a core bug.

3. **Write the Caincraft guide**
   - Create a focused implementation guide in `doc/caincraft` before any code
     change.
   - Include upstream and ChromieCraft references, exact candidate commits, the
     compatibility approach, test plan, and rollback plan.

4. **Choose an integration method**
   - **Already present:** verify and close the Caincraft investigation.
   - **Clean isolated commit:** cherry-pick or reproduce the change in a
     dedicated Caincraft branch.
   - **Conflict or PlayerBots-sensitive change:** manually adapt the minimal
     logic; do not force a merge.
   - **Large dependency chain:** defer to a planned core-sync milestone.

5. **Validate on Caincraft**
   - Build the appropriate server/module target.
   - Run the direct scenario and nearby PlayerBots regressions.
   - Record the exact test result, not only that the server starts.

6. **Deliver in a bite-sized commit**
   - Update `doc/caincraft/CHANGELOG.md`.
   - Commit only the intended fix, required data/configuration, and its guide.
   - Push after review and verification under the Caincraft change process.

## Review Checklist

Before adopting a ChromieCraft/AzerothCore candidate, answer all questions:

- What exact player-visible failure does it resolve on Caincraft?
- Is the fix already in the active Playerbot branch or module?
- Is it generic core/data correctness, or bot decision logic?
- What PlayerBots-specific code could it affect?
- Does it alter database schema, pending SQL, configuration, or generated maps?
- Can it be delivered as one revertible commit?
- Which fresh-character, grouped, dungeon, or raid test proves it works?
- Is the behaviour desirable for a single-player server, not only a public
  progressive realm?

## Initial Focus Areas

Use ChromieCraft's issue tracker and upstream-linked fixes to investigate:

1. Starter-zone and low-level quest blockers.
2. Class mechanics that prevent basic bot or player combat.
3. Movement, pathing, transport, and vehicle failures.
4. Loot, quest-object, and interaction correctness.
5. Dungeon encounter mechanics needed by Caincraft's progression route.
6. Group, LFG, battleground, and resurrection stability where PlayerBots
   relies on generic core behaviour.

## What This Policy Does Not Authorize

- Blind merges, rebases, or mass cherry-picks from ChromieCraft or AzerothCore.
- Replacing the PlayerBots core branch with ChromieCraft's mirror.
- Copying ChromieCraft's public-server policies, modules, economy, or
  progression without a Caincraft design decision.
- Remote pushes before a guide, validation record, changelog entry, and review.
