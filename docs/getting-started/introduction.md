# Introduction to the CMP 170HX

The NVIDIA CMP 170HX is a 2021 Cryptocurrency Mining Processor card built on GA100 silicon. Miners bought it for Ethash. After The Merge, the same boards landed on the surplus market as odd, heavily locked compute cards. Community work since then restored usable HBM geometry and unthrottled SM throughput. The unlocked 170HX is a workable lab GPU for LLM inference and CUDA workloads, with the caveats called out below.

## What you are holding

At the silicon level this is Ampere GA100: TSMC 7 nm, about **54.2 billion** transistors, **826 mm²** die. The PCB tracks the A100 40GB PCIe reference closely, including component designators that match leaked Tesla A100 schematics. NVIDIA built the 170HX themselves (unlike partner CMP HX boards) from GA100 bins that missed A100 quality bars.

Two confirmed production SKUs:

| Property | 8 GB SKU | 10 GB SKU |
|----------|----------|-----------|
| Stock marketed capacity | 8 GB HBM2e | 10 GB HBM2e |
| PCI Device ID | `0x20C2` | `0x2082` |
| PCI Subsystem ID | `0x1585` | `0x1557` |
| Die marking | GA100-105F-A1 | GA100-105A-A1 |
| SMs (Full Specs) | 70 | 70 |
| CUDA cores (Full Specs) | 4480 | 4480 |
| Tensor cores (Full Specs) | 280 | 280 |
| Memory bus | 4096-bit | 5120-bit |
| Bandwidth (approx.) | ~1.49 TB/s | ~1.56 TB/s |
| Unlocked capacity | **64 GB** | **40 GB** |

Shared clocks and power envelope: base **1140 MHz**, boost **1410 MHz**, default TDP **250 W**, software max around **300 W**, idle about **30–40 W**.

!!! info "Compute counts"
    Both SKUs lock to Full Specs: **70 SM / 4480 CUDA / 280 tensor**. Memory bus, bandwidth, and unlocked capacity still differ by SKU.

Sources: [What is the CMP 170HX?](https://170th-street.gitbook.io/hx/introduction/what-is-the-cmp-170hx), [Full Specifications](https://170th-street.gitbook.io/hx/hardware/full-specifications), [170th-Street repo](https://github.com/amoghmunikote/170th-Street).

## What NVIDIA clamped

Stock behavior is intentional lockdown layered on complete-enough silicon:

1. **FP32 FMA throttle.** Stock FP32 FMA sits in the **~0.39–0.41 TFLOPS** class. Unlocked FP32 climbs into the **~12–13 TFLOPS** class per 170th Street tables.
2. **Memory geometry.** Firmware/OTP presents 8 GB or 10 GB. Physical HBM stacks unlock to **64 GB** or **40 GB** respectively via cmpunlocker.
3. **PCIe Gen lock.** Firmware holds the link at Gen1 until a software unlock enables Gen2.
4. **PCIe lane width.** Twelve of sixteen lanes lack AC coupling capacitors on the PCB, so the link trains at **x4** until you solder the missing caps.
5. **Display and graphics APIs.** No outputs. No DirectX, Vulkan, OpenGL, or NVENC. CUDA CC **8.0** and OpenCL are the useful paths.
6. **NVLink.** Connectors exist; fuses keep them dead.
7. **Resizable BAR.** Present but capped at **64 MiB** stock.

Combined stock PCIe is roughly **~1 GB/s** (Gen1 x4). Capacitor mod alone yields Gen1 x16 (~**4 GB/s**). Gen2 is independent and comes from the unlock tool.

## Why the card exists

NVIDIA announced the CMP line in early 2021 during the GPU shortage, framing CMP as mining-only hardware so GeForce stayed positioned for gamers. The 170HX appeared on the Chinese market around **2021-09-01** at roughly **$4299–$4700** MSRP-class pricing, with no glossy Western launch. It is the only CMP HX model NVIDIA manufactured in-house, using GA100 dies that failed A100 binning.

After Ethereum's Merge (2022-09-15), mining fleets dumped hardware. Street prices collapsed into the low hundreds of dollars for years, which is when reverse engineers and hobbyists started treating the board as a compute puzzle. The modern unlock stack (cmpunlocker on nvidia-open **610.43.0x**) is what makes the lab numbers on this site possible.

History detail: [History & Timeline](https://170th-street.gitbook.io/hx/introduction/history-and-timeline).

## What unlocked compute looks like

With cmpunlocker installed on a matching open driver:

- Memory reports **65536 MiB** (8 GB SKU) or **40960 MiB** (10 GB SKU)
- SM compute throttle lifts (SS0/SS1 path in community tooling)
- PCIe Gen2 becomes available in software
- CUDA and OpenCL workloads stop looking like a 2008 GPU

Lab headline on unlocked ~**64 GB** HBM: Nemotron-3.5-Lightning-30B-A3B W4A16 at **228 tok/s** (16k coding), batch **1,306 tok/s** aggregate at 32k×16, and **79%** decode retained at 128k versus 1k. Earlier Qwen3.8-27B DFlash ~212 tok/s remains in the matrix. Full tables and method live on [Performance Overview](../performance/overview.md).

## Who this is for

Good fit if you:

- Want high-bandwidth HBM for LLM inference on a surplus budget
- Are comfortable with Linux kernel modules, Secure Boot off, and pinned driver versions
- Will invest in airflow or a proper water loop before load testing
- Care about documenting numbers and failure modes

Harder fit if you need Windows gaming APIs, plug-and-play enterprise support, NVLink fabrics, or ECC.

## Risks you should price in

!!! danger "Thermal runaway"
    GA100 leakage rises with temperature. Without coolant or strong airflow, temperatures above ~**80 °C** can run away. Dry-run limit: power off within ~**5 minutes**.

!!! warning "Experimental unlock"
    cmpunlocker patches kernel modules. Expect Secure Boot conflicts, DKMS priority traps, and breakage on routine driver upgrades. Read [Unlock Overview](../linux-unlock/unlock-overview.md) and the [Consensus-Protocol wiki](https://github.com/Consensus-Protocol/cmp170hx/wiki) before you treat a box as production.

!!! warning "Waterblock shorts"
    Empty IC footprints on the PCB must get thermal pads before a waterblock goes on. Metal pillars can short bare copper. See [Water Cooling](../hardware/water-cooling.md) (and [Air Cooling](../hardware/air-cooling.md) for forced-air bring-up).

!!! info "SKU traps when buying"
    Verify PCI IDs on a stock driver before you pay. A100 device IDs differ (`20b0` / `20b2` / `20b5` / `20f1` class). SM count and Gen1-class PCIe are honest tells on an unpatched host. ServeTheHome's [unlocked measurements thread](https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/) has a practical comparison table.

## What's next

- [Quick Start](quick-start.md) for the bring-up checklist
- [Prerequisites](prerequisites.md) for PSU, cooling, and host shopping
- [Specifications](../hardware/specifications.md) for the full tables
- [Unlock Overview](../linux-unlock/unlock-overview.md) once the card is cool and identified

## References

References and further info from:

- https://170th-street.gitbook.io/hx
- https://170th-street.gitbook.io/hx/introduction/what-is-the-cmp-170hx
- https://170th-street.gitbook.io/hx/introduction/history-and-timeline
- https://170th-street.gitbook.io/hx/hardware/full-specifications
- https://github.com/amoghmunikote/170th-Street
- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx
- https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/
