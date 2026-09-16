# Power Requirements

How to feed a CMP 170HX without melting an adapter or brown-outing a mid tower PSU. Numbers below come from 170th Street Full Specs and community unlock docs; treat wall-meter readings from your own chassis as ground truth.

Sources: [Full Specifications](https://170th-street.gitbook.io/hx/hardware/full-specifications), [Consensus-Protocol/cmp170hx](https://github.com/Consensus-Protocol/cmp170hx).

## Envelope

| Item | Value |
|------|-------|
| Default TDP | **250 W** |
| Software max power limit | ~**300 W** |
| Idle (typical) | ~**30–40 W** |
| Stock connector | **1× 8-pin CPU/EPS-style** via adapter |
| Slot power | Do not rely on the slot alone for load |

!!! warning "EPS-style plug"
    The card expects the mining/server adapter path: an **8-pin CPU/EPS-style** connector. Use the shipped adapter or a verified equivalent and check seating twice before first power-on.

## Sizing the PSU

Plan continuous budget as:

```text
PSU_12V_budget >= (GPU_300W_ceiling + CPU + drives + fans) * 1.2
```

Practical floors It Still Boots uses when shopping used PSUs:

| Host class | Suggested 12 V headroom |
|------------|-------------------------|
| Single 170HX + modest CPU | **550 W** quality unit minimum comfort |
| Single 170HX + workstation CPU + dual NVMe | **650–750 W** |
| Dual 170HX | Size for **2 × 300 W** GPU ceiling plus host, then add margin |

Idle is friendly (~30–40 W on the GPU). Sustained unlocked FP32 / LLM decode is where the 250–300 W band shows up. Meter the wall once during a long generate job and write the number next to your Performance entry.

!!! tip "Software limit vs heatsink"
    Raising the power limit toward **300 W** without proving cooling first is how you meet the thermal runaway warning on [Cooling](cooling.md). Leave the limit at default until temperatures are boring.

## Cabling checklist

- [ ] EPS/CPU 8-pin fully latched on the adapter
- [ ] Adapter fully latched on the card
- [ ] No SATA-to-PCIe cheater cables anywhere in the GPU path
- [ ] Cable gauge appropriate for ~25 A class 12 V draw on that branch
- [ ] Strain relief so the stiff mining cable cannot lever the PCB (especially after waterblock installs)

## Multi-GPU notes

Each additional 170HX adds another ~250–300 W planning chunk and another EPS feed. P2P / BAR1 experiments (see forks such as [bayley/cmpunlocker](https://github.com/bayley/cmpunlocker)) still need the same electrical budget. Gen1/Gen2 x4 links will dominate communication long before the PSU does if you skipped the capacitor mod.

## Failure modes

| Symptom | Likely cause | First check |
|---------|--------------|-------------|
| Instant shutdown under load | OCP / weak 12 V rail | Meter PSU rating; reseat EPS |
| Card disappears after resume | Brown-out or cable creep | Reseat adapter; check dmesg |
| Connector warm to the touch | High resistance crimp or partial seat | Power off; inspect pins |
| Idle power >> 40 W | Stuck clocks / failed fan curve on host / measuring whole system | Isolate GPU via `nvidia-smi -q -d POWER` |

## Related pages

- [Specifications](specifications.md)
- [Cooling](cooling.md)
- [Prerequisites](../getting-started/prerequisites.md)
- [Quick Start](../getting-started/quick-start.md)
