# Contributed layout profiles

The WebHID **Contributed** tab reads [`index.json`](index.json) and previews
listed layout or macro profiles before installing them into a user-selected
zero-based slot.

To contribute, use the contribution button beside a layout or macro in
WebHID. Enter the required profile name, author, and description, then
authenticate to GitHub. WebHID performs all repository operations: it
validates the profile, creates a contribution branch, adds both the profile
and catalogue entry, and opens a pull request. Do not add contribution files
or catalogue entries manually.

Catalogue entries use this shape:

```json
{
  "name": "Example game layout",
  "type": "layout",
  "author": "GitHub username",
  "description": "Short explanation of the mapping.",
  "file": "community/example-layout.replicazeron.json"
}
```

Layout profiles must match [`profile.schema.json`](profile.schema.json), and
macro profiles must match [`macro.schema.json`](macro.schema.json). The layout
`keycodes` array is row-major for the 6x5 matrix. Position 27 (array index 26)
is the firmware-owned layout selector and must be `null`; WebHID always
preserves the destination slot's value there.

Profiles containing bootloader, reboot, EEPROM-clear, or firmware-build
keycodes are rejected by WebHID. Profiles are reviewed before they are added
to the catalogue.
