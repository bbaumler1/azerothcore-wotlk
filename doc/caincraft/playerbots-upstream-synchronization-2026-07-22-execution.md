# PlayerBots Upstream Synchronization — Execution Record

Date: 2026-07-22`nCaincraft version: 0.2.0 (pending publication)
Status: completed locally and validated

## Result

Caincraft now runs the current compatible official PlayerBots pair:

- Core: `bf25eae704f5fa2cb6c9fc2458f96ec099c03a8e`
- Module: `3fa1c1e49f8f1324b72461e576bce7c89b0a6521`
- Local integration commit: `f0468163ec934c4535350b17bf4cd915e32312ad`
- Integration branches: `caincraft/playerbot-sync-20260722` in both the
  core and independent module checkout.

The original branches and uncommitted work were retained in backup branches
and Git stashes before integration. The two PvP/deserter conflicts were
resolved by retaining upstream's equivalent `Player::IsDeserter()` API.

## Validation performed

- Regenerated the Visual Studio solution with all configured static modules.
- Built `worldserver` twice in `RelWithDebInfo`; both builds succeeded.
- Preserved the existing Eluna-link and Windows resource-compiler workarounds;
  both participated successfully in the build.
- Took timestamped logical backups of `acore_auth`, `acore_characters`, and
  `acore_world` before launch.
- Started the upgraded server and applied all pending official updates:
  5 auth, 2 character, and the full pending world-update batch.
- Performed a second start-to-ready smoke test. The server reported ready,
  initialized 2,112 PlayerBots, had zero missing-config warnings, and had no
  fatal/error log matches.

## Runtime configuration reconciliation

The old runtime configuration was missing 10 PlayerBots and 22 core default
keys introduced since the last sync. Timestamped copies were made, then only
the absent upstream defaults were appended. Existing Caincraft values were not
changed. The active configuration now has zero missing keys relative to both
new `.conf.dist` templates.

## Publication

- Core branch published: `bbaumler1/azerothcore-wotlk` /
  `caincraft/playerbot-sync-20260722`.
- Matching module branch published: `bbaumler1/mod-playerbots` /
  `caincraft/playerbot-sync-20260722`.
- The module checkout now uses the personal fork as `origin` and the official
  `mod-playerbots/mod-playerbots` repository as `upstream`.
## Known follow-up items

- The user-facing in-game Hunter melee validation remains a separate,
  deliberately scoped Caincraft change; see
  [the Hunter melee fallback guide](playerbots-low-level-hunter-melee-fallback.md).
- The `condition`-table spell IDs and one SmartAI warning seen at startup
  predate this synchronization. They are a future data-quality investigation,
  not a PlayerBots startup failure.
