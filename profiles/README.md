# Contributed layout profiles

The WebHID **Contributed** tab reads [`index.json`](index.json) and installs
the listed layout profiles into a user-selected playable slot.

To contribute:

1. Connect a Replicazeron in WebHID.
2. Use the down-arrow button beside a layout to export its profile.
3. Fill in the exported `name`, `author`, `game`, `platform`, and
   `description` fields.
4. Add the JSON file under `profiles/<game>/`.
5. Add its summary and relative file path to `index.json`.
6. Submit both changes in a pull request.

Catalogue entries use this shape:

```json
{
  "name": "Example game layout",
  "game": "Example Game",
  "platform": "PC",
  "author": "GitHub username",
  "description": "Short explanation of the mapping.",
  "file": "example-game/default.replicazeron.json"
}
```

Profiles must match [`profile.schema.json`](profile.schema.json). The
`keycodes` array is row-major for the 6x5 matrix. Position 27 (array index 26)
is the firmware-owned layout selector and must be `null`; WebHID always
preserves the destination slot's value there.

Profiles containing bootloader, reboot, EEPROM-clear, or firmware-build
keycodes are rejected by WebHID. Profiles are reviewed before they are added
to the catalogue.
