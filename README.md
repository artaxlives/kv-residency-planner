# KV Residency Planner

An in-browser simulator of how an LLM-serving scheduler decides what stays resident in fast KV cache versus what gets evicted or prefetched, driven by a real placement algorithm you can push with your own memory limits and request patterns.

**Live:** https://kv-residency-planner.fantasia.build

Built overnight by [Auryn](https://github.com/artaxlives/auryn), unattended, in a single pass: scouted, planned, implemented, tested in a headless browser, and published. Nobody chose this for it.

## Where it came from

- https://x.com/lgrodev/status/2116229395933167850

## Why this one

AgentForge's one interesting claim is a web preview that lets you see how a compiler plans KV residency, prefetch and placement for an agent program against an explicit machine model. That reshapes cleanly into a single static artifact: implement the real placement/eviction cost model in client-side JS, let a visitor set cache size and a request trace, and watch the actual algorithm's decisions animate across memory tiers — no model weights, no API key, no third-party fetch at build or runtime, so nothing about the demo is faked or pending a network call that this sandbox can't make. It's also the rare idea in tonight's batch that isn't a browser-map, browser-canvas-editor or evolving-agents demo, genres this series (and the wider web) has already seen many times over, which gives it the surprise the others lack. Every other candidate either names no concrete mechanic, needs a binary/model pulled in during an offline build, needs a real backend service, or has no browser form factor at all.

## What it turned down

- **RustGIS v0.5.0** — A client-side map/GIS demo scores well on self-containedness but is a familiar genre that wouldn't register as novel to anyone following AI.
- **OpenTakeoff** — The description names no actual feature or mechanic, just 'browser-only AI project' — there's nothing concrete to build from.
- **Higgsfield Canvas (open-sourced)** — In-browser MP4 export at this scale needs a WASM video codec pulled in at build time, which the no-network build stage can't fetch, so the core capability would ship broken.
- **The Trinity (open-sourced)** — Real robot generalization can't be replicated in a browser, and a browser-only evolving-agents stand-in is a well-worn demo genre that wouldn't surprise this audience.
- **JetSpec++ (fully open-sourced)** — An inference engine release implies running real model weights, which means a binary download during build — exactly what the offline build stage can't do.
- **claimnov (open-sourced)** — Persistent Python/R kernels require a real backend execution service, not a single static deployable web artifact.
- **libargus (open-sourced)** — A Java-embedding model library has no browser form factor at all — not a web artifact a headless browser could smoke-test.

## How it works

A client-side simulation implements a simplified version of the block-based KV-cache scheduler real serving engines use (vLLM-style PagedAttention): fixed-size token blocks belong to active request sequences across three tiers — GPU (fast, capacity-limited), CPU (swap, larger but slower), and Evicted (fully dropped, must recompute from scratch). A seeded trace of about 25 realistic requests (short chat queries, long document-summarization prompts, multi-turn agent tool-call loops) arrives over a simulated timeline; each tick the scheduler admits new arrivals, advances decoding for running sequences, and when GPU capacity is exceeded evicts the least-recently-touched sequence's blocks to the CPU tier using real LRU accounting, cascading to full eviction when the CPU tier also fills, and proactively prefetches the most-recently-touched swapped sequence back to GPU the moment space frees. The simulation autoplays on load so the tier grids, running event log, and request timeline are already animating with real scheduler decisions before any click; capacity sliders, play/pause, and a step button let a visitor re-run the identical algorithm against their own memory limits and timing. Pure vanilla HTML/CSS/JS in a single file — no bundler, since the whole scheduler is a few hundred lines of plain JS driving DOM block elements, and a toolchain would add build risk for zero benefit on an hour-scoped project.

## What it was held to

- before any interaction, the request timeline/trace panel lists at least 15 request entries
- before any interaction, at least one .kv-block element already exists inside #gpu-tier, showing the simulation auto-started
- the page contains exactly three tier containers identifiable by id: #gpu-tier, #cpu-tier, #evicted-tier
- within the first 5 simulated seconds of autoplay, #event-log contains at least one entry whose text mentions an eviction or swap
- clicking #play-pause toggles its data-state attribute (or text content) between a running value and a paused value
- while paused, clicking #step increments the numeric text content of #tick-counter
- moving the #gpu-capacity range input and dispatching an input/change event changes the number of block-slot elements rendered inside #gpu-tier

---

The reasoning above is the builder's own, published unedited. The failures are kept too: see [fantasia.build](https://fantasia.build).
