# Quick Start Guide

Fast path from sealed CMP 170HX to an unlocked Linux compute card. Read [Prerequisites](prerequisites.md) first so you are not improvising cooling or power mid-flight.

!!! warning "Cooling is step zero"
    Do not chase unlock scripts on a dry passive heatsink in still air. Confirm airflow or a filled water loop before sustained load. Thermal runaway above ~**80 °C** is real.

## Lab performance headline

Unlocked 64 GiB lab benches lead with Nemotron-3.5-Lightning-30B-A3B W4A16 at **228 tok/s** (16k coding), batch **1,306 tok/s** aggregate at 32k×16, and **79%** decode retained at 128k versus 1k. Earlier Qwen3.8-27B DFlash ~212 tok/s is still in the matrix. Details: [Performance Overview](../performance/overview.md).

## Setup checklist

### Step 1: Identify the SKU

On any Linux host with the card seated (stock driver is fine for ID):

```bash
lspci -nn | grep -i nvidia
lspci -s <bus:dev.fn> -vvv | grep -E 'LnkCap|LnkSta|Subsystem'
```

| Device ID | Stock label | Unlock target |
|-----------|-------------|---------------|
| `10de:20c2` | 8 GB | **64 GB** |
| `10de:2082` | 10 GB | **40 GB** |

Subsystem checks: `1585` (8 GB) and `1557` (10 GB) per 170th Street.

**Difficulty:** Easy

[Full SKU tables →](../hardware/specifications.md)

### Step 2: Host BIOS

Boot the host firmware and set:

- **Above 4G Decoding:** Enabled (needed for large BAR / full VRAM mapping work)
- **Secure Boot:** Disabled (patched unlock modules are unsigned)
- **Resizable BAR / Re-BAR:** Enable if present; stock card still advertises a **64 MiB** limit until unlock tooling widens BAR behavior
- **PCIe slot:** Prefer a full x16 mechanical slot even though stock trains at x4

Exact menu names vary by vendor. Capture them on [Host BIOS](../bios/host-bios.md) as you learn your board.

**Difficulty:** Easy–Medium

### Step 3: Cooling and power before OS work

- Seat the **8-pin CPU/EPS-style** adapter firmly. This is the stock power feed.
- Aim a high-static-pressure fan at the passive heatsink, or complete waterblock install first.
- PSU: plan for **250 W** default TDP with headroom to ~**300 W** software limit, plus host CPU and drives.

!!! danger "Dry-run limit"
    If you ever power the card without coolant in a waterblock, shut down within about **five minutes**.

[Power →](../hardware/power.md) · [Cooling →](../hardware/cooling.md)

**Difficulty:** Medium (water) / Easy (forced air)

### Step 4: Install Linux and nvidia-open 610.43.0x

Community unlock tooling expects:

- Linux x86-64 (aarch64 appears on some forks; stick to x86-64 unless you know otherwise)
- **nvidia-open 610.43.02** or **610.43.03** already installed (libs + firmware)
- Kernel headers matching `uname -r`
- Network access on first unlock install (sources download)
- Root privileges

Install your distro, then the matching open driver package set. Freeze or snapshot that driver version before you patch modules.

**Difficulty:** Medium

[Unlock overview →](../linux-unlock/unlock-overview.md)

### Step 5: Stock sanity check

Before unlocking, confirm the card looks honest:

```bash
nvidia-smi
sudo lspci -s <bus:dev.fn> -vvv | grep -E 'LnkSta|LnkCap'
```

Expect roughly:

- Memory: **8192 MiB** or **10240 MiB**
- Link: Speed **2.5 GT/s**, Width **x4**
- Idle board power in the **~30–40 W** neighborhood when cool and idle

If you already see 40–64 GB on a “stock” driver, someone unlocked this host earlier. Document that before you overwrite modules.

**Difficulty:** Easy

### Step 6: Install cmpunlocker

