# Replicazeron WebHID Control Deck

A standalone browser configurator and firmware updater for
[the Replicazeron Vial/QMK firmware](https://github.com/qwuille/vial-qmk/tree/vial).

The application is a self-contained static page. It communicates directly with
the controller through WebHID and with the STM32duino or RP2040 ROM bootloader
through WebUSB. Device settings and firmware files are processed locally in
the browser; this project has no application server or telemetry.

## Open the configurator

The hosted version will be available at:

**https://qwuille.github.io/replicazeron_webhid/**

Use current Chrome or Edge on a desktop computer. Open the hosted HTTPS page or
open the downloaded `index.html` directly; the browser will ask you to
explicitly grant access to the controller and bootloader.

## Features

- Reads all supported settings immediately after connecting.
- Labels the ten playable layers as 1-10 and the fixed Settings tool layer as
  `Settings L11`; protocol and Vial storage indices remain zero-based.
- Edits ten layout titles and their independent stick modes. Blue Pill Standard
  offers WASD and Faux-analog WASD; Blue Pill DirectInput additionally offers
  DirectInput joystick. RP2040 offers those modes plus XInput + keys. Compact
  visual selectors show the current CSS joystick/WASD and OLED
  designs even while closed; their option panels overlay the page instead of
  expanding the layout row. The reserved Settings layer cannot be renamed.
- Keeps only the Settings-layer five-way D-pad protected in profile transfers.
  Every other Settings key can carry a modifier, mouse button, shortcut, or
  ordinary key. Middle-button drag works as pan in Fusion, FreeCAD CAD
  navigation, and Onshape; a separately mapped Shift key changes it to Fusion
  orbit, FreeCAD can combine it with mapped left/right mouse buttons, and
  Onshape can use the right-button-drag stick action for orbit.
- Exports or imports any playable slot, or the editable Settings keys, as a
  layout profile. Preview uses the actual 30-key Replicazeron geometry from
  Vial, rendered as an image-free CSS device map. A WebHID translation layer
  turns standard QMK, layer, macro, mouse, lighting, and Replicazeron keycode
  numbers into readable key names. OLED navigation and the layout selector
  stay protected.
- Names all 16 zero-based macro slots and exports or imports each macro as an
  independent profile that can be previewed and installed into any slot.
- Provides a visual macro editor with physical-key recording, manual key
  actions, editable per-action delays, reordering, and a firmware-side fixed
  or randomized automatic gap between every key action. A clickable miniature
  CSS keyboard makes familiar letter, number, modifier, and navigation keys
  quick to add without image assets.
- Previews contributed and imported macros as a graphical action timeline,
  including key-down, key-up, tap, text, explicit-delay blocks, and fixed or
  randomized automatic-delay information.
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
- Fetches reviewed community keymaps and macros into separate, expandable
  repository-folder trees and installs one
  into a user-selected slot with read-back verification and automatic rollback.
- Previews keymaps and macro content before writing and accepts local JSON or
  an external/GitHub URL. Contributions are completed inside WebHID: it
  validates name, author, description, and layout or macro data, creates the
  branch, updates the catalogue, and opens the pull request automatically.
  GitHub authentication currently uses a session-only classic access token
  with `public_repo` scope; the page never stores it.
- Configures joystick deadzone, directional axis filtering, and persistent
  analog smoothing from off through maximum.
- Configures Faux analog Walk and Run keys plus their independent
  stick-strength transitions. Either binding can be disabled for a two-speed
  setup; enabling both produces Walk/Pace/Run without a host gamepad.
- Blue Pill Standard intentionally has no USB HID/DirectInput joystick gaming
  mode. It was removed because mixed gamepad and keyboard reports caused
  unsmooth HUD and input-prompt switching in games. Its analog stick remains
  available to firmware for proportional scrolling, cursor control, and CAD
  pan/orbit.
- Detects Standard versus DirectInput STM32 firmware, hardware revision, and
  input capabilities. The flasher defaults to the detected update channel and
  provides an explicit toggle to change variants. Standard may receive new
  features; DirectInput is feature-frozen and receives maintenance fixes only.
- Configures the OLED shutdown delay and sleeping-logo interval. These values
  share unused firmware metadata bits and do not reduce macro capacity.
- Stores each playable layout's OLED design in unused bits of its existing
  firmware mode byte. OLED selection and live macro statistics therefore do
  not consume macro slots or macro-buffer bytes.
- Selects proportional page scrolling with a temporary cursor toggle,
  middle-drag pan, Shift+middle-drag orbit, or right-drag orbit behavior for the
  Settings-layer thumbstick. Firmware v0.3.2 and later suppresses the playable
  layer's WASD/Faux output while Settings is active, preventing movement letters
  from triggering CAD commands alongside the selected mouse tool.
- Selects Firmware or OpenRGB lighting ownership.
- Configures firmware animation, brightness, speed, hue, and a 1-32 pixel
  addressable-strip length; the default is 11.
- Assigns sources, brightness, enable state, and active-high/active-low wiring
  for the adjacent left and right side indicators.
- Monitors OpenRGB and Vial/WebHID traffic independently, or combines them as
  steady OpenRGB and blinking configuration activity with Host control.
- Imports and exports full-device configuration as JSON independently of the
  per-layout profile files.
- Detects the connected controller and automatically selects STM32F103 `.bin`
  WebUSB DFU or RP2040 `.uf2` WebUSB PICOBOOT flashing.
- Uses the matching asset from the latest `qwuille/vial-qmk` GitHub release.
  The Pages deployment verifies GitHub's reported size and SHA-256 digest before
  publishing the asset beside the site; the browser verifies both again and
  validates its STM32 vector table or RP2040 UF2 structure before enabling the
  flash action. Automatic detection and download are the primary path. A small
  manual-mode switch accepts a previously downloaded matching `.bin` or `.uf2`
  and links to GitHub Releases for recovery or offline preparation.
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
| Maintained controllers | STM32F103 Blue Pill-class board and RP2040 |
| Maintained QMK targets | `handwired/replicazeron/stm32f103:vial`, `handwired/replicazeron/rp2040:vial` |
| STM32 release variants | Standard and feature-frozen DirectInput compatibility |
| STM32duino DFU VID:PID | `1EAF:0003` |
| DFU alternate interface | 2 |
| Application origin | `0x08002000` |
| RP2040 BOOTSEL PICOBOOT VID:PID | `2E8A:0003` |

> [!WARNING]
> RP2040 support, including XInput and browser flashing, compiles successfully
> but has not yet been verified on physical hardware. Treat the RP2040 UF2 and
> updater as experimental, and keep manual BOOTSEL recovery available. The
> STM32F103 target is the validated release target.

See [PROTOCOL.md](PROTOCOL.md) for the WebHID packet layout and compatibility
contract.

## Firmware update safety

Only flash firmware built for the detected Replicazeron target. The page checks
the STM32 vector table or the RP2040 UF2 family and flash range, but it cannot
prove that a file matches your wiring or controller clone.

The browser updater requires a compatible STM32duino `boot20` bootloader to be
installed already. It cannot install or repair the bootloader itself. Initial
bootloader installation normally requires ST-Link/SWD or another external
programmer. Keep a command-line DFU or SWD recovery method available.

RP2040 updates use the ROM BOOTSEL PICOBOOT interface, so no separately
installed bootloader is required. Keep a manual BOOTSEL/mass-storage recovery
method available.

RP2040 firmware from before the device-target query cannot identify itself to
the page and is therefore treated as the legacy STM32 target. Install the
current UF2 once manually with BOOTSEL and the `RPI-RP2` drive. Automatic
RP2040 selection and browser flashing work after that one-time update.

## Run locally

Open `index.html` directly in current Chrome or Edge. No local web server is
required. When opened as a local file, the Contributed tab reads its catalogue
from the GitHub repository; device communication and profile files remain
local to the browser.

No package installation or build step is required. `index.html` contains the
HTML, CSS, and JavaScript used by the published site.

The GitHub Pages workflow builds the published site without committing firmware
binaries to this repository. At deployment time it downloads both assets from
the latest `qwuille/vial-qmk` release, checks their GitHub-provided sizes and
SHA-256 digests, and generates `firmware/manifest.json`. This same-origin copy
is necessary because GitHub release-asset redirects are not CORS-readable by a
static browser application.

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

The controller preview and `RZ` mark are image-free, code-generated artwork
created for this WebHID application. The application does not embed third-party
controller photographs.

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
