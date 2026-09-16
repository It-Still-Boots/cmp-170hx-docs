# Unlock Overview

How It Still Boots brings a stock CMP 170HX to unlocked HBM and usable SM throughput on Linux. This page is the lab runbook. Mechanism detail (Falcon, Booter, ROP chain, register tables) lives on the [Consensus-Protocol/cmp170hx wiki](https://github.com/Consensus-Protocol/cmp170hx/wiki). We do not rewrite that bible here.

Canonical installer: [amoghmunikote/cmpunlocker](https://github.com/amoghmunikote/cmpunlocker). Related fork with extra P2P work: [bayley/cmpunlocker](https://github.com/bayley/cmpunlocker).

## What cmpunlocker changes

Patched **nvidia-open** kernel modules reconfigure the GPU during GSP boot. Nothing is flashed to VBIOS. On every module load the unlock re-applies.

| Capability | Result |
|------------|--------|
| Memory geometry | **8 GB** stock (`0x20C2`) → **64 GB** unlocked; **10 GB** stock (`0x2082`) → **40 GB** unlocked |
| SM / FP32 FMA throttle | Stock ~**0.39–0.41 TFLOPS** class FP32 climbs toward ~**12–13 TFLOPS** unlocked (measure your own card) |
| PCIe Gen | Software path to **Gen2** (5.0 GT/s) when the build includes Gen2 patches |
| BAR1 | Widened toward full VRAM mapping (needs host [Above 4G Decoding](../bios/host-bios.md)) |
| Persistence | Survives reboot as long as the patched modules stay preferred by the module loader |

Capacity is per SKU. An 80 GB profile on 10 GB cards was built, tested, and rejected as unstable by community wiki consensus.

PCIe **width** is a separate problem. Stock trains **x4** because twelve of sixteen lanes lack AC coupling caps. Gen2 unlock does not solder those caps. See [PCIe capacitor mod](../modifications/pcie-capacitor-mod.md).

## Driver pin

Community unlock paths in this docs set target **nvidia-open 610.43.02** or **610.43.03** (libs + firmware), matching kernel headers, root, and Secure Boot off. Pin or snapshot that driver tree before you patch modules. Routine `apt upgrade` of nvidia packages is a common way to lose the unlock.

Check the cmpunlocker README for the current supported range before you install. If upstream moves the pin, follow upstream and update this page.

## Order of operations

1. **Cool the card** (forced air on the passive sink, or a filled water loop). See [Cooling](../hardware/cooling.md).
2. **Confirm stock identity** with `lspci` / `nvidia-smi`: device `20c2` or `2082`, Gen1 x4, 8192 or 10240 MiB.
3. **Host BIOS**: Above 4G on, Secure Boot off. [Host BIOS](../bios/host-bios.md).
4. **Install and pin nvidia-open 610.43.02/03** plus matching headers.
5. **Run cmpunlocker** (`install.sh`, with `--profile=8gb` or `--profile=10gb` when you need to force geometry).
6. **Cold reboot** (full power off, then boot).
7. **Verify** MiB and link speed (below).
8. Only then chase optional hardware: [capacitor mod](../modifications/pcie-capacitor-mod.md), [watercooling](../modifications/watercooling.md).

## Install sketch

Follow the live README. Typical shape:

```bash
git clone https://github.com/amoghmunikote/cmpunlocker.git
cd cmpunlocker
sudo ./install.sh --profile=8gb    # 8 GB card → 64 GB
# sudo ./install.sh --profile=10gb # 10 GB card → 40 GB
```

Then cold reboot.

!!! warning "Secure Boot and DKMS"
    Patched modules are unsigned. Secure Boot must stay off. On Ubuntu-class hosts, stock DKMS modules under `updates/dkms/` can win over `updates/cmpunlocker/`. If memory is still 8/10 GB after a "successful" install, run `modinfo -n nvidia` and fix module priority. See [Troubleshooting](../troubleshooting/common.md).

## Verify

```bash
nvidia-smi
sudo lspci -s <bus:dev.fn> -vvv | grep LnkSta
modinfo -n nvidia
```

Success looks like:

| Check | 8 GB SKU | 10 GB SKU |
|-------|----------|-----------|
| `nvidia-smi` memory | **65536 MiB** | **40960 MiB** |
| Link speed (Gen2 unlock present) | **5.0 GT/s** | **5.0 GT/s** |
| Link width (no capacitor mod) | **x4** | **x4** |
| Module path | Contains `cmpunlocker` (or your install's updates path) | same |

Optional short CUDA/OpenCL FP32 probe should leave the ~0.4 TFLOPS stock FMA class. Keep probes short until cooling is proven.

## What we deliberately omit

Falcon privilege masks, Booter PLM sequences, ROP gadget layout, and per-register write tables belong on the [Consensus-Protocol wiki](https://github.com/Consensus-Protocol/cmp170hx/wiki). Start at Unlock → How it works / Falcon and the Booter if you need that depth. It Still Boots documents what we run and how we verify it.

## Related pages

- [Quick Start](../getting-started/quick-start.md)
- [Host BIOS](../bios/host-bios.md)
- [Troubleshooting](../troubleshooting/common.md)
- [Quick Reference](../reference/quick-reference.md)

## References

References and further info from:

- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/bayley/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx/wiki
- https://170th-street.gitbook.io/hx
- https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/
