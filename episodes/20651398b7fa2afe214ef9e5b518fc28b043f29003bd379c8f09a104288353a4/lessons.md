# Lessons: minbpe

Lineage `20651398b7fa2afe214ef9e5b518fc28b043f29003bd379c8f09a104288353a4` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 10 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:encode_ir

- Attempts: 4 (2 accepted, 1 rejected, 1 no candidate)
- Best accepted effect: 34.31% (ratio 0.6569), episode `4297b89143e7`
- Accepted effects: 34.31%, 12.19%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.2375 USD (over 4 attempts with a recorded cost)

## Target perf:train_ir

- Attempts: 6 (4 accepted, 1 rejected, 1 no candidate)
- Best accepted effect: 15.17% (ratio 0.8483), episode `0bada18988c2`
- Accepted effects: 15.17%, 14.04%, 1.94%, 1.38%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.1929 USD (over 6 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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

> Changed minbpe/regex.py RegexTokenizer.train at lines 70-80. It skips regex-training chunks shorter than two tokens when collecting pair counts and when applying a merge.
> Own sandbox evaluation perf train_ir had outcome: accepted. guard: ok, 1 files, 5 lines. build: base ok, candidate ok. equivalence: identical outputs. train_ir: parent 3093707638, candidate 3001903254, ratio 0.9703. encode_ir: parent 1122634032, candidate 1122743414, ratio 1.0001.
> Chunks shorter than two tokens cannot contribute adjacent pairs and cannot be changed by a pair merge. The old path spent helper calls and scans there.
> Submitted the perf candidate on train_ir. Status is waiting. Next, let independent replays decide it, then measure the same kind of skipped work before touching more code.
>
> 2026-10-13, episode `eb1be42ef413` (rejected)

> I read minbpe/base.py, minbpe/basic.py, minbpe/regex.py, minbpe/gpt4.py, and lineage_bench.py. I changed minbpe.regex.RegexTokenizer.train and the merge helper path in minbpe/regex.py.
> evaluation 1 was perf train_ir. outcome: accepted. equivalence: identical outputs. train_ir was parent 3518650345, candidate 2973852171, ratio 0.8452. encode_ir was parent 964998222, candidate 965493059, ratio 1.0005.
> I believe the win is from not allocating replacement lists for chunks that do not contain the selected pair. The encode path did not improve.
> Next try the same rule in the other training paths only if they copy unchanged chunks during merges. The candidate was submitted on train_ir and is queued. The verdict comes later from independent replays.
>
> 2026-10-13, episode `0bada18988c2` (accepted)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
