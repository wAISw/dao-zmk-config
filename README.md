# ZMK Firmware for Dao56

The `main-56` branch configures a 56-key split Dao keyboard based on Dao44,
with an additional number row on GPIO P0.26. The left half is the central;
the right half is a Bluetooth peripheral.

## Firmware builds

GitHub Actions builds these images on every push and pull request:

| Image | Purpose |
| --- | --- |
| `dao_left.uf2` | Left half without ZMK Studio |
| `dao_right.uf2` | Right half |
| `dao_left_studio.uf2` | Left half with ZMK Studio over USB |
| `settings_reset.uf2` | Clear saved settings and Bluetooth pairings |

Download the `firmware` artifact from the completed Actions run. For Studio,
flash `dao_left_studio.uf2` on the left and `dao_right.uf2` on the right.
Connect the left half by USB and open [ZMK Studio](https://zmk.studio/).
Hold Lower and Raise to enter ADJ, then press the key immediately left of
Backspace (the `[` key on the base layer) to unlock Studio.

## Configuration

- `config/dao.keymap`: four layers, each with 56 bindings.
- `config/dao56.dtsi`: fifth matrix row, transforms, physical layout, and battery
  divider calibration for Dao56.
- `config/dao.conf` and `config/dao_right.conf`: keyboard names, radio power,
  and idle/sleep timeouts (30/60 minutes).
- `boards/dao/dao/`: local Dao board module using Zephyr Hardware Model V2.
- `zephyr/module.yml`: registers the local board module.
- `config/west.yml`: pinned ZMK revision and its dependency manifest.
- `build.yaml`: firmware build matrix.

ZMK and its reusable GitHub Actions workflow use the same pinned commit.
The Dao board definitions are maintained locally because the original external
module uses the older hardware model. See the board directory for provenance.

## Local build

Install the [ZMK local toolchain](https://zmk.dev/docs/development/local-toolchain/setup/native),
including West and Zephyr SDK **0.16.9**, matching the CI image. Newer SDK
versions can have incompatible Picolibc headers. Use a separate West workspace:

```sh
export DAO_CONFIG=/absolute/path/to/dao-zmk-config
export ZEPHYR_SDK_INSTALL_DIR=/absolute/path/to/zephyr-sdk-0.16.9
mkdir dao56-build
cd dao56-build
cp -R "$DAO_CONFIG/config" config
west init -l config
west update
west zephyr-export

west build -s zmk/app -d build/dao_left -b dao_left//zmk -- \
  -DZMK_CONFIG="$DAO_CONFIG/config" -DZMK_EXTRA_MODULES="$DAO_CONFIG"
west build -s zmk/app -d build/dao_right -b dao_right//zmk -- \
  -DZMK_CONFIG="$DAO_CONFIG/config" -DZMK_EXTRA_MODULES="$DAO_CONFIG"
west build -s zmk/app -d build/dao_left_studio -b dao_left//zmk \
  -S studio-rpc-usb-uart -- -DCONFIG_ZMK_STUDIO=y \
  -DZMK_CONFIG="$DAO_CONFIG/config" -DZMK_EXTRA_MODULES="$DAO_CONFIG"
west build -s zmk/app -d build/settings_reset -b nice_nano@2.0.0//zmk -- \
  -DSHIELD=settings_reset -DZMK_CONFIG="$DAO_CONFIG/config" \
  -DZMK_EXTRA_MODULES="$DAO_CONFIG"
```

Firmware is written to each build directory's `zephyr/zmk.uf2`.

## Flashing

1. Turn off the selected half and connect it to the PC by USB-C.
2. Press RESET twice to enter UF2 bootloader mode.
3. Copy the corresponding UF2 image to the new USB drive.
4. Disconnect USB and repeat for the other half.

To pair the halves, turn both off, turn both on, then press RESET once on
both halves simultaneously.

`settings_reset.uf2` clears saved settings. After using it, flash the normal
firmware again and pair the halves and host again.
