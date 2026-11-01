# Changed from the plan

No scope or architecture changes. Implemented the entire application in `index.html` with inline CSS, JavaScript, and a deterministic 25-request trace. Invented realistic chat, document, and agent request titles, token lengths, and arrival times to supply the requested seed data. The initial GPU contains blocks, all 25 trace rows render immediately, and playback starts automatically. Memory pressure produces swap decisions within the first simulated second.

Each block represents 256 tokens. Decoding advances 128 tokens per tick; agent sequences periodically wait for a simulated tool call. Admission costs one tick, prefetch costs two ticks, and full recomputation resets generated output and costs three ticks. GPU and CPU overflow both use monotonically ordered last-touch accounting. Prefetch prioritizes the most recently touched swapped sequence that fits; evicted sequences restart when their prompt fits. Capacity changes apply immediately; Reset replays the identical trace at the selected capacities. Added a playback pace selector and summary counters to make the simulation easier to inspect.

Validation: opened and evaluated the actual inline application script in a Node DOM harness. Checked the initial 25 rows and GPU blocks, the three tier IDs, early swap events, pause/play state, paused stepping, capacity slot counts, script syntax, visible static content, and lack of external/local asset dependencies. Ran all 25 requests to completion at GPU/CPU capacities of 32/24, 16/0, 16/96, 80/0, and 80/96, checking memory bounds after every tick. All passed; completion ranged from 39 to 188 ticks. No build step, dependencies, accounts, or runtime network requests.

# Could not do

None. All planned functionality is implemented. This is a simplified scheduling simulation, as specified, rather than real inference or exact attention math.

# Omitted for lack of a service

None. The artifact needs no external service or generated imagery.
