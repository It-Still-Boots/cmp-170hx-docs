# Host BIOS

Firmware settings that matter when you seat a CMP 170HX and later load patched nvidia-open modules. Menu names differ by vendor. Search for the strings below.

This page does not list every board. If you find the path on yours, send the firmware version and the menu path in a PR.

## Settings to hunt

| Setting (common labels) | Target | Why |
|-------------------------|--------|-----|
| **Above 4G Decoding** / Above 4G Memory Mapping | **Enabled** | Required when BAR1 is grown from the stock 64 MB window toward 64 GB. A window that size does not fit under 4 GB. The 64 GB / 40 GB framebuffer size is a separate register change and does not depend on this. Many boards also hide Resizable BAR until Above 4G is on. |
| **Secure Boot** | **Disabled** | cmpunlocker installs unsigned patched kernel modules. Secure Boot will refuse them. |
| **Resizable BAR** / Re-Size BAR / Smart Access Memory | Enable if present | Stock BAR1 is 64 MB. Growing it needs the host feature enabled, Above 4G on, and a kernel fixup that programs the card before PCI enumeration. The BIOS toggle alone does not move this card off 64 MB. On many boards the option stays greyed out until Above 4G is on and CSM is off. |
| **CSM** / Compatibility Support Mode / Legacy Boot | Prefer **Disabled** (UEFI-only) when you want ReBAR | CSM on often blocks Above 4G / ReBAR. If the host must stay Legacy for some other card, document that tradeoff. |
| **PCIe Generation** (slot / root port) | Auto, or Gen2+ allowed | Stock endpoint trains Gen1 until the software Gen unlock. Do not force Gen1-only on the slot if you want Gen2 after unlock. |
| **ASPM** / PCIe Power Management | Leave Auto | No CMP 170HX case in these notes required turning ASPM off. If `LnkSta` flaps on your board, try Disabled and record the board. |

!!! tip "Where menus hide"
    Look under **Advanced → PCI Subsystem**, **Chipset → PCI Express**, **NB Configuration**, or **Server Mgmt / PCIe**. Gigabyte often puts Above 4G under Settings → IO Ports. Supermicro puts related knobs under Advanced → PCIe/PCI/PnP.

## Practical bring-up checklist

1. Seat the card in a full **x16 mechanical** slot. Stock still trains at **x4**. An x16 slot is what lets the link go wider after the missing AC-coupling capacitors are fitted.
2. Enter setup (Del / F2 / F11 depending on OEM).
3. Enable **Above 4G Decoding**. Save, reboot, re-enter if ReBAR was missing.
4. Disable **Secure Boot**. Leave the platform keys in place. Clearing them is a separate step and can stop other signed operating systems on that machine from booting.
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

Before unlock: Gen1 **x4**, BAR1 **64 MB**, framebuffer **8192 MiB** on `10de:20c2` or **10240 MiB** on `10de:2082`. After a successful unlock those become **65536 MiB** and **40960 MiB**. Link speed is **5.0 GT/s** when the cmpunlocker build includes Gen2. Width stays **x4** until the capacitor mod. BAR1 stays 64 MB unless you also apply the early BAR1 resize.

## Related pages

- [Prerequisites](../getting-started/prerequisites.md)
- [Quick Start](../getting-started/quick-start.md)
- [Unlock Overview](../linux-unlock/unlock-overview.md)

## References

- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx/wiki
- https://170th-street.gitbook.io/hx
- https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/
