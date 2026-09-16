# Hardware Specifications

Single-page reference for the NVIDIA CMP 170HX as used in the lab. Compute counts follow 170th Street Full Specifications (**70 SM / 4480 CUDA / 280 tensor** for both SKUs). Clocks, power, APIs, memory geometry, and bandwidth come from the same Full Specs / locked fact sheet.

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

Full Specs compute (both SKUs):

| Property | Value |
|----------|-------|
| Streaming Multiprocessors | 70 |
| CUDA cores (64 per SM) | 4480 |
| Tensor cores (4 per SM) | 280 |
| Base clock | 1140 MHz |
| Boost clock | 1410 MHz |
| L1 cache | 192 KB per SM |
| L2 cache | 32 MB (32768 KB) |
| TMUs | 280 |
| ROPs | 128 |

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

Locked bandwidth figures: **~1.49 TB/s** (8 GB SKU) and **~1.56 TB/s** (10 GB SKU).

## Performance (theoretical vs unlocked)

From 170th Street Full Specs performance table (paraphrased; see source for raw rows):

| Metric | Stock (throttled) | Unlocked (class) |
|--------|-------------------|------------------|
| FP32 | ~0.41 TFLOPS | ~12.99 TFLOPS |
| FP64 | ~0.19 TFLOPS | ~6.44 TFLOPS |
| FP16 | ~49.05 TFLOPS | ~49.05 TFLOPS (table shows same) |
| INT8 | ~1.64 TFLOPS | ~48.03 TFLOPS |

Lab prose often summarizes stock FP32 FMA as the **~0.39–0.41 TFLOPS** class and unlocked FP32 as the **~12–13 TFLOPS** class. Cross-check your own OpenCL/CUDA microbench before publishing.

PCB note: reference designators match the A100 40GB PCIe family closely enough that A100 schematics and waterblocks are useful (see [Teardown](teardown.md) and [Water Cooling](water-cooling.md)).

## Connectivity

| Property | Value |
|----------|-------|
| Mechanical slot | PCIe x16 |
| Stock electrical link | Gen1 x4 (~1 GB/s) |
| After capacitor mod | Gen1 x16 (~4 GB/s) |
| After software Gen unlock | Gen2 (width still whatever the caps allow) |
| Gen3 | Work in progress |
| NVLink | Connectors present, fuse-disabled |
| Power connector | 1× 8-pin CPU/EPS-style via adapter |

Stock lockdown is two independent layers: firmware Gen lock, plus missing AC coupling capacitors on **12 of 16** lanes. Capacitor mod and Gen2 unlock each solve one layer. Neither replaces the other. Procedure: [PCIe capacitor mod](../modifications/pcie-capacitor-mod.md).

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
- Waterblock family: Bykski **N-TESLA-A100-X-V2** (see [Water Cooling](water-cooling.md))

## Related pages

- [Power](power.md)
- [Air Cooling](air-cooling.md)
- [Water Cooling](water-cooling.md)
- [Teardown](teardown.md)
- [Quick Start](../getting-started/quick-start.md)

## References

References and further info from:

- https://170th-street.gitbook.io/hx/hardware/full-specifications
- https://170th-street.gitbook.io/hx/introduction/what-is-the-cmp-170hx
- https://github.com/amoghmunikote/170th-Street
- https://github.com/Consensus-Protocol/cmp170hx/wiki
