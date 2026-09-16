# Prerequisites

Shop and stage everything below before you treat the CMP 170HX as a daily driver. Missing cooling or the wrong power pinout will stop you colder than a bad driver pin.

## Essential hardware

### The card

**NVIDIA CMP 170HX**

- **Where used cards show up:** surplus GPU markets, eBay, regional mining liquidations
- **Identify before paying:** PCI ID `20c2` (8 GB → 64 GB unlock) or `2082` (10 GB → 40 GB unlock) on a stock driver
- **Avoid:** boards with bent PCIe fingers, cracked cooler mounts, or mystery “already unlocked” claims you cannot verify
- **MSRP context:** launched around **2021-09-01** near **$4299**; street pricing since The Merge has been far lower and volatile

Bring a known-good x86 Linux box to the meetup if you can run `lspci` before cash changes hands.

### Host system

Minimum useful host:

- PCIe x16 mechanical slot (electrical x4 is fine at stock; capacitor mod wants all sixteen traces intact on the card)
- UEFI firmware with **Above 4G Decoding**
- Ability to disable **Secure Boot**
- Enough chassis airflow for a dual-slot passive brick, or room for a GPU waterblock and radiator

Workstation and used server boards are both fair game. Document your exact BIOS knobs on [Host BIOS](../bios/host-bios.md) when you find them.

### Power supply

**12 V PSU with a real 8-pin CPU/EPS cable** (or a quality adapter that lands on the card’s EPS-style plug)

| Budget item | Guidance |
|-------------|----------|
| GPU TDP default | **250 W** |
| Software power ceiling | **~300 W** |
| Idle GPU | **~30–40 W** |
| System headroom | Size the PSU for GPU + CPU + drives with ≥20% margin |

!!! warning "Connector shape"
    Stock feed is **1× 8-pin CPU/EPS-style via adapter**, matching Full Specs. Do not assume a standard PCIe GPU 8-pin pinout on the card side. Use the adapter that shipped with the card or a verified equivalent.

[Power details →](../hardware/power.md)

### Cooling

Pick one path before first CUDA burn-in:

| Path | What to buy | Notes |
|------|-------------|-------|
| Forced air | High static pressure 120/140 mm fans aimed at the passive sink | Works in open benches and server chassis with directed airflow |
| Water | Bykski **N-TESLA-A100-X-V2** + loop (rad, pump, fittings, coolant) | Compatible family also covers A100 40GB PCIe / A30 / L40 per community validation |
| Avoid | Bykski **N-TESLA-A100-80G-X-V2**, non-V2 acrylic block | Wrong PCB or weaker construction |

!!! danger "Thermal pads on empty footprints"
    Every unpopulated IC footprint under the waterblock pillars needs a thermal pad. Bare copper under metal pillars shorts and kills boards. This is the most common waterblock brick mode called out in the watercooling install guide.

[Air Cooling →](../hardware/air-cooling.md) · [Water Cooling →](../hardware/water-cooling.md)

### Tools for optional mods

**PCIe capacitor mod** (later):

- Hot air or fine tip iron suitable for **0402**
- Magnification
- **24×** (order ≥30) **0402 0.22 µF** X7R, ≥16 V (example: Samsung **CL05B224KO5NNNC**)
- Teardown tools: Phillips, metric hex keys, plastic spudger

**Teardown / waterblock:**

- Phillips screwdriver
- Metric hex set (Bykski’s included wrench is often the wrong size)
- Plastic spudger
- Organized tray for spring screws and washers

Deep teardown steps: [Teardown](../hardware/teardown.md) and the [Teardown Guide](https://170th-street.gitbook.io/hx/hardware/teardown-guide).

---

## Essential software

### Linux distribution

Any modern distro you can pin is fine if it runs **nvidia-open 610.43.02 or 610.43.03** cleanly. Ubuntu LTS-class hosts are common in unlock write-ups. Snapshot the rootfs before patching modules.

Required pieces:

- Root access
- `linux-headers-$(uname -r)` / `kernel-devel` matching the running kernel
- Build tools for the unlock installer
- Python 3 (cmpunlocker build-time profile selection)
- Network on first install

### Unlock tooling

| Project | Role | URL |
|---------|------|-----|
| amoghmunikote/cmpunlocker | Canonical community unlock | https://github.com/amoghmunikote/cmpunlocker |
| bayley/cmpunlocker | Fork with additional P2P work | https://github.com/bayley/cmpunlocker |
| Consensus-Protocol/cmp170hx | Deep wiki (mechanism, procedures, frontier) | https://github.com/Consensus-Protocol/cmp170hx |

!!! info "Driver pin"
    Cache the exact nvidia-open **610.43.0x** packages locally. Community notes warn that NVIDIA may drop older open builds from easy mirrors.

### Monitoring

Install after drivers load:

- `nvidia-smi`
- `nvtop` or equivalent
- `lm-sensors` if your motherboard exposes useful package thermals
- Your LLM stack’s own metrics (vLLM / DFlash / whatever you standardize on)

---

## Highly recommended

### Thermal materials

- Fresh paste for die / heat spreader work after teardown
- Extra thermal pads sized for VRM / PMIC / empty footprints when watercooling
- Isopropyl alcohol and lint-free wipes

### Storage and networking

- Fast system NVMe for model weights
- Wired Ethernet while you debug unsigned modules and Secure Boot

### Spares

- Extra 0402 caps if you attempt the lane mod
- Spare EPS cable
- Known-good display GPU or BMC/IPMI path so a black CMP (no outputs) never leaves you blind

---

## Optional but useful

| Item | Why |
|------|-----|
| Second identical 170HX | Multi-GPU / P2P experiments (fork-dependent) |
| Dedicated bench power meter | Correlate wall draw with the 250–300 W envelope |
| USB microscope | 0402 inspection |
| Chassis with 2.5–3 slot clearance | Waterblock + fittings height |

---

## Before you order checklist

### From existing hardware

- [ ] Host with a free PCIe x16 slot and working Linux install path
- [ ] Monitor/KVM that does not depend on the CMP (it has no display outs)
- [ ] Keyboard, another machine for ISOs, USB installer stick
- [ ] Screwdriver + spudger if you plan teardown

### Plan the build

- [ ] Airflow map or radiator placement sketched
- [ ] PSU EPS cable confirmed
- [ ] SKU target chosen (64 GB unlock vs 40 GB unlock)
- [ ] Driver version **610.43.02/03** download staged
- [ ] Unlock repo cloned on a machine that can reach GitHub

---

## Ready to proceed?

1. [Quick Start](quick-start.md) for the ordered bring-up
2. [Introduction](introduction.md) if you still need the SKU story
3. [Specifications](../hardware/specifications.md) when you write purchase notes or bench headers

**Lab tip:** Order cooling parts in the same cart as the card. The passive sink is honest about its design target: a screaming server aisle with directed airflow.

## References

References and further info from:

- https://170th-street.gitbook.io/hx/hardware/full-specifications
- https://170th-street.gitbook.io/hx/hardware/teardown-guide
- https://170th-street.gitbook.io/hx/modifications/watercooling-installation
- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/bayley/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx
