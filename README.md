# UniXNU-fuxi

Device repository for the Xiaomi 13 (fuxi, SM8550), Format 2.
Target: **iPadOS 17.7.11 (21H461)**, donor iPad7,1, through the shared
SM8550 support and the UniXNU layered build driver.

This is a clean port started 2026-11-01 from the UniXNU `examples/device`
template. It reuses board data that mouse233/unixnu-fuxi verified on a real
fuxi unit (RAM-receiver path: APFS root pivot, launchd, EL0 user processes),
but builds through `./unixnu` — not through that repository's legacy board
scripts. See `SOURCES.md` for every borrowed value and its provenance, and
`porting.toml` for the current per-area status.

## Status

M1 host resolution passed on 2026-11-01: `defconfig / fetch / status --json /
tree` all succeed, the assembled tree contains `kits/fuxi`, and the
porting-record check passes (13 areas). `unixnu.lock` is committed.
Next M1 step is the kernel build, which needs a Mac, the Linux XNU
toolchain, or a GitHub macOS runner. No boot result on this project's
own device yet. The project owner's unlocked fuxi unit is the 12 GB +
256 GB specification, so `[userdata]` adopts mouse233's geometry for the
same specification; it still gets verified against the unit's own GPT
before any userdata write.

## Build (M1)

Run from the UniXNU checkout, with this repository as `../UniXNU-fuxi`:

```sh
./unixnu defconfig fuxi_defconfig -D ../UniXNU-fuxi
./unixnu fetch
./unixnu status --json
./unixnu tree
python3 scripts/porting-record.py check --device ../UniXNU-fuxi
```

Host requirements and the Linux/macOS toolchains: see the UniXNU
[requirements](https://unixnu.com/guide/requirements) page. The kernel step
needs a Mac, the Linux XNU toolchain, or a GitHub macOS runner.

## Layout

| Path | Contents |
| --- | --- |
| `board.toml` | Board, SoC source, OS profile, firmware and payload policy |
| `feeds.toml` | Pinned SM8550 SoC source (`a9f45dc`, same pin as sheng) |
| `dts/fuxi.dts` | Board DTS over the shared `sm8550.dtsi` |
| `configs/fuxi_defconfig` | Kernel defconfig (M1 minimal set) |
| `porting.toml` | Structured porting record, rendered by unixnu.com |
| `SOURCES.md` | Provenance of every reused board value |

Firmware: `fuxiPkg` in a uefi-fuxi checkout (`--uefi`), exact revision still
to be pinned. Boot is `fastboot boot` only during bring-up; nothing in this
repository flashes or erases a partition.
