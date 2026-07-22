# PlayerBots: Low-Level Hunter Melee Fallback

## Status

Draft implementation note.

## Objective

Make a low-level Hunter bot continue fighting after its target reaches melee
range. The bot must start normal melee swings and use Raptor Strike when it is
known and usable.

This is a narrowly scoped combat-continuity fix. It is not a Hunter rotation,
kiting, pet, equipment, or general combat-strategy redesign.

## Player-Facing Problem

A newly created Hunter can engage a target at range, but stops contributing
once that target reaches melee range. In this state Auto Shot cannot fire and
the bot has no viable low-level fallback action.

The expected level-1 experience is simple: if a mob is standing on the Hunter,
the Hunter should melee it and queue Raptor Strike where possible.

## Confirmed Cause

`GenericHunterStrategy` handles `enemy within melee` by preferring Explosive
Trap, Mongoose Bite, and Wing Clip. These actions are unavailable to a fresh
Hunter.

The spec strategies then fall through to ranged default actions ending in Auto
Shot. Auto Shot is invalid in its dead zone.

There is a `SwitchToMeleeTrigger`, but this patch must not depend on it:

- no Hunter strategy currently adds it as a `TriggerNode`;
- it requires the named `ranged` strategy, while the Hunter factory adds
  `bm`, `mm`, or `surv` instead; and
- changing that state-machine behaviour has a larger, higher-risk surface.

Raptor Strike already has an action node with `melee` as a prerequisite, but
it is currently only reached as the fallback for Mongoose Bite. That leaves
low-level Hunters without a path to it.

## Scope

### In scope

- Add a low-risk close-range fallback to `GenericHunterStrategy`.
- Prefer Raptor Strike when it is available.
- Ensure normal melee auto-attacks begin when no higher-priority close-range
  Hunter action can be used.
- Manually verify the behaviour for low-level solo and grouped Hunters.

### Out of scope

- Reworking `SwitchToMeleeTrigger` or adding/removing the `ranged` and `close`
  strategies.
- Altering Auto Shot, pathfinding, target selection, pet AI, or Hunter gear.
- Changing the behaviour of Hunters that can safely kite or use an existing
  higher-priority escape/control action.
- Database or configuration changes.

## Implementation Design

### Target file

`modules/mod-playerbots/src/Ai/Class/Hunter/Strategy/GenericHunterStrategy.cpp`

### Change

Extend the existing `enemy within melee` trigger with two ordered fallback
actions:

1. `raptor strike`
2. `melee`

They must be lower priority than the existing close-range actions:

| Action | Intended priority | Reason |
| --- | ---: | --- |
| Explosive Trap | 37.0 | Preserve existing trap behaviour. |
| Mongoose Bite | 22.0 | Preserve an available reactive melee attack. |
| Wing Clip | 21.0 | Preserve snare behaviour. |
| Raptor Strike | 20.5 | Use the level-appropriate melee special when possible. |
| Melee | 20.0 | Guaranteed basic-attack fallback. |

Illustrative result:

```cpp
triggers.push_back(new TriggerNode(
    "enemy within melee",
    { NextAction("explosive trap", 37.0f),
      NextAction("mongoose bite", 22.0f),
      NextAction("wing clip", 21.0f),
      NextAction("raptor strike", 20.5f),
      NextAction("melee", 20.0f) }));
```

No new trigger, action, spell lookup, configuration option, or database row is
required.

### Why this is sufficient

- `enemy within melee` already provides the correct range gate.
- The existing Raptor Strike action node asks for `melee` first, so it can
  initiate melee attack before queueing the next melee-swing spell.
- If Raptor Strike is unavailable, on cooldown, or otherwise unusable, the
  normal `melee` action starts basic swings.
- Existing disengage/flee actions use higher priorities where they are active,
  so a Hunter that has a usable escape retains that behaviour.

### Non-goal: strategy switching

Do not add a `switch to melee` action in this change. The requested behaviour
is an immediate, reliable fallback once the target is in melee range; it does
not require changing the active combat strategy. A later, separately tested
change may repair or remove the dormant switch triggers.

## Behaviour Contract

| Situation | Expected result |
| --- | --- |
| Level-1 Hunter; target reaches melee | Starts melee attacking and queues Raptor Strike if learned. |
| Raptor Strike unavailable or unusable | Starts/continues normal melee swings. |
| Wing Clip or Mongoose Bite is usable | Those existing higher-priority actions are attempted first. |
| Ranged target outside melee | Existing ranged rotation and Auto Shot remain unchanged. |
| Target is close but an escape action is valid | Existing higher-priority disengage/flee behaviour remains unchanged. |

## Verification Plan

Run these in-game checks with an unmodified test database and a Hunter bot that
has normal starter equipment and spells.

1. **Level 1, solo**
   - Start combat against one ordinary melee creature.
   - Allow the creature to reach the bot.
   - Verify the Hunter begins melee swings instead of idling or repeatedly
     attempting Auto Shot.
   - Verify Raptor Strike is queued/cast when it is known and the next melee
     swing permits it.

2. **Level 4-5, solo**
   - Repeat with Serpent Sting and Auto Shot available.
   - Confirm ranged opening behaviour is unchanged and melee fallback begins
     only after the target closes.

3. **Hunter with Wing Clip/Mongoose Bite available**
   - Allow a melee target to reach the Hunter.
   - Confirm the existing close-range ability is still selected before the
     fallback.
   - Confirm the Hunter resumes melee swings if that action cannot be used.

4. **Grouped Hunter**
   - Use a tanked target and a target that switches to the Hunter.
   - Confirm the Hunter does not become idle in either case.
   - Confirm existing group positioning, disengage, and flee behaviour is not
     visibly regressed.

5. **Regression checks**
   - Test a target outside melee range: Auto Shot still starts and persists.
   - Test a target dying or changing target in melee: no attack-loop, crash,
     or stuck combat state.

Record character level, learned spells, whether the bot is solo/grouped, target
type, and observed action sequence for each case.

## Completion Criteria

- A low-level Hunter no longer idles when its target is in melee range.
- Raptor Strike is used when appropriate; basic melee is used otherwise.
- The source change is limited to the generic Hunter strategy.
- No configuration, database, core, or unrelated PlayerBots file changes are
  included.
- All verification scenarios above pass and are captured in the implementation
  or pull-request notes.

## Risk and Rollback

Risk is low: the change adds fallback actions only after existing
higher-priority close-range actions. If it produces unwanted high-level Hunter
behaviour, revert the two added `NextAction` entries; it has no persistent
state or data migration.
