# companion-module-lightware-lw3

See HELP.md and LICENSE

# Version History

## v2.1.0

Changes since **v2.0.5**, the last version published in the Companion module repository.
v2.0.6 was tagged but never published, so the one fix it carried is repeated below.

Developed and tested against an MX2-16x16-HDMI20-R, a device none of the previous releases
could drive at all.

### Requirements

- **Companion 5.0 or newer.** The module moves to module API 2.1, which earlier Companion
  releases cannot load. Stay on v2.0.5 if you are on Companion 4.x or older.
- An upgrade script runs automatically and moves existing Output Lock buttons to the new
  named port dropdown. No other buttons are affected.

### The main change: the device is asked, not guessed

Earlier releases decided what a device could do from its product name, matched against a
hard coded list of model patterns. Anything not on that list, or any firmware that named
things differently, ended up partly or entirely unusable — and the failure was silent.

The module now queries the device and adapts to what it finds. Four things are detected
separately, so one unsupported path no longer disables the rest:

- **Crosspoint node** — `/MEDIA/XP/VIDEO` or `/MEDIA/VIDEO/XP`
- **Routing property** — `DestinationConnectionList` or `DestinationConnectionStatus`,
  whichever the node actually exposes
- **Port names** — `/MEDIA/VIDEO/*.Text` or `/MEDIA/NAMES/VIDEO.*`
- **Presets** — `/PRESETS/AVC` or `/MEDIA/PRESET`

Lock, USB switching and macro actions appear only when the device reports the matching
methods, instead of being offered unconditionally.

### Added

- **Input Lock** action, alongside the existing Output Lock
- **Toggle** option on both lock actions, resolved from the state the device reports rather
  than from a locally remembered value, so locking elsewhere stays in sync
- **Feedbacks** for the lock state of an input or an output
- **Buttons for the presets stored on the device**, under "Recall Preset"
- **"Log protocol traffic"** setting, which writes the raw LW3 exchange to the log
- Lock actions and feedbacks pick their port from a named dropdown instead of a port number

### Fixed

- Config used the field type `text`, which current Companion releases reject outright with
  "Unsupported field type text", making the connection unusable (fix originally tagged as
  v2.0.6, never published)
- Actions carrying device supplied dropdowns were registered before their choices existed
  and were then discarded by Companion, so most of the module's actions never appeared in
  the actions list at all ([#41](https://github.com/bitfocus/companion-module-lightware-lw3/issues/41))
- Firmware reporting `DestinationConnectionStatus` instead of `DestinationConnectionList`
  left the module without any routing information
  ([#15](https://github.com/bitfocus/companion-module-lightware-lw3/issues/15))
- Unrecognised devices were assigned the wrong crosspoint path style, which left them
  unusable ([#45](https://github.com/bitfocus/companion-module-lightware-lw3/issues/45))
- Port names and presets were only read when crosspoint detection succeeded, so a single
  unsupported path silently disabled sources, destinations and presets together
- Error detection never triggered at all — the check read an always empty string — and did
  not know the `-E` prefix, so failed requests were parsed as if they were data
- The response parser was not reset when the helper reconnected: a connection lost in the
  middle of a multi line response left it waiting for a closing brace and swallowing every
  later reply
- Rename notifications were requested with a wildcard the device rejects, so renaming a
  port on the device never reached Companion
- Renaming a preset on the device corrupted the preset list instead of updating the entry
- An output with nothing routed to it, reported as `0`, was treated as a malformed
  connection status
- Trailing spaces in port names reported by the device are trimmed
- A command that could not be sent left its response handler behind forever

### Internal

- Rewritten for `@companion-module/base` v2 on the node22 runtime, split from a single
  `index.js` into `src/`
- Tooling moved to `@companion-module/tools` v3, Yarn 4 and Prettier

### Known limitations

- Two lock _toggles_ for the same port pressed in quick succession can act on stale state,
  because the device's confirmation is still in flight
- Recalling a preset and recovery after a real network drop are verified against a protocol
  simulator, not yet against hardware

## v2.0.6 (2025-04-21)

Tagged but never published to the Companion module repository.

- Change type for the Info field in config settings

## v2.0.5 (2024-03-22)

- Bugfix: preset and crosspoint handling
- Dependency updates

## v2.0.4 (2023-06-26)

- Feat: added soft reset action

## v2.0.3 (2023-06-08)

- Bugfix: fixed readback of the internal matrix presets for MX2. This bug was preventing the module to load with MX2.
- Bugfix: fixed feedback 'route' for MX2

## v2.0.2 (2023-05-26)

- Bugfix: make actions working again
- Bugfix: make feedbacks working again
- Bugfix: make presets morking again
- Bugfix: make special MX actions working again
- Bugfix: show Macro action even if currently no macros are available
- Bugfix: don't throw error when feedbacks are checked for a larger matrix then currently connected
- Bugfix: removed lodash

## v2.0.1 (2023-05-14)

- Added lodash

## v2.0.0 (2023-05-14)

- Major: rewrite for Companion v3 compatibility
- Known Bugs: nothing is working, except the route crosspoint action

## v1.1.0 (2022-12-04)

- Feat: added actions for selecting and routing inputs and outputs like on a X/Y panel
- Feat: added output lock action
- Feat: added load preset action
- Feat: added run macro action
- Feat: added switch USB host action for MX2-8x8-USB
- Feat: added feedbacks for crosspoint status
- Feat: added variables for input and output names
- Feat: refactor code to ES6 format

## v1.0.2 (2022-02-02)

- Brush: replaced system.emit calls

## v1.0.1 (2020-03-12)

- Change: changed module name from lightware3 to lightware-lw3

## v1.0.0 (2018-07-03)

- Initial release
