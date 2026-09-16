# Community

Upstream reverse engineering, unlock tooling, and board-level mods came from other people. Use this page to find them, ask questions where they already hang out, and contribute fixes back to these docs. Lab numbers live under [Performance](../performance/overview.md).

Ask questions where the upstream projects already hang out, or open a GitHub issue / PR on this repo.

## Where to ask

| Topic | Best place |
|-------|------------|
| Unlock install failures, module priority, driver pin | [amoghmunikote/cmpunlocker](https://github.com/amoghmunikote/cmpunlocker) issues |
| Register-level / Falcon / geometry deep dives | [Consensus-Protocol/cmp170hx wiki](https://github.com/Consensus-Protocol/cmp170hx/wiki) and that project's tracker |
| PCB mods, waterblock pads, teardown | [170th Street](https://170th-street.gitbook.io/hx) / [amoghmunikote/170th-Street](https://github.com/amoghmunikote/170th-Street) |
| Unlocked measurements, “is this an A100?” | [ServeTheHome Forums thread](https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/) |
| LLM decode reports on 170HX hardware | [Localmaxxing reports](https://www.localmaxxing.com/en/reports) and [hardware hub](https://www.localmaxxing.com/en/hardware) |
| Typos / lab-note gaps in *this* site | [It-Still-Boots/cmp-170hx-docs](https://github.com/It-Still-Boots/cmp-170hx-docs) issues and PRs |

## How to contribute to these docs

1. Fork [It-Still-Boots/cmp-170hx-docs](https://github.com/It-Still-Boots/cmp-170hx-docs).
2. Edit Markdown under `docs/`. Keep the lab-note voice: concrete numbers, full sentences, no marketing filler.
3. Run `mkdocs build --strict` locally before you open a PR.
4. Attribute upstream when you paraphrase the hardware guides, cmpunlocker, or the Consensus wiki. Do not paste walls of GitBook.
5. If a step is not verified in these docs, say so and link the upstream procedure.

Useful PRs: BIOS menu paths for specific boards, verified `LnkSta` before/after caps, cooling photos with temperatures, and corrections when a driver pin or part number moves.

## Upstream and community links

- [170th Street docs](https://170th-street.gitbook.io/hx)
- [amoghmunikote/170th-Street](https://github.com/amoghmunikote/170th-Street)
- [amoghmunikote/cmpunlocker](https://github.com/amoghmunikote/cmpunlocker)
- [bayley/cmpunlocker](https://github.com/bayley/cmpunlocker) (P2P-oriented fork)
- [Consensus-Protocol/cmp170hx](https://github.com/Consensus-Protocol/cmp170hx) · [wiki](https://github.com/Consensus-Protocol/cmp170hx/wiki)
- [ServeTheHome Forums: unlocked measurements](https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/)
- [Localmaxxing hardware](https://www.localmaxxing.com/en/hardware)
- [Localmaxxing reports](https://www.localmaxxing.com/en/reports)
- This docs repo: [It-Still-Boots/cmp-170hx-docs](https://github.com/It-Still-Boots/cmp-170hx-docs)

## References

References and further info from:

- https://170th-street.gitbook.io/hx
- https://github.com/amoghmunikote/170th-Street
- https://github.com/amoghmunikote/cmpunlocker
- https://github.com/bayley/cmpunlocker
- https://github.com/Consensus-Protocol/cmp170hx/wiki
- https://forums.servethehome.com/index.php?threads/cmp-170hx-unlocked-and-measured-164-tflops-fp16-64gb-verified-and-how-to-tell-one-from-an-a100.56137/
- https://www.localmaxxing.com/en/hardware
- https://www.localmaxxing.com/en/reports
- https://github.com/It-Still-Boots/cmp-170hx-docs
