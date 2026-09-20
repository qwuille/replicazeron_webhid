# Replicazeron WebHID Control Deck

A standalone browser configurator and firmware updater for
[Qwuille's Replicazeron Vial/QMK firmware](https://github.com/qwuille/vial-qmk/tree/vial).

The application is a self-contained static page. It communicates directly with
the controller through WebHID and with its STM32duino bootloader through
WebUSB. Device settings and firmware files are processed locally in the
browser; this project has no application server or telemetry.

## Open the configurator

The hosted version will be available at:

**https://qwuille.github.io/replicazeron_webhid/**

Use current Chrome or Edge on a desktop computer. The page must be served over
HTTPS or localhost, and the browser will ask you to explicitly grant access to
the controller and bootloader.

## Features

- Reads all supported settings immediately after connecting.
- Edits ten layout titles and their independent Joystick, WASD, or WASD + Shift
  modes. The reserved Settings layer cannot be renamed.
- Configures joystick deadzone and axis filtering.
- Selects Firmware or OpenRGB lighting ownership.
- Configures firmware animation, brightness, speed, hue, and a 1-32 pixel
  addressable-strip length; the default is 11.
- Assigns sources, brightness, enable state, and active-high/active-low wiring
  for the adjacent left and right side indicators.
- Imports and exports configuration as JSON.
- Validates and installs an STM32F103 `.bin` through WebUSB DFU.
- Offers standard reset and a PA12 USB reconnect assist for affected Blue Pill
  clones.

## Required firmware

This page is not a generic QMK or Vial configurator. It requires the matching
Replicazeron custom HID protocol in Qwuille's firmware:

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

Serve the repository instead of opening `index.html` directly:

```sh
python -m http.server 8000
```

Then open `http://localhost:8000`. Localhost is treated as a secure browser
context for WebHID and WebUSB development.

No package installation or build step is required. `index.html` contains the
HTML, CSS, and JavaScript used by the published site.

## Credits and scope

The WebHID Control Deck is maintained by
[Kristian Ljungkvist (Qwuille)](https://github.com/qwuille). The firmware
follows earlier Replicazeron firmware work by
[9R](https://github.com/9R/qmk_firmware) and
[Incedius](https://github.com/incedius/vial-qmk), and uses Vial/QMK.

These are software and project-lineage credits. This repository does not claim
that its maintainers designed the original commercial hardware that inspired
the community project, and it does not distribute printable models.

## License

Copyright (c) 2026 Kristian Ljungkvist (Qwuille).

Released under the [MIT License](LICENSE). Copies and substantial portions must
retain the copyright and license notice. See [NOTICE](NOTICE) for the concise
authorship and upstream acknowledgement.
