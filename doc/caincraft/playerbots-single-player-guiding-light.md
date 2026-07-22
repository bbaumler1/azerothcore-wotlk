# Caincraft PlayerBots & AzerothCore Guiding Light

> A living vision and delivery guide for making Caincraft an excellent
> single-player World of Warcraft experience with PlayerBots.

## Document Status

- **Status:** Living document
- **Last updated:** 2026-07-22
- **Audience:** Caincraft maintainers and future contributors
- **How to use it:** Refine this document when lessons from live play change a
  priority, assumption, or success criterion. Create a focused implementation
  note before changing code for a specific behaviour.

## The Vision

Caincraft should make solo World of Warcraft feel complete, social, and
dependable. Bots are companions who make adventuring possible and enjoyable;
they must never become work the player has to constantly supervise.

The end state is not "perfect human simulation." It is a world where one real
player can level, quest, dungeon, raid, explore, and progress naturally with a
reliable community of bots.

## North-Star Experience

A player should be able to:

1. Create a new character and immediately find capable companions.
2. Level through zones without bots repeatedly stalling, idling, or requiring
   rescue.
3. Run dungeons with a party that follows sensible combat and recovery rules.
4. Progress into selected raids with encounter support appropriate to the
   content.
5. Feel that towns, groups, trade, PvP, and the wider world have purposeful
   activity even when no other human is online.

## Design Principles

### Reliability before cleverness

An unglamorous fallback that keeps a bot fighting, moving, or regrouping is
more valuable than an impressive rotation that fails at a common edge case.

### Single-player first

Optimize for one human with a party or raid of bots. Large-population scaling is
useful only where it improves that experience; it is not the primary goal.

### Small, observable changes

Each improvement should have a narrow problem statement, reproducible test
case, completion criteria, and rollback path. Prefer a series of small wins to
a risky rewrite.

### Put fixes in the right layer

Use PlayerBots for decisions and intent. Use AzerothCore or its data for
generic game correctness. Do not hide a core bug inside bot-only behaviour.

### Preserve game feel

Bots may use carefully chosen conveniences, but should retain meaningful
combat, travel, gearing, progression, and social choices. Avoid solving every
problem with teleportation, cheats, or silent state changes.

### Learn from play

Play sessions are product research. Record what looked broken, surprising,
annoying, or delightful, then prioritize by player impact rather than code
novelty.

## The Two Workstreams

| Workstream | Primary question | Typical work |
| --- | --- | --- |
| **PlayerBots AI** | What should this bot decide to do? | Combat priorities, strategy/action/trigger behaviour, grouping, recovery, quests, social behaviour, bot controls. |
| **AzerothCore and data** | Can the server correctly support that behaviour? | Spell mechanics, movement, pathing, encounters, quest/loot data, generic server hooks, stability. |

### Ownership rule

| If the issue is... | Start in... |
| --- | --- |
| A real player and a bot would both experience the wrong game behaviour | AzerothCore or database/content data |
| The game works, but the bot chooses poorly | PlayerBots |
| The bot lacks information the core already knows | Prefer a narrow core hook, then make the decision in PlayerBots |
| A one-off encounter mechanic | Encounter strategy/script after generic behaviour is confirmed |

## Delivery Roadmap

The phases are directional, not gates. A small, high-value issue may move ahead
when it is safe and well understood.

### Phase 0: Workshop and safety

**Purpose:** Make improvement work repeatable and safe.

PlayerBots AI:

- Keep a short, named test roster: fresh characters, a level-20 party, a
  level-80 dungeon party, and a raid roster.
- Establish concise decision/debug traces for difficult behaviour.
- Document bot configuration profiles for the intended single-player server.

AzerothCore and data:

- Keep reliable database backups and a known-good test state.
- Verify maps, mmaps, vmaps, and data updates before blaming AI.
- Keep core and module compatibility intentional and documented.

**Exit signal:** A defect can be reproduced, tested, and reverted without
guesswork.

### Phase 1: Quick wins and basic dignity

**Purpose:** Remove behaviours that immediately make bots feel broken.

PlayerBots AI:

- Low-level class fallback audit: every class can take a useful combat action
  with starter spells and equipment.
- Ammo, food, drink, gear, bag, pet, and repair readiness.
- Target loss, loot completion, follow, summon, resurrection, and regroup
  reliability.
- First stuck-detection and safe recovery rules.

AzerothCore and data:

- Fix concrete spell, movement, quest, loot, and pathing defects discovered in
  the starter-zone test route.
- Correct content data that makes a valid objective impossible.

**Representative note:**
[Low-Level Hunter Melee Fallback](playerbots-low-level-hunter-melee-fallback.md).

**Exit signal:** A fresh player can adventure with bots for an hour without an
idle-combat failure, repetitive stall, or abandoned basic objective.

### Phase 2: Reliable leveling journey

**Purpose:** Make bots dependable across zones, travel, and ordinary groups.

PlayerBots AI:

- Recovery ladder: retry, reposition, choose an alternative, report failure,
  then safely abandon only when necessary.