Canonical tool: [amoghmunikote/cmpunlocker](https://github.com/amoghmunikote/cmpunlocker). Follow that README for the current `install.sh` flags. Typical profile selection:

```bash
sudo ./install.sh --profile=8gb    # 8 GB card → 64 GB geometry
sudo ./install.sh --profile=10gb   # 10 GB card → 40 GB geometry
```

Then perform a **cold reboot** (full power off, then boot).

!!! tip "DKMS priority trap (Ubuntu-class hosts)"
    ServeTheHome forum reports: stock DKMS modules under `updates/dkms/` can win over `updates/cmpunlocker/`. If `nvidia-smi` still shows 8192 MiB after a “successful” install, check `modinfo -n nvidia` and add an explicit depmod override pointing at `updates/cmpunlocker`. Details: [Troubleshooting](../troubleshooting/common.md).

Deep mechanism docs: [Consensus-Protocol/cmp170hx wiki](https://github.com/Consensus-Protocol/cmp170hx/wiki). It Still Boots will not rewrite the Falcon / ROP bible here.

**Difficulty:** Medium–Hard

### Step 7: Verify the unlock

```bash
nvidia-smi
sudo lspci -s <bus:dev.fn> -vvv | grep LnkSta
```

Success looks like:

- Memory: **65536 MiB** (8 GB SKU) or **40960 MiB** (10 GB SKU)
- Link speed: Gen2-class (**5.0 GT/s**) once Gen2 unlock is in your cmpunlocker build
- Width: still **x4** until you complete the capacitor mod

Optional compute sniff test: a short CUDA or OpenCL FP32 probe should leave the **~0.4 TFLOPS** stock FMA class and move toward the **~12–13 TFLOPS** unlocked class. Keep the probe short until cooling is proven.

**Difficulty:** Easy

### Step 8: Optional hardware upgrades

Only after unlock verification:

1. [PCIe capacitor mod](../modifications/pcie-capacitor-mod.md): **24× 0402 0.22 µF** → Gen1 x16 (~4 GB/s). Does not raise Gen by itself.
2. [Watercooling](../modifications/watercooling.md): Bykski **N-TESLA-A100-X-V2** (avoid 80G and non-V2). Pad every empty IC footprint.

**Difficulty:** Hard (SMD) / Hard (loop)

### Step 9: Run a real workload

Point your LLM stack at the unlocked device. It Still Boots lab headline: Nemotron W4A16 **228 tok/s** (16k coding), batch **1,306 tok/s**, **79%** retained at 128k; earlier Qwen3.8-27B DFlash ~212 tok/s sits in the matrix. Do not invent CSVs here; see [Performance](../performance/overview.md).

---

## Quick troubleshooting

### Still shows 8 GB / 10 GB after unlock

**Check:** `modinfo -n nvidia` path, Secure Boot state, cold vs warm reboot, correct `--profile`, dmesg for unlock / SEC2 lines.

### Link stuck at Gen1 after unlock

**Check:** cmpunlocker build date / branch includes Gen2 (shipped in master since 2026-07-29 per Consensus-Protocol README). Width staying at x4 is expected without capacitor mod.

### Thermal throttle or sudden power-off

**Check:** airflow direction, heatsink seating, water loop fill, `nvidia-smi -q -d TEMPERATURE,POWER`. Power off if you approach **80 °C** without a plan.

### Host refuses to POST with card installed

**Check:** EPS adapter seating, PSU capacity, try another slot, clear CMOS, confirm the card is not shorted after a partial waterblock install.

[Troubleshooting →](../troubleshooting/common.md)

---

## Next steps

1. Fill out [Prerequisites](prerequisites.md) shopping gaps
2. Read [Specifications](../hardware/specifications.md) once before you publish benches
3. Follow [Unlock Overview](../linux-unlock/unlock-overview.md) for canonical links
4. Drop numbers into [Performance](../performance/overview.md) when the lab run is reproducible

## References

References and further info from:

- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx/wiki
- https://170th-street.gitbook.io/hx
- https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/
