# Device trees for the Sony Xperia 10 VI (pdx246, columbia)

This is Sony's copyleft device tree release for the SM6450 ("parrot")
platform, as published in `kernel-copyleft-dts`, branch `70.2.A.2.xxx` --
the branch matching this device's `70.2.A.4.168` firmware line. It is used
by `kernel/sony/sm6450` through the symlink
`arch/arm64/boot/dts/vendor -> ../../../../../sm6450-devicetrees`, the same
way `kernel/sony/sm6475` uses `kernel/sony/sm6475-devicetrees`.

Sony did not rename the device trees for the commercial product: the Xperia
10 VI boots the tree Qualcomm calls "Parrot QRD", which Sony patched in
place. `parrot-qrd-overlay.dts` (board-id `0x1000B 0`, PM7250B) is the
overlay this device selects -- `ro.boot.dtbo_idx` is 25 on stock and the
kernel prints `Machine model: Qualcomm Technologies, Inc. Parrot QRD`.

## Changes against Sony's release

* `qcom/blair-touch.dtsi` and `qcom/diwali-gdsc.dtsi` were restored from
  branch `70.0.A.2.xxx`. The `70.2.A.2.xxx` tree still includes both
  (`parrot-qrd.dtsi:162` and `parrot.dtsi:3012`) but does not ship them, so
  it cannot be built as released.
* The seven techpack overlays were moved from the flat `qcom/` directory
  into `qcom/{camera,audio,display,video}/`, with a Makefile each. They
  have to sit one level deeper than the base device trees, because
  `vendor/lineage/build/tasks/kernel.mk` treats everything directly under
  `arch/arm64/boot/dts/vendor/*/` as a base device tree and
  `merge_dtbs.py` folds the rest into those. This is also how the official
  `sm6475-devicetrees` is laid out. Their `.dtsi` files stayed where Sony
  put them; the Makefiles point dtc at the parent directory, and at the
  camera and audio techpacks in `kernel/sony/sm6450-modules` for the four
  `dt-bindings` headers that live there.
* `qcom/Makefile` gained `subdir-y += audio camera display video`.

Nothing else was touched: no node, property or value was changed.

## Why this reproduces the stock images

Built and merged with `merge_dtbs.py`, these sources produce a base
`parrot.dtb` and a `parrot-qrd-overlay.dtbo` that are content-identical to
the two blobs the phone boots from stock firmware
(`XQ-ES54_EEA-user 16 70.2.A.4.168`):

| | stock | from these sources |
|---|---|---|
| `parrot.dtb` | 387,771 B, 275 compatibles, 7,787 properties | 387,759 B, 275, 7,787 |
| `parrot-qrd-overlay.dtbo` | 269,872 B, 79 compatibles, 703 nodes, 658 fragments | 271,084 B, 79, 703, 658 |

Every remaining difference is phandle numbering and fragment ordering,
which dtc allocates and the bootloader resolves. No property differs in
value.

Note that Sony Open Devices' own `parrot-columbia-pdx246_generic.dts` is
*not* this tree: it has no fingerprint and no NFC or eSE node, uses
`focaltech,fts_ts` and `willsemi,wl2868c` where the commercial tree uses
`focaltech,fts` and `will,wl2868c`, and would leave the stock vendor blobs
without touch, fingerprint, NFC and cameras.
