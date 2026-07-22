# Caincraft Near-Term Experience Roadmap

## Status

Living roadmap, established 2026-07-22.

This document turns the broader [Guiding Light](playerbots-single-player-guiding-light.md)
into the next small, player-visible improvements. It is deliberately ordered by
experience impact and reversibility, not by technical novelty.

## How to Use This Roadmap

- Complete one slice before beginning the next.
- Every code, configuration, database, or content slice gets its own focused
  implementation guide before work begins.
- A candidate becomes an implementation only after it has a reproducible
  player-facing observation.
- Keep the PlayerBots and AzerothCore/data lanes separate unless a defect
  genuinely requires both.

## Now — Close the Hunter Loop

### 0.2.1 acceptance: Low-Level Hunter Melee Fallback

**Owner:** PlayerBots AI
**Why first:** The code is built and startup-tested, but direct player-facing
validation is still outstanding.

Run and record the scenarios in the
[Hunter melee fallback guide](playerbots-low-level-hunter-melee-fallback.md):

1. Level-1 solo Hunter versus an ordinary melee creature.
2. Level 4-5 ranged opening followed by a target closing to melee.
3. Hunter with Wing Clip or Mongoose Bite available.
4. Grouped Hunter, including a target switching to the Hunter.

**Done when:** The observed action sequence proves the Hunter starts melee
swings and queues Raptor Strike when appropriate, without regressing ranged
combat or escape behaviour.

**Do not expand this slice:** If a failure appears, record it as a new guide;
do not fold a strategy-switch, pathing, pet, or rotation rewrite into this fix.

## Next — Make Starter Combat Dependable

### 0.2.2: Levels 1-10 Combat Continuity Audit

**Owner:** PlayerBots AI
**Experience goal:** A fresh companion should never become idle simply because
its preferred spell, range, resource, or weapon state is unavailable.

This is an investigation-and-test slice first, not a broad code change.

- Create a small starter roster: one bot for every playable class, normal
  starter gear, starter spells only.
- Test one ordinary melee enemy and one ranged enemy per class.
- Record idle time, repeated unusable actions, missing basic attacks, resource
  failures, and target-loss behaviour.
- Rank findings by frequency and by whether they prevent the player from
  adventuring.

**Expected deliverable:** One focused guide for the single highest-impact
failure found. Examples might include a caster's basic-attack fallback or a
melee class's unreachable-target recovery, but no fix is pre-approved until
the observation confirms it.

**Done when:** The audit yields an evidence-backed next guide, not a vague
list of possible class rewrites.

### 0.2.3: Companion Readiness — One Failure at a Time

**Owner:** PlayerBots AI, with core/data only if the game state is incorrect.
**Experience goal:** Bots should arrive at basic combat able to participate.

Start with the first real failure observed during normal play from this list:

- no ammunition or a failed ammunition refresh;
- missing food, drink, reagents, repair, or bag space;
- Hunter pet missing, dead, or not recovered after a normal interruption;
- unusable starter weapon/equipment state.

**Scope rule:** Choose exactly one readiness failure, write its guide, and
verify it across relog, death, vendor, and group transitions as applicable.

**Done when:** That specific failure has a reliable recovery path and a clean
rollback.

## Then — Remove the Most Visible Babysitting

### 0.2.4: Target, Follow, and Regroup Recovery

**Owner:** PlayerBots AI.
**Experience goal:** After an ordinary disruption, bots recover without the
player issuing repeated commands.

Observe and isolate one of these high-value failures:

- current target dies, evades, or becomes unreachable;
- bot stops following after combat, loot, a taxi, summon, or resurrection;
- party does not wait, drink, mount, or regroup coherently;
- a bot remains stuck after a recoverable movement interruption.

**Implementation shape:** Prefer a small recovery ladder—retry, reposition,
choose a safe alternative, report the failure—over teleporting or silently
forcing state.

**Done when:** The chosen interruption resolves during a normal play session
without a repeated manual bot command.

## Parallel AzerothCore/Data Lane

### Starter Journey Reliability Ledger

**Owner:** AzerothCore/data.

Run one repeatable starter-to-level-20 route with the test roster and log only
defects that affect a real player too: broken quest objectives, impossible
interactions, invalid spell mechanics, pathing, loot, or movement.

For each entry, record:

- exact reproduction route and expected retail-like behaviour;
- whether it is core code, database/content data, map data, or PlayerBots AI;
- an upstream AzerothCore issue/fix if one exists; and
- whether a ChromieCraft report is useful as QA evidence.

Adopt a ChromieCraft-aligned fix only through the existing
[benchmark and adoption policy](chromiecraft-benchmark-and-adoption-policy.md):
small, independently reproducible, upstream-aware, and tested on Caincraft.

**First deliverable:** A short ranked ledger—not a bulk merge—with the first
candidate selected for its own implementation guide.

## Recommended Sequence

| Order | Slice | Player-visible outcome | Gate before implementation |
| ---: | --- | --- | --- |
| 1 | Hunter acceptance | Confirm melee fallback really fixes the observed gap. | In-game observations recorded. |
| 2 | Levels 1-10 combat audit | Find the next actual idle-combat failure. | Reproducible class/test scenario. |
| 3 | One readiness fix | Bots begin ordinary combat prepared. | One named readiness failure. |
| 4 | One recovery fix | Fewer commands after normal interruptions. | One named target/follow/regroup failure. |
| 5 | Starter journey ledger | Separate AI defects from core/data defects. | Repeatable route and evidence. |

## What Comes After These Slices

If the sequence above reduces obvious idle, readiness, and regroup failures,
move into Phase 2 of the Guiding Light: quest selection, travel, party
cohesion, role-aware group fundamentals, and leveling-dungeon reliability.
Do not advance merely because a calendar date changes; advance when the
player experience supports it.
