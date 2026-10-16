# Dao board module

Based on the Dao board definitions from
[ergonautkb-zmk-module](https://github.com/ergonautkb/ergonautkb-zmk-module/tree/0baf6fb036595f4c16bde895b8804123e6b4cf0d/boards/arm/dao),
with the original MIT license and copyright notices preserved.

The definitions are maintained locally to support Zephyr Hardware Model V2.
Build targets are `dao_left//zmk` and `dao_right//zmk`.

The common hardware definitions describe Dao44. The user configuration adds
Dao56's fifth matrix row, key positions, and physical layout in
`config/dao56.dtsi`.
