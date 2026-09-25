# CMP 170HX Documentation

[![Documentation](https://img.shields.io/badge/docs-live-blue.svg)](https://it-still-boots.github.io/cmp-170hx-docs/)

Documentation for the NVIDIA CMP 170HX: hardware, host BIOS, the Linux unlock, cooling, and decode numbers from one unlocked 64 GiB card.

**Read the docs:** [https://it-still-boots.github.io/cmp-170hx-docs/](https://it-still-boots.github.io/cmp-170hx-docs/)

## What's included

- **Getting started** — SKUs (`10de:20c2` → 64 GB, `10de:2082` → 40 GB), prerequisites, bring-up order
- **Hardware** — specifications, EPS power, air cooling, waterblocks, teardown summary
- **PCIe capacitor mod** — 24× 0402 0.22 µF, width only (Gen2 is software)
- **Host BIOS** — Above 4G, Secure Boot, Resizable BAR, and what each one actually changes
- **Linux unlock** — cmpunlocker on nvidia-open 610.43.02 / 610.43.03
- **Performance** — 1,603 decode points, 16 models, vLLM / llama.cpp / SGLang
- **Troubleshooting and a one-page reference**

## Building locally

```bash
pip install -r requirements.txt
mkdocs serve
```

The site is at http://127.0.0.1:8000/

```bash
mkdocs build
```

## Sources

- [170th Street](https://170th-street.gitbook.io/hx) and [amoghmunikote/170th-Street](https://github.com/amoghmunikote/170th-Street)
- [amoghmunikote/cmpunlocker](https://github.com/amoghmunikote/cmpunlocker) and [bayley/cmpunlocker](https://github.com/bayley/cmpunlocker)
- [Consensus-Protocol/cmp170hx](https://github.com/Consensus-Protocol/cmp170hx/wiki)
- [ServeTheHome unlock thread](https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/)

## Contributing

Edit a page with the "Edit this page" link, or open a pull request on [It-Still-Boots/cmp-170hx-docs](https://github.com/It-Still-Boots/cmp-170hx-docs). Useful additions are a BIOS menu path with the firmware version, a `LnkSta` line before and after the capacitor mod, and a temperature log under load.

Built with [MkDocs](https://www.mkdocs.org/) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).
