# GU603ZM-hackintosh
ROG m16 GU603ZM hackintosh EFI
# Hackintosh OpenCore EFI Archive

ROG m16 GU603ZM on macOS 26.7.3 (25G342)

---

## 1. Machine Overview

| Item | Details |
|---|---|
| Platform | Intel CPU + NVIDIA dGPU(3.0) / Intel iGPU hybrid |
| Audio | Realtek ALC285 (`10EC:0285`, enumerated via Intel SST bus, ACPI name `HDAS`) |
| Wi-Fi / Bluetooth | Intel (`itlwm` + `IntelBluetoothFirmware` / `BlueToolFixup`) |
| Input | VoodooI2C (trackpad) + VoodooPS2Controller (keyboard, incl. function-key mapping) |
| SMBIOS | MacBookPro16,4 |
| Power / S0ix | S0ixStandby / HibernationFixup / SSDT-DEEPIDLE / S0ix-related args |

---

## 2. Directory Structure

```
EFI/
├── BOOT/
│   └── BOOTx64.efi              # Boot entry
└── OC/
    ├── OpenCore.efi
    ├── config.plist             # Active configuration
    ├── ACPI/                    # SSDT / DSDT
    ├── Drivers/                 # HfsPlus / OpenRuntime / OpenCanopy / apfs_aligned etc.
    ├── Kexts/                   # Kernel extensions (see table below)
    ├── Resources/               # OpenCanopy icon resources
    ├── codec_dump.{txt,json}    # ALC285 codec node dump report (from Windows)
    ├── probe-alc285.sh          # Live audio topology probe script
    └── config*.plist            # Staged backups (see "Backups")
```

### Kexts

| Kext | Purpose |
|---|---|
| Lilu / VirtualSMC | Core patches and SMC emulation |
| WhateverGreen | iGPU / dGPU drivers and framebuffer patches |
| AppleALC | Audio (layout-id = 33) |
| itlwm / AirportItlwm(.disabled) | Intel Wi-Fi |
| IntelBluetoothFirmware / BlueToolFixup / IntelBTPatcher | Intel Bluetooth |
| VoodooI2C / VoodooI2CHID | Trackpad |
| VoodooPS2Controller / BrightnessKeys | Keyboard / brightness hotkeys |
| AsusSMC | ASUS-specific sensors / hotkeys |
| ECEnabler / SMCBatteryManager | Battery / EC reading |
| CPUFriend / CpuTopologyRebuild / CpuTscSync | CPU power management and TSC sync |
| NVMeFix / S0ixStandby / HibernationFixup | NVMe, sleep / S0ix power |
| USBToolBox / UTBMap / UTBDefault / XHCI-unsupported | USB mapping |
| AMFIPass / RestrictEvents / BTLEAdvFixup / SimpleHDA / MHelper etc. | Compatibility patches and helpers |

> Kexts with a `.disabled` suffix (e.g. `AirportItlwm.kext.disabled`) are currently not enabled.

---

## 3. Key Configuration Notes

- **Audio**: currently `alcid=33`. Six layout-ids have been tested historically — `31 / 33 / 71 / 52 / 61 / 66` — of which **only `33` and `66` produce sound**; whether `31` produced sound is no longer remembered (needs re-test); the rest (`71 / 52 / 61`) are silent. The best layout for the headphone jack / external audio is still undecided.
- **Machine identifier / system serial / NIC address**: `config.plist` has been **desensitized to placeholders** (`XXXX...`); the real values are kept in the backup file (see next section). The desensitized version **cannot be used to boot and activate Apple services** — it is for safe archiving / sharing only.

---

## 4. Backups (Important)

`OC/` keeps multiple staged `config` snapshots; the filename records the change and its date:

| File | Date | Notes |
|---|---|---|
| `config.plist` | latest | **Active, but machine identifiers desensitized to placeholders (`XXXX...`)** |

---

## 5. Current Status & Known Open Items

| Status | Notes |
|---|---|
| ✅ System | Boots and runs stably |
| ⚠️ Function keys | Some function-key mappings incomplete (PS2 keyboard mapping layer); rarely used, deferred |
| ⚠️ Headphone jack | Best layout (33 ↔ 52) not yet finalized |


---

## 6. TODO (for future project downtime)

- [ ] Complete function-key mapping (PS2 keyboard layer)
- [ ] Finalize headphone jack / external audio layout
- [ ] Clean up historical `config` snapshots in `OC/` that have no reference value
