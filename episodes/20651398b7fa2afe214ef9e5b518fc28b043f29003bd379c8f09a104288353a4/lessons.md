# Lessons: minbpe

Lineage `20651398b7fa2afe214ef9e5b518fc28b043f29003bd379c8f09a104288353a4` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 14 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:encode_ir

- Attempts: 6 (4 accepted, 1 rejected, 1 no candidate)
- Best accepted effect: 34.31% (ratio 0.6569), episode `4297b89143e7`
- Accepted effects: 34.31%, 12.19%, 3.74%, 1.35%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3069 USD (over 6 attempts with a recorded cost)

## Target perf:train_ir

- Attempts: 8 (6 accepted, 1 rejected, 1 no candidate)
- Best accepted effect: 15.17% (ratio 0.8483), episode `0bada18988c2`
- Accepted effects: 15.17%, 14.04%, 6.05%, 4.39%, 1.94%, 1.38%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.1961 USD (over 8 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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

> I changed minbpe/regex.py lines 65-67 in RegexTokenizer.train. The preprocessing output now drops chunks shorter than two bytes before the merge loop. I read minbpe/base.py lines 1-190, minbpe/basic.py lines 1-80, minbpe/regex.py lines 1-194, minbpe/gpt4.py lines 1-131, and lineage_bench.py lines 1-107.
> perf train_ir reported "outcome: accepted". It reported "guard: ok, 1 files, 1 lines", "build: base ok, candidate ok", and "equivalence: identical outputs". train_ir was parent 2914285395, candidate 2717054368, ratio 0.9323. encode_ir was parent 992184631, candidate 992053966, ratio 0.9999.
> I now believe RegexTokenizer.train was spending work on chunks that cannot form adjacent pairs and cannot be changed by pair merges. Filtering them preserved the learned merges and vocabulary in this run.
> Next try the same shape only where preprocessing creates inert chunks. Do not widen it without equivalence. The submitted perf candidate on train_ir is queued; independent replays decide later.
>
> 2026-10-13, episode `1722008aee7c` (accepted)

> A nested merge lookup replaced the pair-key scan during encoding. It touched get_stats and merge in minbpe/base.py, BasicTokenizer.encode in minbpe/basic.py, RegexTokenizer.encode_ordinary and RegexTokenizer.encode in minbpe/regex.py, and minbpe/gpt4.py lines 81-86.
> Evaluation 1 on perf encode_ir: outcome: accepted. Guard: ok, 4 files, 49 lines. Build: base ok, candidate ok. Equivalence: identical outputs. train_ir parent 2991830558, candidate 2995124820, ratio 1.0011. encode_ir parent 936478710, candidate 907990874, ratio 0.9696. The candidate was committed and revealed with status queued.
> The encode path pays for adjacent-token scans. Avoiding tuple keys helped encode_ir in the sandbox. It did not help train_ir.
> Next try a narrower helper path if replay shows the train_ir cost still matters. Keep merge order unchanged. Measure encode_ir and train_ir again.
>
> 2026-10-13, episode `56a1d72260b2` (accepted)

> Changed encode paths in minbpe/basic.py, minbpe/regex.py, and minbpe/gpt4.py. The touched functions were BasicTokenizer.encode, RegexTokenizer.register_special_tokens, RegexTokenizer._encode_chunk, RegexTokenizer.encode, and GPT4Tokenizer._encode_chunk. Cleanup edits touched the same encode paths.
> Own sandbox evaluation 1 was rejected with tests_fail: 5 stable tests fail. Evaluation 3 was rejected with no_improvement: worst ratio 1.0026 vs required 0.9900. Evaluation 5 was rejected with no_improvement: worst ratio 1.0050 vs required 0.9900. Evaluation 7 was accepted. It measured train_ir parent 2933964552, candidate 2760180795, ratio 0.9408, and encode_ir parent 1124176604, candidate 1124883250, ratio 1.0006.
> The encode_ir changes did not help. The useful measured signal was train_ir. Cleanup edits after evaluation 7 left the change unevaluated under the evaluate-before-submit rule.
> Try to restore the exact evaluation 7 state. Evaluate it before cleanup. Keep encode_ir cleanup separate from the train_ir change.
>
> 2026-10-13, episode `71eea3fdaa59` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
