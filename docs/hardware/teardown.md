# Teardown

Summary of what a CMP 170HX teardown involves. This page does **not** invent screw counts or motion paths beyond the linked procedure.

**Step-by-step with photos:** [Teardown Guide](https://170th-street.gitbook.io/hx/hardware/teardown-guide) · [GitHub mirror](https://github.com/amoghmunikote/170th-Street)

## Why tear it down

- Preparing for the Bykski **N-TESLA-A100-X-V2** waterblock
- Inspecting the PCB against A100 40GB PCIe reference designators
- Access for the [PCIe capacitor mod](../modifications/pcie-capacitor-mod.md)
- Repair or thermal-paste refresh after a heat event

## Before you start

!!! warning "Read the full guide first"
    The teardown guide calls out steps that confuse even experienced techs (power-cable extraction and sliding the PCB out of the backplane). Watch the linked reference videos before you touch steps that free the board or the heatsink.

Tools called out there:

- Small Phillips screwdriver
- Metric hex wrench set (do not trust the wrong-size hex that ships with some waterblocks)
- Plastic spudger
- Clean, flat, static-safe surface
- Organized storage for spring-loaded screws and washers (they are not interchangeable with waterblock kit screws)

## High-level sequence (summary only)

The teardown guide’s numbered flow, paraphrased:

1. Remove the **four** PCIe bracket screws; **save the bracket** for later waterblock/backplate spacing.
2. Leave the opposite-side mounting bracket alone.
3. Remove the **ten** screws on the PCB back side.
4. Open the front cooler cover; the PCIe bracket comes away with it and exposes the copper sink.
5. Unscrew the power-cable bracket.
6. Free the stiff power cable from the backplane with a spudger (do not yank).
7. Slide the PCB horizontally so the PCIe connector clears the backplane slot, then lift. This is the step that traps people.
8. Remove the **four** spring-loaded heatsink screws; keep the **four** washers.
9. Separate the heatsink. Paste often acts like glue; spudger technique with the card rotated so the sink cannot fall onto components.
10. You now have a bare PCB ready for inspection, capacitor work, or waterblock prep.

For torque sense, photos, and the video embeds, open the Teardown Guide linked above. If a step is ambiguous, stop and use that page.

## After separation

| Next goal | Go here |
|-----------|---------|
| Waterblock | [Water Cooling](water-cooling.md) |
| Lane caps | [PCIe capacitor mod](../modifications/pcie-capacitor-mod.md) |
| Reassemble air cooler | Reverse the guide; renew paste; confirm spring washers return to the original screws |

!!! danger "Waterblock pad reminder"
    Empty IC footprints under block pillars need thermal pads before the block seats. See [Water Cooling](water-cooling.md).

## Related pages

- [Air Cooling](air-cooling.md)
- [Water Cooling](water-cooling.md)
- [Specifications](specifications.md)
- [PCIe capacitor mod](../modifications/pcie-capacitor-mod.md)

## References

- https://170th-street.gitbook.io/hx/hardware/teardown-guide
- https://github.com/amoghmunikote/170th-Street
- https://170th-street.gitbook.io/hx/modifications/watercooling-installation
- https://170th-street.gitbook.io/hx/modifications/pcie-capacitor-mod
