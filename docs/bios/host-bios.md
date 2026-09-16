# Host BIOS

Firmware settings that matter when you seat a CMP 170HX and later unlock it with patched nvidia-open modules. Menu names differ by vendor. Hunt for the strings below rather than assuming an ASUS / Supermicro layout.

It Still Boots has not published a board-by-board cookbook for every lab host yet. Capture your exact paths when you find them and send a PR against this page.

## Settings to hunt

| Setting (common labels) | Target | Why |
|-------------------------|--------|-----|
| **Above 4G Decoding** / Above 4G Memory Mapping / Large BAR Support (address space) | **Enabled** | Gives the firmware enough PCIe MMIO room for large BAR / full VRAM mapping once unlock widens BAR1. Required on many boards before Resizable BAR even appears. |
| **Secure Boot** | **Disabled** | cmpunlocker installs unsigned patched kernel modules. Secure Boot will refuse them. |
| **Resizable BAR** / Re-Size BAR / Smart Access Memory | Enable if present | Stock card advertises ReBAR with a **64 MiB** limit. Unlock tooling widens BAR1 behavior; the host still needs the feature path open. On many boards ReBAR stays greyed out until Above 4G is on and CSM is off. |
| **CSM** / Compatibility Support Mode / Legacy Boot | Prefer **Disabled** (UEFI-only) when you want ReBAR | CSM on often blocks Above 4G / ReBAR. If the host must stay Legacy for some other card, document that tradeoff. |
| **PCIe Generation** (slot / root port) | Auto, or Gen2+ allowed | Stock endpoint trains Gen1 until the software Gen unlock. Do not force Gen1-only on the slot if you want Gen2 after unlock. |
| **ASPM** / PCIe Power Management | Leave Auto unless debugging link flaps | Some hosts misbehave with aggressive ASPM on mining-era endpoints. If LnkSta flaps, try ASPM Disabled as an experiment and note the board. |

!!! tip "Where menus hide"
    Look under **Advanced → PCI Subsystem**, **Chipset → PCI Express**, **NB Configuration**, or **Server Mgmt / PCIe**. Gigabyte often puts Above 4G under Settings → IO Ports. Supermicro puts related knobs under Advanced → PCIe/PCI/PnP.

## Practical bring-up checklist

1. Seat the card in a full **x16 mechanical** slot. Stock trains at **x4**; the capacitor mod later wants all sixteen card-side traces intact.
2. Enter setup (Del / F2 / F11 depending on OEM).
3. Enable **Above 4G Decoding**. Save, reboot, re-enter if ReBAR was missing.
4. Disable **Secure Boot** (and Clear Secure Boot Keys if the menu requires it before modules will load).
5. Enable **Resizable BAR** when the option exists.
6. Confirm the slot is not forced to Gen1-only.
7. Save and boot Linux with a second GPU, iGPU, or BMC console. The 170HX has no display outputs.
8. Record the exact menu path and firmware version in your lab notes / a PR to this page.

## After OS install

Cross-check from Linux once drivers load:

```bash
sudo lspci -s <bus:dev.fn> -vvv | grep -E 'LnkCap|LnkSta|Region|BAR'
nvidia-smi -q | grep -i -E 'FB Memory|BAR1'
```

Stock expectation before unlock: Gen1 **x4**, memory **8192** or **10240 MiB**, BAR1 still small. After a successful unlock: memory **65536** or **40960 MiB**, Gen2-class link speed when your cmpunlocker build includes Gen2, width still **x4** until the capacitor mod.

## Related pages

- [Prerequisites](../getting-started/prerequisites.md)
- [Quick Start](../getting-started/quick-start.md)
- [Unlock Overview](../linux-unlock/unlock-overview.md)

## References

References and further info from:

- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx/wiki
- https://170th-street.gitbook.io/hx
- https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/
