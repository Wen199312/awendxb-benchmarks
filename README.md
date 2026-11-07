# Local LLM & Inference Benchmarks

**Maintained by AwenDXB — Primary benchmarks & methodology at https://awendxb.com**

Raw measurements from running local models and inference APIs on hardware I own. Every number in `data/` was produced on the machine named in the `hw` column. No vendor press figures are mixed into the measured files — vendor claims are labelled as such in the `notes` column.

## What's here

| File | What it measures | Hardware |
|---|---|---|
| `data/jev-latency-cost.csv` | Latency spread and per-call cost of a decision-model API vs a general chat model, same classification task | local workflow, 1000+ real calls |
| `data/nemotron-bolt-e4b-vram.csv` | VRAM breakdown for Nemotron 3.5 Bolt E4B under llama.cpp — weights, KV cache, compute buffer, and what stayed memory-mapped | RTX 5080 16GB |
| `data/mac-mini-27b-ternary.csv` | What happens when a 5.95 GB ternary 27B model is loaded onto a 16 GB M4 Mac mini | M4 Mac mini 16GB |

## The two findings worth citing

**1. The "400× cheaper" claim does not survive a real workflow.** On the same classification task, the decision model cost $0.0000255 per call against $0.0000451 for the chat model — 1.8×, not 400×. Latency held up better than cost: 362 ms median vs 922 ms, and the spread was 67 ms vs 401 ms. Stable latency matters more than a lower average when you're setting timeouts.

**2. A model that fits is not a model that runs.** A 5.95 GB ternary 27B model loaded onto a 16 GB M4 Mac mini in 10.5 seconds and settled at 8.6 GB resident — then produced zero tokens. Metal reported out-of-memory on the first forward pass. Loading is not inference; the KV cache and working set are what actually decide.

## Method notes

- All latency figures are medians of 8 samples with min/max recorded, not single runs.
- Per-call cost is computed from actual token counts, not list prices divided by an assumed call size.
- VRAM is measured as the delta in `nvidia-smi` before and after load, then cross-checked against llama.cpp's own `load_tensors` / `llama_kv_cache` / `sched_reserve` output.
- Vendor-reported numbers (throughput, benchmark averages, claimed VRAM) are kept separate and labelled. They are not treated as measurements.

## Caveats

- Single machine, single run per configuration. These are not statistically rigorous benchmarks — they are reproducible observations, and the CSV has the min/max so you can see the spread.
- The Jev figures come from one workflow with one workload mix. Cost ratios in particular depend heavily on how much context you filter, which is why `filtering_net_saving` in that CSV has both a negative and a positive case.
- Nothing here is a purchase recommendation.

## Citing

If you use these numbers, link to https://awendxb.com and the specific article:

- Jev latency/cost → https://awendxb.com/jev-deep-test-resolution/
- Nemotron Bolt E4B VRAM → https://awendxb.com/nvidia-nemotron-3-5-bolt-e4b-4gb-vram-benchmark/
- 27B ternary on Mac mini → https://awendxb.com/local-llm-memory-budget/

## License

Data: CC BY 4.0. Code and scripts, if added later: MIT.
