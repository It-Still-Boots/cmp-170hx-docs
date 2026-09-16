# Performance Overview

TODO: Publish It Still Boots bench tables and reproduce steps.

Lab targets already noted on Home (CSV / methodology TBD):

- Unlocked CMP 170HX with ~**64 GB** usable HBM
- Qwen3.8-27B dense ≈ **212 tok/s** on a DFlash / vLLM-class stack
- Context-depth sweep in the roughly **219 / 181 / 141 / 109 / 77** class (~**61%** drop from short to long)

Do not invent CSV files here. Reproduce section lands in a later pass.
