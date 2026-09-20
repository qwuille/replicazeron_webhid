# Replicazeron WebHID protocol

This document describes the compatibility contract used by `index.html`. It is
not the Vial protocol and is not intended for unrelated QMK devices.

Layout-profile keymaps use the standard VIA dynamic-keymap commands on the
same Raw HID interface: command `0x04` reads one keycode and command `0x05`
writes one keycode. Their payload is layout, row, column, and a big-endian
16-bit QMK keycode. The custom `0x70` protocol continues to carry the profile
title and joystick mode.

## Transport and identity

- Runtime USB VID:PID: `4142:2305`
- HID usage page: `0xFF60`
- HID usage: `0x0061`
- Input/output report payload: 32 bytes
- Command marker in byte 0: `0x70`

The HID report ID is discovered from the selected device's descriptor instead
of being hardcoded. Multi-byte integers in the custom payload are big-endian
unless a field below says otherwise.

## Common packet

| Byte | Meaning |
|---:|---|
| 0 | Command marker `0x70` |
| 1 | Operation |
| 2 | Layout index for layout-specific operations; otherwise zero |
| 3-31 | Operation payload, zero-filled when unused |

Successful replies echo the command marker, operation, and layout index. The
page accepts only the matching pending reply and uses a timeout to reject a
non-responsive or incompatible device.

## Operations

| Value | Operation | Payload or reply |
|---:|---|---|
| 0 | Title get | Reply bytes 3-15: UTF-8 title, NUL padded |
| 1 | Title set | Request bytes 3-15: UTF-8 title, maximum 13 bytes |
| 2 | Deadzone get | Reply bytes 3-4: unsigned 16-bit value, high byte first |
| 3 | Deadzone set | Request bytes 3-4: unsigned 16-bit value, high byte first |
| 4 | RGB get | Reply uses the RGB settings layout below |
| 5 | RGB set | Request uses the RGB settings layout below |
| 6 | Axis filter get | Reply byte 3: filter strength |
| 7 | Axis filter set | Request byte 3: filter strength |
| 8 | Joystick mode get | Reply byte 3: mode for layout 0-9 |
| 9 | Joystick mode set | Request byte 3: mode for layout 0-9 |
| 10 | Side indicators get | Reply uses the side-indicator layout below |
| 11 | Side indicators set | Request uses the side-indicator layout below |
| 12 | Bootloader request | Guarded reset request described below |

Joystick mode values are `0 = Joystick`, `1 = WASD`, and
`2 = WASD + Shift`. Layout 10 is the fixed Settings layer and does not have an
editable title or joystick mode.

## RGB settings payload

| Byte | Meaning |
|---:|---|
| 3 | Firmware lighting enabled: 0/1 |
| 4 | Static mode: 0 = animated, 1 = static |
| 5 | Firmware animation index |
| 6 | Brightness, 0-255 |
| 7 | Speed index, 0-3 |
| 8 | Hue, 0-255 |
| 9 | Controller owner: 0 = firmware, 1 = OpenRGB |
| 10 | Addressable-strip pixel count, 1-32 |

## Side-indicator settings payload

| Byte | Meaning |
|---:|---|
| 3 | Both indicators enabled: 0/1 |
| 4 | Shared maximum brightness, 34-255 |
| 5 | Wiring polarity: 0 = active high, 1 = active low |
| 6 | Left indicator source |
| 7 | Right indicator source |

Source values are `0 = Off`, `1 = Stick strength`, `2 = Buttons held`,
`3 = Combined activity`, `4 = Always on`, `5 = Caps Lock`, `6 = Num Lock`,
and `7 = Scroll Lock`. The side indicators are firmware-controlled GPIO LEDs
and are not exposed as VialRGB/OpenRGB endpoints.

Both physical indicators are on the assembled controller's right side. The
left of that pair is firmware LED A/PB13; the right is LED B/PB12.

## Bootloader request

Operation 12 is accepted only with the guard bytes `44 46 55 21` (ASCII
`DFU!`) in request bytes 3-6. Byte 7 selects reset behavior:

- `0`: standard software reset
- `1`: PA12 USB reconnect assist followed by reset

A successful runtime reply returns `1` in byte 3 before the device disconnects.
The page then uses WebUSB—not WebHID—to connect to STM32duino DFU device
`1EAF:0003`, alternate interface 2.

## Compatibility policy

Firmware and WebHID changes that alter an existing field must be coordinated.
New fields may use previously unused bytes while retaining old behavior. A
future incompatible packet layout should introduce an explicit protocol-version
operation or a new command marker instead of silently reinterpreting fields.

## Layout profile files

Layout profiles are external JSON documents rather than a new firmware storage
format. Format version 1 contains a title, joystick mode, and 30 row-major QMK
keycodes for the 6x5 matrix. Array index 26 is `null` because that physical key
is the firmware-owned layout selector; installation preserves the destination
slot's existing value at that position.

WebHID snapshots the destination slot before installation, writes and reads
back all profile fields, and restores the snapshot if installation or
verification fails. The fixed Settings layer is never a valid destination.
