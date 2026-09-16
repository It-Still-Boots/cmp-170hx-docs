# Common Issues

Failure modes that show up on unlocked CMP 170HX hosts. Keep cooling honest before you debug software. Each section is symptom → likely cause → first fix.

## Card not seen / wrong PCI ID

**Symptoms:** Empty `lspci` NVIDIA line, or device IDs that look like an A100 (`20b0` / `20b2` / `20b5` / `20f1` class) when you expected a 170HX.

**Likely causes:** Bad seat, no EPS power, host slot dead, you bought the wrong board, Secure Boot / early driver bind confusion.

**First checks:**

- Reseat the card and the **8-pin CPU/EPS-style** adapter.
- Confirm PSU 12 V budget.
- `lspci -nn | grep -i nvidia` on a stock driver. Expect `10de:20c2` (8 GB) or `10de:2082` (10 GB), subsystems `1585` / `1557`.
- Try another slot. Clear CMOS if the board POSTs without the card but hangs with it.

## Memory still 8 GB / 10 GB after unlock

**Symptoms:** Installer exited 0, reboot done, `nvidia-smi` still shows **8192** or **10240 MiB**.

**Likely causes:** Wrong module winning the load order; Secure Boot still on; warm reboot only; wrong `--profile`; driver not 610.43.02/03.

**First checks:**

```bash
modinfo -n nvidia
mokutil --sb-state
nvidia-smi
```

On Ubuntu-class hosts, DKMS under `updates/dkms/` often beats `updates/cmpunlocker/`. Point depmod / module priority at the cmpunlocker updates path (ServeTheHome unlock thread and cmpunlocker issues cover the exact override). Cold power off, then boot. Confirm `--profile=8gb` vs `10gb` matches the SKU.

## Gen stuck at 1 after unlock

**Symptoms:** Memory unlocked, but `LnkSta` still **2.5 GT/s**.

**Likely causes:** Build without Gen2 patches; host slot forced Gen1; ASPM / slot quirks; width still x4 (expected without caps).

**First checks:** Confirm your cmpunlocker commit actually includes Gen2 (README / changelog). Leave host PCIe gen on Auto. Width staying at **x4** without the [capacitor mod](../modifications/pcie-capacitor-mod.md) is normal. Gen and width are independent.

## DKMS / module priority fighting cmpunlocker

**Symptoms:** Unlock works once, then a kernel or nvidia DKMS rebuild restores stock behavior; `modinfo -n nvidia` leaves the cmpunlocker directory.

**Likely causes:** DKMS rebuild overwrote or outranked patched modules.

**First checks:** After every kernel or nvidia package update, re-run the unlock install or re-assert the updates/cmpunlocker priority. Snapshot the rootfs before upgrades. Pin nvidia packages when you can.

## Thermal abort / runaway

**Symptoms:** Sudden power-off, clocks collapse, temps climb through the **70s °C** without plateau under load.

**Likely causes:** Passive sink in still air; dry waterblock; power limit raised to ~300 W before cooling is proven.

**First checks:** Aim high-static-pressure fans through the fins, or fill the loop. Lab hard stop near **~80 °C**. Dry-run without coolant: power off within ~**5 minutes**. See [Air Cooling](../hardware/air-cooling.md) / [Water Cooling](../hardware/water-cooling.md).

## Waterblock short from missing pads

**Symptoms:** Card dead after waterblock install; shorts on 12 V / ground; host refuses to POST with the card.

**Likely causes:** Empty IC footprints left bare under block pillars; backplate installed without the saved PCIe bracket spacing.

**First checks:** Do not keep powering a suspected short. Tear down, inspect pads, cover every empty footprint the pillars can reach, verify bracket spacing. Procedure detail: [170th Street watercooling](https://170th-street.gitbook.io/hx/modifications/watercooling-installation) and [Water Cooling](../hardware/water-cooling.md).

## Driver upgrade broke unlock

**Symptoms:** Worked on 610.43.02/03; after an nvidia package bump, modules fail to build or unlock no longer applies.

**Likely causes:** Unsupported driver major/minor; patched modules rebuilt against the wrong tree.

**First checks:** Roll back to **610.43.02** or **610.43.03**, reinstall cmpunlocker, cold reboot. Cache those packages locally. Read the current cmpunlocker README before chasing newer drivers.

## Related pages

- [Unlock Overview](../linux-unlock/unlock-overview.md)
- [Quick Start](../getting-started/quick-start.md)
- [Host BIOS](../bios/host-bios.md)
- [Air Cooling](../hardware/air-cooling.md)
- [Water Cooling](../hardware/water-cooling.md)

## References

References and further info from:

- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx/wiki
- https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/
- https://170th-street.gitbook.io/hx/modifications/watercooling-installation
- https://170th-street.gitbook.io/hx/modifications/pcie-capacitor-mod
