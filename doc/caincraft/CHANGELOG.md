# Caincraft Changelog

All notable Caincraft PlayerBots, AzerothCore, configuration, and content work
is recorded here. See the
[Change Delivery Process](caincraft-change-delivery-process.md) for the
required workflow and versioning policy.

This changelog uses semantic-style versions: `MAJOR.MINOR.PATCH`.

## Unreleased

## 0.2.1 - 2026-07-22

### Fixed

- Added low-level Hunter close-range fallbacks: Raptor Strike when usable and
  normal melee otherwise, after existing trap/Mongoose Bite/Wing Clip actions.

### Verified

- Built and startup-smoke-tested the PlayerBots module; in-game Hunter combat
  acceptance scenarios remain pending.

### Documentation

- Established Caincraft's PlayerBots and AzerothCore guiding principles,
  delivery roadmap, and bite-sized change process.
- Added an implementation guide for the low-level Hunter melee/Raptor Strike
  fallback investigation.

## 0.2.0 - 2026-07-22

### Changed

- Synchronized Caincraft to the current compatible official PlayerBots core
  and module, then applied and validated the associated database updates.
- Reconciled newly introduced core and PlayerBots configuration defaults while
  preserving existing Caincraft values.

### Verified

- Built `worldserver` successfully in `RelWithDebInfo` and completed a
  start-to-ready smoke test with 2,112 PlayerBots initialized.

### Published

- Published matching `caincraft/playerbot-sync-20260722` branches to the Caincraft
  core and PlayerBots module forks.

## 0.1.0 - 2026-07-22

### Added

- Began the versioned Caincraft design and implementation record.
