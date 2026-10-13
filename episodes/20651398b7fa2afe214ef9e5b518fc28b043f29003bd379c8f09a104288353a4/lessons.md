# Lessons: minbpe

Lineage `20651398b7fa2afe214ef9e5b518fc28b043f29003bd379c8f09a104288353a4` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 12 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:encode_ir

- Attempts: 5 (3 accepted, 1 rejected, 1 no candidate)
- Best accepted effect: 34.31% (ratio 0.6569), episode `4297b89143e7`
- Accepted effects: 34.31%, 12.19%, 3.74%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.2727 USD (over 5 attempts with a recorded cost)

## Target perf:train_ir

- Attempts: 7 (5 accepted, 1 rejected, 1 no candidate)
- Best accepted effect: 15.17% (ratio 0.8483), episode `0bada18988c2`
- Accepted effects: 15.17%, 14.04%, 6.05%, 1.94%, 1.38%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.1917 USD (over 7 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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

> I tried pair selection via stats.items() in BasicTokenizer.train and RegexTokenizer.train. I tried a short-sequence fast path and local get_stats binding. I tried an index-loop get_stats variant in minbpe/base.py.
> evaluation 1 was "outcome: rejected (no_improvement: worst ratio 0.9953 vs required 0.9900)". train_ir was parent 2952450113, candidate 2938429255, ratio 0.9953. evaluation 3 was rejected at ratio 0.9969. evaluation 5 was rejected at ratio 1.0416. evaluation 7 was rejected at ratio 1.0743. equivalence was identical outputs. build was base ok, candidate ok. Repository was restored to the starting state. No candidate was submitted.
> I now believe the train_ir cost here is not in the small Python reshapes I tried. The short-sequence fast path did not help. The index-loop get_stats variant was slower.
> Next, read the merge loop again before editing. Look for less repeated work around pair counts, not just cleaner iteration. Measure train_ir and encode_ir after each change.
>
> 2026-10-13, episode `def7ec96ffd9` (no candidate)

> I changed minbpe/base.py merge and minbpe/regex.py merge. I moved unmatched trailing-token handling out of the scan loop. I read minbpe/base.py, minbpe/basic.py, minbpe/regex.py, minbpe/gpt4.py, and lineage_bench.py.
> Evaluation 1 was rejected. train_ir was parent 2859591337, candidate 2857360562, ratio 0.9992. encode_ir was parent 1124152912, candidate 1116894643, ratio 0.9935. Evaluation 3 was accepted in sandbox. train_ir was parent 2859591337, candidate 2819054839, ratio 0.9858. encode_ir was parent 1124152906, candidate 1116648972, ratio 0.9933. Outputs were identical.
> The first change did not help enough. The second change says the end-of-list check in merge mattered inside the hot loop. The code is still doing the same merge work.
> Submitted the train_ir candidate with 16 changed lines. It was committed and revealed with status waiting. Next, check independent replay, then look for the next branch or bounds check inside training.
>
> 2026-10-13, episode `f83af6d14c19` (accepted)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
