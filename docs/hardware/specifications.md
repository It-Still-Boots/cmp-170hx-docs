# Hardware Specifications

Single-page reference for the NVIDIA CMP 170HX as It Still Boots uses it in the lab. Figures below prefer 170th Street’s Introduction for SKU-split compute counts, and Full Specifications for clocks, power, APIs, and memory geometry. Where those pages disagree, the discrepancy is called out explicitly.

Primary sources: [Full Specifications](https://170th-street.gitbook.io/hx/hardware/full-specifications), [What is the CMP 170HX?](https://170th-street.gitbook.io/hx/introduction/what-is-the-cmp-170hx), [170th-Street GitHub](https://github.com/amoghmunikote/170th-Street).

## General

| Property | Value |
|----------|-------|
| Model | NVIDIA CMP 170HX |
| Architecture | Ampere (GA100) |
| Process | TSMC 7 nm N7 FinFET |
| Transistors | 54.2 billion |
| Die size | 826 mm² |
| Form factor | Dual-slot PCIe add-in card |
| Cooling (stock) | Passive server heatsink |
| Display outputs | None |
| Release | ~2021-09-01 |
| Original MSRP | ~$4299 USD |

## SKU identification

| Property | 8 GB SKU | 10 GB SKU |
|----------|----------|-----------|
| Stock capacity | 8 GB HBM2e | 10 GB HBM2e |
| PCI Device ID | `0x20C2` | `0x2082` |
| PCI Subsystem ID | `0x1585` | `0x1557` |
| Die | GA100-105F-A1 | GA100-105A-A1 |
| Unlocked capacity | **64 GB** | **40 GB** |
| Memory bus | 4096-bit | 5120-bit |
| Bandwidth (approx.) | ~1.49 TB/s | ~1.56 TB/s |

!!! info "Capacity is per SKU"
    Consensus-Protocol and cmpunlocker docs agree: 8 GB stock unlocks to 64 GB; 10 GB stock unlocks to 40 GB. An 80 GB profile on 10 GB cards has been built, tested, and rejected as unstable.

## Compute

Introduction-side split (It Still Boots preferred):

| Property | 8 GB SKU | 10 GB SKU |
|----------|----------|-----------|
| Streaming Multiprocessors | 56 | 70 |
| CUDA cores (64 per SM) | 3584 | 4480 |
| Tensor cores (4 per SM) | 224 | 280 |

Shared clock domain from Full Specs:

| Property | Value |
|----------|-------|
| Base clock | 1140 MHz |
| Boost clock | 1410 MHz |
| L1 cache | 192 KB per SM |
| L2 cache | 32 MB (32768 KB) |
| TMUs | 280 (Full Specs table; listed without SKU split) |
| ROPs | 128 (Full Specs table; listed without SKU split) |

!!! warning "SM count discrepancy (Patrick to resolve)"
    170th Street **Introduction** splits **56 SM / 3584 CUDA / 224 tensor** (8 GB) vs **70 SM / 4480 CUDA / 280 tensor** (10 GB). The **Full Specifications** Compute table lists **SM 70 / 4480 CUDA / 280 tensor** with no SKU split. It Still Boots documents both and prefers the Introduction split until Patrick confirms which table wins for marketing and for `nvidia-smi` / CUDA device query cross-checks.

## Memory

| Property | 8 GB SKU | 10 GB SKU |
|----------|----------|-----------|
| Type | HBM2e | HBM2e |
| Stock capacity | 8 GB | 10 GB |
| Unlocked capacity | 64 GB | 40 GB |
| Bus width | 4096-bit | 5120-bit |
| Memory clock (Full Specs) | 1458 MHz | 1215 MHz |
| Bandwidth (theoretical) | ~1.49 TB/s | ~1.56 TB/s |
| ECC | Disabled | Disabled |
| Resizable BAR | Present, limited to **64 MiB** stock | Present, limited to **64 MiB** stock |

!!! tip "Bandwidth note"
    Introduction text sometimes quotes a higher theoretical figure for the 10 GB part (~1865 GB/s in one paragraph). Full Specs lists ~1.56 TB/s. It Still Boots uses **~1.49 / ~1.56 TB/s** from Full Specs / the locked fact sheet until someone re-derives from measured clocks.

## Performance (theoretical vs unlocked)

From 170th Street Full Specs performance table (paraphrased; see source for raw rows):

| Metric | Stock (throttled) | Unlocked (class) |
|--------|-------------------|------------------|
| FP32 | ~0.41 TFLOPS | ~12.99 TFLOPS |
| FP64 | ~0.19 TFLOPS | ~6.44 TFLOPS |
| FP16 | ~49.05 TFLOPS | ~49.05 TFLOPS (table shows same) |
| INT8 | ~1.64 TFLOPS | ~48.03 TFLOPS |

Lab prose often summarizes stock FP32 FMA as the **~0.39–0.41 TFLOPS** class and unlocked FP32 as the **~12–13 TFLOPS** class. Cross-check your own OpenCL/CUDA microbench before publishing.

PCB note: reference designators match the A100 40GB PCIe family closely enough that A100 schematics and waterblocks are useful (see [Teardown](teardown.md) and [Cooling](cooling.md)).

## Connectivity

| Property | Value |
|----------|-------|
| Mechanical slot | PCIe x16 |
| Stock electrical link | Gen1 x4 (~1 GB/s) |
| After capacitor mod | Gen1 x16 (~4 GB/s) |
| After software Gen unlock | Gen2 (width still whatever the caps allow) |
| NVLink | Connectors present, fuse-disabled |
| Power connector | 1× 8-pin CPU/EPS-style via adapter |

Stock lockdown is two independent layers: firmware Gen lock, plus missing AC coupling capacitors on **12 of 16** lanes. Capacitor mod and Gen2 unlock each solve one layer. Neither replaces the other. Procedure stub: [PCIe capacitor mod](../modifications/pcie-capacitor-mod.md).

## Power

| Property | Value |
|----------|-------|
| TDP (default) | 250 W |
| Max power limit (software) | ~300 W |
| Idle (typical) | ~30–40 W |

See [Power](power.md).

## API support

| API | Supported |
|-----|-----------|
| CUDA | Yes (Compute Capability **8.0**) |
| OpenCL | Yes (3.0 per Full Specs) |
| DirectX | No |
| Vulkan | No |
| OpenGL | No |
| NVENC / NVDEC | No |

## Form factor and cooling implications

- Dual-slot passive cooler sized for rack airflow
- No display outs: always keep a second GPU, iGPU, or BMC console for the host
- Waterblock family: Bykski **N-TESLA-A100-X-V2** (see [Cooling](cooling.md))

## Related pages

- [Power](power.md)
- [Cooling](cooling.md)
- [Teardown](teardown.md)
- [Quick Start](../getting-started/quick-start.md)
