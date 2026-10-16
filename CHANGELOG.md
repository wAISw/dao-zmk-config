# Changelog

## Unreleased

### Fixed

- Migrate the Dao board definitions to Zephyr Hardware Model V2, including
  explicit nRF52840 selection, ZMK variants, DC/DC configuration, and UF2
  bootloader retention support.
- Maintain the board module locally instead of using the unmigrated external
  module. Preserve the Dao56 matrix, keymap, battery calibration, and timeouts.
- Update the settings-reset target to `nice_nano@2.0.0//zmk` and enable ZMK
  Studio only for the central left half.
- Pin ZMK and its GitHub Actions workflow to the same tested commit and replace
  the deprecated hold-tap property with `quick-tap-ms`.
