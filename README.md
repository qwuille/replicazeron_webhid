# Replicazeron WebHID Control Deck

A standalone browser configurator and firmware updater for
[the Replicazeron Vial/QMK firmware](https://github.com/qwuille/vial-qmk/tree/vial).

The application is a self-contained static page. It communicates directly with
the controller through WebHID and with its STM32duino bootloader through
WebUSB. Device settings and firmware files are processed locally in the
browser; this project has no application server or telemetry.

## Open the configurator

The hosted version will be available at:

**https://qwuille.github.io/replicazeron_webhid/**

Use current Chrome or Edge on a desktop computer. Open the hosted HTTPS page or
open the downloaded `index.html` directly; the browser will ask you to
explicitly grant access to the controller and bootloader.

## Features

- Reads all supported settings immediately after connecting.
- Edits ten layout titles and their independent Joystick, WASD, or WASD + Shift
  modes. The reserved Settings layer cannot be renamed.
- Exports or imports any playable slot, or the editable Settings keys, as a
  layout profile. OLED navigation and the layout selector stay protected.
- Names all 16 zero-based macro slots and exports or imports each macro as an
  independent profile that can be previewed and installed into any slot.
- Provides a visual macro editor with physical-key recording, manual key
  actions, editable per-action delays, reordering, and a firmware-side fixed
  or randomized automatic gap between every key action.
- Shows used, free, and total macro-buffer capacity before saving, and rejects
  a sequence that would overflow the controller's EEPROM allocation.
- Progressively enlarges Layouts and Macros while scrolling slowly through the
  middle band of the viewport. A quick wheel, trackpad, or touch gesture bypasses
  enlargement and continues down the page. This behavior is enabled by default,
  can be disabled persistently, and retains a manual Expand/Collapse control.
  Expanded mode is an animated overlay and no longer locks or hides the main
  page. Escape leaves it, while scrolling past the bottom moves directly to the
  firmware-update section.
- Remembers the selected My layouts, Macros, or Contributed tab across page
  reloads in the same browser.
- Fetches reviewed community keymaps and macros into separate sections from
  the repository and installs one
  into a user-selected slot with read-back verification and automatic rollback.
- Previews keymaps and macro content before writing and accepts local JSON or
  an external/GitHub URL. Contributions are completed inside WebHID: it
  validates name, author, description, and layout or macro data, creates the
  branch, updates the catalogue, and opens the pull request automatically.
  GitHub authentication currently uses a session-only classic access token
  with `public_repo` scope; the page never stores it.
- Configures joystick deadzone and axis filtering.
- Selects proportional page scrolling with a temporary cursor toggle,
  middle-drag pan, Shift+middle-drag orbit, or right-drag orbit behavior for the
  Settings-layer thumbstick.
- Selects Firmware or OpenRGB lighting ownership.
- Configures firmware animation, brightness, speed, hue, and a 1-32 pixel
  addressable-strip length; the default is 11.
- Assigns sources, brightness, enable state, and active-high/active-low wiring
  for the adjacent left and right side indicators.
- Imports and exports full-device configuration as JSON independently of the
  per-layout profile files.
- Validates and installs an STM32F103 `.bin` through WebUSB DFU.
- Offers standard reset and a PA12 USB reconnect assist for affected Blue Pill
  clones.

## Required firmware

This page is not a generic QMK or Vial configurator. It requires the matching
Replicazeron custom HID protocol in the matching firmware:

| Item | Value |
|---|---|
| Runtime USB VID:PID | `4142:2305` |
| Raw HID usage page/usage | `FF60:0061` |
| Raw HID report | 32 bytes |
| Maintained controller | STM32F103 Blue Pill-class board |
| Maintained QMK target | `handwired/replicazeron/stm32f103:vial` |
| STM32duino DFU VID:PID | `1EAF:0003` |
| DFU alternate interface | 2 |
| Application origin | `0x08002000` |

See [PROTOCOL.md](PROTOCOL.md) for the WebHID packet layout and compatibility
contract.

## Firmware update safety

Only flash a binary built for the matching STM32F103 Replicazeron target and
bootloader layout. The page checks file size and the STM32 vector table, but it
cannot prove that a binary matches your wiring or controller clone.

The browser updater requires a compatible STM32duino `boot20` bootloader to be
installed already. It cannot install or repair the bootloader itself. Initial
bootloader installation normally requires ST-Link/SWD or another external
programmer. Keep a command-line DFU or SWD recovery method available.

## Run locally

Open `index.html` directly in current Chrome or Edge. No local web server is
required. When opened as a local file, the Contributed tab reads its catalogue
from the GitHub repository; device communication and profile files remain
local to the browser.

No package installation or build step is required. `index.html` contains the
HTML, CSS, and JavaScript used by the published site.

## Future companion overlay

A later Windows/Linux companion may reuse the controller map as a compact,
always-on-top overlay with live button and stick feedback, adjustable opacity,
and click-through operation. That application should remain display-only and
avoid game-input injection. A firmware event or configurable hotkey can later
toggle its opacity. This native companion is intentionally separate from the
static configurator so the current WebHID page retains its no-install design.

The matching firmware gives Vial and WebHID configuration traffic priority
over OpenRGB lighting traffic. WebHID maintains that priority with a heartbeat;
after configuration traffic stops, OpenRGB resumes automatically and the last
lighting frame remains visible during the pause. Closing OpenRGB before a Vial
session remains the most conservative option because both still share one Raw
HID interface.

## Credits and scope

The WebHID Control Deck and firmware follow earlier Replicazeron firmware work by
[9R](https://github.com/9R/qmk_firmware) and
[Incedius](https://github.com/incedius/vial-qmk), and uses Vial/QMK.

These are software and project-lineage credits. This repository does not claim
that its maintainers designed the original commercial hardware that inspired
the community project, and it does not distribute printable models.

## License

Copyright (c) 2026 Replicazeron contributors.

Released under the [MIT License](LICENSE). Copies and substantial portions must
retain the copyright and license notice. See [NOTICE](NOTICE) for the concise
authorship and upstream acknowledgement.
