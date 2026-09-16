# Cooling

The CMP 170HX ships with a passive server heatsink. In a rack aisle with directed airflow that is a feature. On a desk, it is a thermal incident waiting for a CUDA kernel. This page summarizes the safe operating story; full waterblock screw-by-screw steps stay on 170th Street until It Still Boots drafts [watercooling.md](../modifications/watercooling.md).

Sources: [Watercooling Installation](https://170th-street.gitbook.io/hx/modifications/watercooling-installation), [Teardown Guide](https://170th-street.gitbook.io/hx/hardware/teardown-guide), [Full Specifications](https://170th-street.gitbook.io/hx/hardware/full-specifications).

## Thermal reality

| Fact | Detail |
|------|--------|
| Stock cooler | Passive dual-slot copper/aluminum server sink |
| Idle power | ~30–40 W (easy) |
| Load envelope | 250 W default, up to ~300 W software |
| Runaway threshold | Above ~**80 °C**, leakage can feed temperature in a positive loop |
| Dry-run rule | Power off within ~**5 minutes** if the card has no coolant / no real airflow |

!!! danger "Thermal runaway"
    GA100 leakage rises with temperature. 170th Street’s watercooling guide warns that dry-running without coolant is a short experiment only: shut down within about five minutes, and treat temperatures above ~80 °C as an emergency stop.

## Path A: Forced air on the stock sink

Use this for bring-up and light unlock verification.

- Aim **high static pressure** fans directly through the fin stack (open bench or shroud).
- Prefer push or push-pull across the dense fins; random case intake is usually insufficient.
- Watch `nvidia-smi` temperature while you run a short probe, then back off if the curve climbs without plateau.
- Keep dust out of the fins; mining-farm residue is common on surplus cards.

!!! tip "Server chassis"
    A 2U/4U chassis with GPU-directed fans is the stock design intent. If you already own that airflow, you can defer watercooling.

## Path B: Watercooling (summary)

Recommended block from 170th Street community validation:

| Item | Spec |
|------|------|
| Block | Bykski **N-TESLA-A100-X-V2** |
| Avoid | **N-TESLA-A100-80G-X-V2** (different PCB), non-V2 acrylic revision |
| Also fits family | A100 40GB PCIe, A30 24GB, Tesla L40 (shared layout story) |
| Prep | Complete [teardown](teardown.md) to bare PCB first |

!!! danger "Empty IC footprints"
    Unpopulated IC footprints **must** receive thermal pads before the block goes on. Waterblock contact pillars will short exposed copper if a footprint is left bare. This is the most common permanent-damage mode during install.

Performance anecdote from 170th Street (paraphrased): around **180 W** on a non-FMA FP32 workload, a **360 mm** radiator with fans at minimum speed held roughly **45 °C**. Treat that as a qualitative datapoint, then log your own loop.

Full procedure (pads, power-cable wrestling, backplate spacing with the saved PCIe bracket): [170th Street Watercooling Installation](https://170th-street.gitbook.io/hx/modifications/watercooling-installation). It Still Boots stub: [watercooling.md](../modifications/watercooling.md).

## Monitoring checklist

- [ ] `nvidia-smi -q -d TEMPERATURE,POWER` during first unlock verify
- [ ] Hard stop policy written down (It Still Boots lab default: abort near **80 °C** until the loop is proven)
- [ ] After water install: pressure-test the loop ≥15 minutes before filling with coolant (per 170th Street)
- [ ] Recheck pad compression at the block edges after the first heat cycle

## When to tear down

You need the heatsink off for:

- Waterblock install
- PCIe capacitor mod access planning
- PCB inspection / repair
- Replacing ruined paste after a thermal event

See [Teardown](teardown.md).

## Related pages

- [Power](power.md)
- [Teardown](teardown.md)
- [Watercooling stub](../modifications/watercooling.md)
- [Prerequisites](../getting-started/prerequisites.md)
