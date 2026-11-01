# Sources and provenance

Every board value in this repository traces to one of these sources.
Anything not listed here still has to be measured on this project's own
device (an unlocked Xiaomi 13 owned by the project owner).

## Upstream (pinned)

| Source | Revision | Used for |
| --- | --- | --- |
| UniXNU/UniXNU | 5e839fb26be0332a521e870b6bf0bcdf82520409 (audited 2026-11-01) | Build driver, template, OS profile ipados-17.7.11-21H461, components catalog |
| UniXNU/UniXNU-sm8550-common | a9f45dc60c035084b95463781538380f4c3606c9 (feeds.toml pin; same as sheng) | sm8550.dtsi, sm8550.config, SoC XNU patches, Sm8550Platform kit |
| UniXNU/UniXNU-sheng | 28b30285768e04008ac525c04182e245bfac441e | Reference integration only: DTS cluster split, firmware package list shape |
| mouse233/uefi-fuxi | 32b3b57e4bb542136e1a932062e5b10c81b5f287 (branch feat/ramdisk-test, pinned 2026-11-01) | Mu-Silicium UEFI firmware (fuxiPkg, USB RAM receiver), stock fuxi DTB (Resources/DTBs/fuxi.dtb), mkbootimg; FD base/size from fuxi's own xbl_config per Resources/Configs/fuxi.toml |

## mouse233/unixnu-fuxi @ 4545692515a54d8bfc94597a7ba72fd9b25bead5

The only fuxi port with device-verified evidence (its porting record:
configuration implemented; firmware/packaging host-verified; memory, storage
and diagnostics device-verified; display/input/GPU/network/audio/power still
planned there too).

Borrowed, with the file/field named:

| Value | Where it lands here | Evidence in source |
| --- | --- | --- |
| IPSW identity iPad7,1 / 17.7.11 / 21H461 / j120ap / chip 0x8011 / board 12 | OS profile (already in upstream profile) | board.toml [ipsw] |
| Firmware config `fuxi`, DSC `Platforms/Xiaomi/fuxiPkg/fuxi.dsc`, FD path, Mu submodules, Gic-Spurious patch, package list | board.toml [firmware] | board.toml [firmware] |
| Payload volume `FUXIESP`, 192 MiB, panic_screen | board.toml [payload] | board.toml [payload] |
| ADT owned_memory_size 0x130000000, chip_id 207 | board.toml [payload.adt] | board.toml [payload.adt] |
| DRAM/SMEM size 0x80000000 | dts memory reg, board dram reference | board.toml dram_size |
| Splash region 0xb8000000 / 0x2b00000 already in fuxi stock DT; do NOT add a bootimg dtb_node | dts/fuxi.dts, board.toml [bootimg] note | board.toml bootimg comment |
| Root volume UUID 04769D26-2CA0-4825-B443-E2E5F88300F2 | Not yet used; recorded for the storage stage | board.toml [payload.adt] |
| make-fuxi-adt.py approach (cpu_regs 0..0x700 step 0x100) | DTS CPU nodes come from sm8550.dtsi with same regs | board.toml [payload.adt] |

Borrowed on 2026-11-01: mouse233's `[userdata]` geometry (first_lba
0x2F7840, last_lba 0x3AFC3FA, 4096-byte LBA, ~240.6 GB), after the project
owner confirmed their device is the same 12 GB + 256 GB specification.
Still pending: verify against the owner's own device GPT at the storage
stage before any userdata write.

Also not borrowed: its kit name (`sheng`), its empty Format 2 component
selection, and its board-script build path. This repo uses kit `fuxi`, the
catalog component IDs, and the layered driver.

## Reached on mouse233's device (context, not this project's result)

- v36: native SystemOS root authentication (auth=80)
- v37: root pivot OK (md0 -> APFS), /sbin/launchd SHA-256 verified
- v41: shared region mapped, several userland processes running at EL0
- v43: first natural XNU stop, watchdogd critical-exit panic
- Not reached there either: home screen, display presentation, touch,
  GPU, Wi-Fi, audio, power transitions; receiver shutdown unverified
  for XNU handoff attempts