- Quest selection informed by level, prerequisites, travel cost, group fit,
  inventory space, and recent failures.
- Party cohesion: wait, regroup, drink, mount, avoid accidental pulls, and
  resume after interruptions.
- Role-aware fundamentals: threat, healing triage, mana management, interrupts,
  dispels, crowd control, and defensive cooldowns.

AzerothCore and data:

- Repair generic navigation and interaction defects identified by telemetry.
- Add only the core hooks needed to expose reliable game state to PlayerBots.
- Address recurring broken quest/objective data.

**Exit signal:** Several zones and leveling dungeons can be completed as a
normal play loop rather than a supervised test.

### Phase 3: Excellent party and dungeon companions

**Purpose:** Turn functional bots into trustworthy group members.

PlayerBots AI:

- Leader intent: pull, hold, retreat, skip, loot, drink, mount, and regroup.
- Sensible target priority, threat handling, interrupts, CC assignments, and
  healer protection.
- Generic dungeon state: trash, patrols, boss pulls, wipes, resets, and safe
  recovery.
- Clear player-facing controls for aggressive, cautious, hold, follow, assist,
  focus, and loot intent.

AzerothCore and data:

- Correct dungeon encounters and movement issues that block valid bot tactics.
- Improve generic combat and targeting behaviour where it affects all players.

**Exit signal:** A five-player dungeon is predictable and enjoyable without
constant commands, cheats, or resets.

### Phase 4: A living solo world

**Purpose:** Make the world feel populated and purposeful.

PlayerBots AI:

- Dynamic bot distribution by faction, level, zone, and player activity.
- Useful profession, trade, grouping, and guild participation.
- Social personality that adds flavour without spam or unreliability.
- Objective-aware battleground behaviour: squads, defenders, healers, retreat,
  and queue balancing.

AzerothCore and data:

- Economy guardrails, auction/mail/trade correctness, and PvP reliability.
- Content and database fixes that support believable world activity.

**Exit signal:** The server feels inhabited and useful even with one real
player online.

### Phase 5: Selected endgame and raids

**Purpose:** Deliver reliable progression through the content Caincraft values.

PlayerBots AI:

- Reusable raid primitives: stack, spread, positions, adds, interrupts, phases,
  movement hazards, and wipe recovery.
- Encounter support in the progression order actually played on Caincraft.
- Gearing, consumable, composition, and attunement behaviour aligned to the
  server's progression rules.

AzerothCore and data:

- Fix boss scripts, spells, vehicles, and encounter data found by real runs.
- Upstream generic fixes when appropriate and maintainable.

**Exit signal:** Chosen endgame content is repeatable and fun with bots.

## Priority Framework

Prioritize an issue when it is:

1. A basic-play blocker.
2. Frequent or highly visible.
3. Narrow enough to test and safely roll back.
4. Useful to more than one class, zone, or system.
5. Supported by a reproducible observation.

Deprioritize it when it is a speculative rewrite, a cosmetic improvement with
little player impact, or a solution that obscures an unresolved core defect.

## Success Measures

Use a lightweight record rather than intuition alone.

| Measure | Why it matters |
| --- | --- |
| Bot idle/stuck incidents per hour | Directly measures basic reliability. |
| Recovery success rate | Shows whether bots can handle normal game friction. |
| Commands required per dungeon | Measures how much babysitting remains. |
| Wipes or resets caused by bot behaviour | Identifies group/encounter gaps. |
| Quest completion without intervention | Measures journey reliability. |
| Player enjoyment notes | Prevents optimizing only what is easy to count. |

## Standard Improvement Loop

1. Observe a real player-facing failure.
2. Reproduce it with the smallest possible scenario.
3. Decide whether PlayerBots, core/data, or both own the problem.
4. Create a focused note in `doc/caincraft`.
5. Implement the smallest correct change.
6. Test the affected scenario and nearby regressions.
7. Record the result, then promote the next highest-value observation.

## Early Backlog

1. Low-level Hunter melee and Raptor Strike fallback.
2. Levels 1-10 class-action audit for every class.
3. Ammo, consumable, equipment, and pet readiness audit.
4. Stuck and failed-objective recovery investigation.
5. Bot decision trace suitable for diagnosing player-visible failures.
6. Post-wipe, resurrection, summon, and regroup reliability.
7. One complete leveling-dungeon reliability pass.
8. Consistent labels for PlayerBots, core, database/data, and configuration
   ownership.

## Things We Deliberately Avoid Early

- Rewriting the PlayerBots engine.
- Broad core merges without a compatibility and test plan.
- Treating every hard problem as a teleport or cheat problem.
- Pursuing universal raid intelligence before ordinary parties are reliable.
- Adding complex AI systems before behaviour is observable and testable.

## Iteration Log

| Date | Decision or lesson | Impact |
| --- | --- | --- |
| 2026-07-22 | Established the two-workstream approach and reliability-first roadmap. | Initial version. |
| 2026-07-22 | Identified low-level Hunter melee fallback as the first focused PlayerBots improvement. | Added linked implementation note. |
