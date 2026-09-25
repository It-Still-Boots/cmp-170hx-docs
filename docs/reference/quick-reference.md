# Quick Reference

One-page cheat sheet for the CMP 170HX lab desk. For narrative context see [Introduction](../getting-started/introduction.md) and [Specifications](../hardware/specifications.md).

## PCI IDs and unlock targets

| Stock label | Device ID | Subsystem | Unlocked HBM |
|-------------|-----------|-----------|--------------|
| 8 GB | `0x20C2` (`10de:20c2`) | `0x1585` | **64 GB** (65536 MiB) |
| 10 GB | `0x2082` (`10de:2082`) | `0x1557` | **40 GB** (40960 MiB) |

Compute (Full Specs, both SKUs): **70 SM / 4480 CUDA / 280 tensor**. See [Specifications](../hardware/specifications.md).

## Power and cooling limits

| Item | Value |
|------|-------|
| Default TDP | **250 W** |
| Software ceiling | **~300 W** |
| Idle GPU | **~30–40 W** |
| Connector | **1× 8-pin CPU/EPS-style** via adapter |
| Thermal hard stop | **~80 °C** until the loop/air path is proven |
| Dry-run without coolant | Power off within **~5 minutes** |

## PCIe layers

| Layer | Stock | After fix |
|-------|-------|-----------|
| Width | Gen1 **x4** (~1 GB/s) | Caps: Gen1 **x16** (~4 GB/s) |
| Generation | Firmware Gen1 | Software Gen2 (**5.0 GT/s**) via cmpunlocker |
| Caps parts | (n/a) | **24× 0402 0.22 µF** (ex. Samsung CL05B224KO5NNNC) |

## Driver and unlock

| Item | Value |
|------|-------|
| Driver pin | **nvidia-open 610.43.02** or **610.43.03** |
| Tool | [amoghmunikote/cmpunlocker](https://github.com/amoghmunikote/cmpunlocker) |
| Profiles | `--profile=8gb` → 64 GB; `--profile=10gb` → 40 GB |
| Host firmware | Above 4G **on**, Secure Boot **off** |
| BAR1 stock | **64 MB**. A larger BAR1 is a separate kernel fixup, not the framebuffer unlock |

## Key commands

```bash
# Identity
lspci -nn | grep -i nvidia
lspci -s <bus:dev.fn> -vvv | grep -E 'LnkCap|LnkSta|Subsystem'

# Memory / thermals / power
nvidia-smi
nvidia-smi -q -d TEMPERATURE,POWER,MEMORY

# Which nvidia.ko won?
modinfo -n nvidia

# Link after unlock / capacitor mod
sudo lspci -s <bus:dev.fn> -vvv | grep LnkSta
```

| Stage | Memory | `LnkSta` (typical) |
|-------|--------|--------------------|
| Stock | 8192 or 10240 MiB | 2.5 GT/s, x4 |
| Unlocked, no caps | 65536 or 40960 MiB | 5.0 GT/s (if Gen2 in build), x4 |
| Caps only | stock MiB until unlock | 2.5 GT/s, x16 |
| Unlocked + caps | full unlocked MiB | 5.0 GT/s, x16 when both stick |

## Related pages

- [Quick Start](../getting-started/quick-start.md)
- [Unlock Overview](../linux-unlock/unlock-overview.md)
- [Host BIOS](../bios/host-bios.md)
- [Troubleshooting](../troubleshooting/common.md)

## References

- https://170th-street.gitbook.io/hx/introduction/what-is-the-cmp-170hx
- https://170th-street.gitbook.io/hx/hardware/full-specifications
- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx/wiki
- https://170th-street.gitbook.io/hx/modifications/pcie-capacitor-mod
