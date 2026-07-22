# Caincraft Change Delivery Process

This is the required delivery process for PlayerBots, AzerothCore,
configuration, database, and content changes. It keeps Caincraft work safe,
reviewable, and easy to learn from as the project grows.

See the [Guiding Light](playerbots-single-player-guiding-light.md) for the
project vision and roadmap.

## 1. Write Before Implementing

Before changing code, configuration, or data, create or update a focused guide
in `doc/caincraft`.

Each guide must include:

- the player-facing problem and reproducible scenario;
- the confirmed or suspected root cause;
- the responsible layer: PlayerBots, core/data, configuration, or more than
  one layer;
- exact files and behavioural changes in scope;
- explicit non-goals;
- verification scenarios and completion criteria; and
- risk and rollback notes.

The guide is the agreement on what will be changed. If investigation changes
the design materially, update the guide before continuing implementation.

## 2. Keep Changes Bite-Sized

One change solves one player-facing problem or one tightly coupled, testable
behaviour. Do not mix unrelated refactors, cleanup, broad tuning, or multiple
features into the same change.

A good Caincraft change is:

- small enough to explain in a short summary;
- narrow enough to test with a named scenario;
- isolated enough to revert cleanly; and
- useful enough that the player can notice the difference.

If a proposal contains several independent behaviours, create separate guides
and deliver them one at a time.

## 3. Implement and Verify

Implement only what the guide describes. Record the rebuild, restart,
configuration, and migration requirements. Perform the specified in-game
checks plus relevant regression checks.

Compilation is necessary but not sufficient. A PlayerBots change is complete
only when its intended player-facing behaviour has been observed in-game, or
when the guide clearly records why that validation remains pending.

## 4. Version and Changelog

Maintain `doc/caincraft/CHANGELOG.md`. Every delivered change updates its
**Unreleased** section before it is committed or pushed.

Caincraft uses semantic-style versions: `MAJOR.MINOR.PATCH`.

| Version part | Increment when... |
| --- | --- |
| `MAJOR` | An incompatible server, database, configuration, or progression change requires a migration or changes established player expectations. |
| `MINOR` | A meaningful, backward-compatible bot, core, content, or experience improvement is delivered. |
| `PATCH` | A focused bug fix, tuning correction, documentation correction, or small compatibility fix is delivered. |

Each changelog entry should state the player-facing result, affected
workstream, verification status, and a link to the implementation guide where
useful.

## 5. Commit and GitHub Handoff

After verification:

1. Review the diff and confirm it contains only the intended bite-sized change.
2. Commit with a clear Conventional Commit message.
3. Update the Caincraft changelog version or Unreleased entry.
4. Push the completed change to the Caincraft GitHub fork of the relevant
   repository.
5. Keep the guide with the repository so the decision and test record travel
   with the code.

Investigation and drafting do not imply a remote push. Push only after the
change is reviewed, validated, and explicitly ready for handoff.

## Definition of Done

A Caincraft change is done when all of the following are true:

- A guide exists and matches the delivered implementation.
- The diff is intentionally scoped and reviewable.
- Required rebuild, migration, and configuration steps are documented.
- Specified verification and regression checks have been recorded.
- The Caincraft changelog has been updated.
- The verified commit has been pushed to the appropriate Caincraft GitHub fork.
