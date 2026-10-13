# Lessons: minbpe

Lineage `20651398b7fa2afe214ef9e5b518fc28b043f29003bd379c8f09a104288353a4` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 17 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:encode_ir

- Attempts: 7 (5 accepted, 1 rejected, 1 no candidate)
- Best accepted effect: 34.31% (ratio 0.6569), episode `4297b89143e7`
- Accepted effects: 34.31%, 12.19%, 3.74%, 1.35%, 1.12%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3064 USD (over 7 attempts with a recorded cost)

## Target perf:train_ir

- Attempts: 10 (7 accepted, 1 rejected, 2 no candidate)
- Best accepted effect: 15.17% (ratio 0.8483), episode `0bada18988c2`
- Accepted effects: 15.17%, 14.04%, 6.05%, 4.39%, 3.36%, 1.94%, 1.38%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.2393 USD (over 10 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Tried edits in minbpe/regex.py and minbpe/basic.py after reading minbpe/base.py lines 1-208, minbpe/regex.py lines 1-233, minbpe/basic.py lines 1-95, and lineage_bench.py lines 1-107. The target was perf train_ir.
> Evaluation 1 was rejected: train_ir parent 2600031455, candidate 2616236728, ratio 1.0062. Evaluation 3 was rejected: ratio 1.0768. Evaluation 5 was rejected: ratio 0.9939. Evaluation 7 was rejected: ratio 0.9937. Every build was base ok and candidate ok. Equivalence was identical outputs.
> No measured candidate met the required train_ir improvement before the evaluation limit was reached. The best accepted-equivalent attempt improved train_ir by only about 0.6%, below the 1.0% threshold. No candidate was submitted.
> Next try a change outside these edited lines, still measured by perf train_ir. If it misses required 0.9900, stop earlier.
>
> 2026-10-13, episode `64e0edb30661` (no candidate)

> Changed minbpe/regex.py RegexTokenizer.encode_ordinary and minbpe/basic.py BasicTokenizer.encode. Inlined the encode-time merge step after the lowest-rank adjacent pair is selected. Kept the selected pair as scalar values.
> Own sandbox evaluation 1 on perf encode_ir: outcome: accepted. Guard: ok, 2 files, 46 lines. Build: base ok, candidate ok. Equivalence: identical outputs. train_ir parent 2570885327, candidate 2570714396, ratio 0.9999. encode_ir parent 1000196880, candidate 989394437, ratio 0.9892.
> The helper call and pair tuple construction were work inside every encode merge step. Removing them did not change scan order or merge results in this run.
> Submitted the perf candidate on encode_ir. Status is queued. Independent replays come next.
>
> 2026-10-13, episode `9d030a543daf` (accepted)

> Tried inlining the merge application loop inside RegexTokenizer.train in minbpe/regex.py. Also tried the same loop shape in BasicTokenizer.train in minbpe/basic.py, then reverted it. Read minbpe/regex.py lines 1-197, minbpe/base.py lines 1-208, minbpe/basic.py lines 1-80, lineage_bench.py lines 1-107.
> Sandbox evaluation 1 was rejected: train_ir parent 2678982934, candidate 2717114266, ratio 1.0142 vs required 0.9900. encode_ir ratio 1.0012. Sandbox evaluation 3 was accepted: train_ir parent 2678982928, candidate 2588512713, ratio 0.9662. encode_ir ratio 1.0014. Both had identical outputs and guard ok.
> The helper call per chunk in RegexTokenizer.train was the cost worth removing here. The BasicTokenizer.train change did not help enough to keep.
> Next try a narrower pass over RegexTokenizer.train that keeps the lazy-copy behavior for chunks without the selected pair. The submitted candidate has 23 changed lines and is queued for independent replays.
>
> 2026-10-13, episode `cfd755e70325` (accepted)

> Changed minbpe/regex.py in RegexTokenizer.train. First replaced lines 82-85, then lines 82-90, then lines 62-68 and 71-75. The submitted version inlined pair counting for already-filtered chunks and combined chunk encoding with the length filter.
> evaluation 1 was rejected: train_ir parent 2797777683, candidate 2816665451, ratio 1.0068. evaluation 3 was rejected: train_ir parent 2797777689, candidate 2796513514, ratio 0.9995. evaluation 5 was accepted: train_ir parent 2797777689, candidate 2674367201, ratio 0.9559. All had identical outputs.
> The helper-call change did not help. The smaller rewrite did not meet required 0.9900. The last sandbox result kept encode_ir at ratio 1.0002 and moved train_ir to ratio 0.9559.
> Next try is still in RegexTokenizer.train. Check whether more work in the merge loop can be kept local without changing pair insertion order or learned merges. The submitted perf candidate is queued. The verdict comes later from independent replays.
>
> 2026-10-13, episode `5e5c5325aef7` (accepted)

> Changed minbpe/base.py to add a cached nested merge lookup on Tokenizer. Changed BasicTokenizer.encode in minbpe/basic.py. Changed RegexTokenizer.encode_chunk and RegexTokenizer.encode_ordinary in minbpe/regex.py.
> Sandbox evaluation 1 for perf encode_ir returned outcome: accepted. Guard: ok, 3 files, 24 lines. Build: base ok, candidate ok. Equivalence: identical outputs. train_ir parent 2894356096, candidate 2896110474, ratio 1.0006. encode_ir parent 1038805572, candidate 1025301620, ratio 0.9870.
> The repeated encode path was rebuilding the same lookup. The cache is only safe with invalidation when the merges mapping is replaced or changes size. train_ir did not help: ratio 1.0006.
> Next try the independent replay result for the queued encode_ir candidate. Then inspect the train_ir path before adding more cache state.
>
> 2026-10-13, episode `2b6dd2046340` (accepted)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
