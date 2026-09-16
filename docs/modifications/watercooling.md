# Watercooling

Closed-loop and block options for putting a CMP 170HX on a desk without a rack full of screamers. Stock cooling is a **passive** server sink. For daily unlocked workloads, plan on water (AIO kit or custom loop) or serious directed airflow.

Install warnings and pad procedure still live on [170th Street Watercooling Installation](https://170th-street.gitbook.io/hx/modifications/watercooling-installation). Read that before you pull the stock sink.

!!! danger "Empty IC footprints"
    Unpopulated IC footprints under the block **must** get thermal pads. Metal pillars on the block will short bare copper if a footprint is left open. This is the most common way people kill a card during waterblock install.

!!! warning "Thermal runaway"
    GA100 leakage climbs with temperature. Dry-run without coolant only for a short bring-up, then power off within about **five minutes**. Treat temperatures above ~**80 °C** as an emergency stop.

## Bykski AIO (FormulaMod)

[Bykski B-FRD-TESLA-A100](https://www.formulamod.com/Bykski-AIO-Cooler-for-NVIDIA-Tesla-A100-40GB-and-A30-p6189666.html) is a full-cover AIO kit aimed at **Tesla A100 40GB**, **A30 24GB**, and **CMP 170HX**. Nickel-plated copper cold plate, stainless / POM assembly, anodized aluminum backplate that stays out of the coolant, G1/4 ports if you later series something into the loop.

![Bykski B-FRD-TESLA-A100 AIO with 120mm, 240mm, and 360mm radiator options](../assets/watercooling/bykski-aio-hero.jpg)

![Bykski AIO radiator size options 120 / 240 / 360 mm](../assets/watercooling/bykski-aio-sizes.jpg)

### Radiator options

FormulaMod lists the kit with **120 mm**, **240 mm**, and **360 mm** radiators (same cold plate, different rad / fan count):

| Radiator | Fans (typical) | Notes |
|----------|----------------|-------|
| **360 mm** | 3× 120 mm | Default recommendation for an unlocked 170HX at 250–300 W. 170th Street’s qualitative datapoint (~**45 °C** at ~**180 W** on a 360 mm with fans at minimum) sits in this class. |
| **240 mm** | 2× 120 mm | Possible in a tight case if airflow on the rad is strong and you watch temps under real LLM / CUDA load. Leave headroom. |
| **120 mm** | 1× 120 mm | **Most likely not enough** for sustained unlocked load on this card. Fine for photos and maybe idle / light probes; treat it as undersized for 24/7 inference. |

Some listings also show a thicker **360 mm** dual-card row SKU. Confirm the SKU text on FormulaMod before you pay.


For a **custom loop** (your own pump / rad / fittings), the community-validated cold plate is still Bykski **N-TESLA-A100-X-V2** (newer stock may show as **N-TESLA-A100-40G-SR**). Do **not** buy the **80G** A100 block or the older non-V2 acrylic revision. Block-only listing: [FormulaMod N-TESLA-A100-X-V2](https://www.formulamod.com/Bykski-GPU-Block-For-Nvidia-Tesla-A100-40GB-Nvidia-CMP-170HX-Nvidia-Tesla-A30-24G-High-Heat-Resistance-Material-POM-Full-Metal-Construction-With-Backplate-Full-Cover-GPU-Water-Cooling-Cooler-Radiator-Block-N-TESLA-A100-X-V2-p3765067.html).

## Astralcooler AIO (FormulaMod)

[Astralcooler server GPU AIO](https://www.formulamod.com/Astralcooler-All-in-one-server-GPU-cooler-radiator-360480-compatible-with-NVIDIA-CMP-170HX-p7185402.html) is another A100-family full-cover kit sold explicitly for **CMP 170HX / A100 40GB / A30 24GB**. Oxygen-free copper baseplate, nickel plating, all-metal chassis story, pump(s) in the tubing.

![Astralcooler AIO for CMP 170HX / A100 40GB / A30](../assets/watercooling/astralcooler-aio-hero.jpg)

![Astralcooler kit compatibility and radiator options](../assets/watercooling/astralcooler-compat.jpg)

### One card vs two cards

| Kit | What you get | Use when |
|-----|--------------|----------|
| **360 mm** | **1** cold plate + 360 mm radiator | Single CMP 170HX |
| **480 mm** | **2** cold plates + 480 mm radiator | Two CMP 170HX (or two cards in that PCB family) on one rad |

Pick the SKU that matches how many boards you are cooling. A dual-block 480 mm kit on one card wastes money and fittings; a single 360 mm kit will not cool two unlocked cards.

## Shyrrik waterblock (AliExpress / Taobao)

**Shyrrik** (listing spellings vary) sells a full-cover **block + backplate** for the same PCB family (A100 40GB / A30 / CMP 170HX) on AliExpress:

- [AliExpress listing](https://www.aliexpress.com/item/1005012960080788.html)

![Shyrrik A100 / CMP 170HX full-cover waterblock](../assets/watercooling/shyrrik-waterblock.jpg)

This is a **cold plate**, not a complete AIO by itself. You still need pump, radiator, fittings, and coolant (or an AIO rad kit sold with that block).

On **Taobao**, the same Shyrrik-style block often shows up bundled as an **AIO** (block + pump + radiator). Search the Chinese listings for A100 / 170HX waterblock AIO kits that use this plate if you want a one-box loop from domestic China sellers. Always match the PCB photo in the listing to your board before ordering.

## Corsair CPU AIO adapted (240 mm)

There is a **240 mm** kit sold as a **Corsair-style CPU AIO adapted** onto an A100 / 170HX full-cover plate. It shows up on **Xianyu**, **AliExpress**, and **Taobao** in China-facing channels:

- [AliExpress listing](https://www.aliexpress.com/item/1005012990720684.html)

![Corsair-adapted 240mm AIO on CMP 170HX / A100 waterblock](../assets/watercooling/corsair-adapted-aio-240.jpg)

Treat this as a **240 mm** thermal budget (same caution as the Bykski 240 mm row above). Verify seller photos against your PCB, check pump / fitting quality, and log temps under unlock-level load before you trust it overnight.

## Quick pick

| Goal | Start here |
|------|------------|
| One unlocked card, least fuss | Bykski **B-FRD-TESLA-A100** AIO, **360 mm** |
| Two cards, one rad | Astralcooler **480 mm / 2 blocks** |
| Custom loop parts | Bykski **N-TESLA-A100-X-V2** / **40G-SR** block |
| China-market block / AIO hunt | Shyrrik block on AE; Taobao AIO bundles; Corsair-adapted **240 mm** if you accept the smaller rad |

## Install pointers

1. Finish [teardown](../hardware/teardown.md) to bare PCB before any block goes on.
2. Pad every empty footprint the pillars can touch.
3. Seat the 8-pin / EPS power cable path before you fully torque the block (rigid cables fight a fully screwed plate).
4. Keep the original PCIe bracket when fitting the backplate for correct spacing.
5. Pressure-test ≥15 minutes before coolant fill (per 170th Street).

Deep procedure: [170th Street Watercooling Installation](https://170th-street.gitbook.io/hx/modifications/watercooling-installation). Hardware cooling overview: [Cooling](../hardware/cooling.md).
