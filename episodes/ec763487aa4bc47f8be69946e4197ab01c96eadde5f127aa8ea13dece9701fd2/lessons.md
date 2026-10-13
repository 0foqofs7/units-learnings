# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 18 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 4 (4 accepted)
- Best accepted effect: 32.45% (ratio 0.6755), episode `c285e522f788`
- Accepted effects: 32.45%, 13.41%, 4.53%, 1.71%
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3876 USD (over 4 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 3 (3 accepted)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%
- Files changed by accepted candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.2592 USD (over 3 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 5 (4 accepted, 1 no candidate)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 9.58%, 6.55%
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3377 USD (over 5 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 4 (2 rejected, 1 accepted, 1 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Why candidates failed: stale (1), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.2301 USD (over 4 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 2 (2 accepted)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%
- Files changed by accepted candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.3205 USD (over 2 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Read minbpe/basic.py, minbpe/base.py, minbpe/regex.py, lineage_bench.py, and searched get_stats|merge. I changed BasicTokenizer.train in minbpe/basic.py. The submitted try used collections.Counter for pair frequency counting and selected the most common pair directly.
> evaluation 1 was rejected: train_basic_ir parent 3652785387, candidate 3965347480, ratio 1.0856. evaluation 3 was rejected: parent 3652785387, candidate 3935466492, ratio 1.0774. evaluation 5 was accepted: parent 3652785393, candidate 3078694288, ratio 0.8428. Equivalence was identical outputs. The submitted perf candidate had 6 changed lines and status queued.
> The extra loop changes did not help. Counter helped when BasicTokenizer.train let it count pairs and choose the most common pair directly. Tie order for observed pairs stayed the same in the sandbox.
> Next try should stay in BasicTokenizer.train. Check whether merge still walks more Python than needed. Wait for independent replays before treating the queued result as final.
>
> 2026-10-13, episode `0bddfdfcf08a` (accepted)

> I read minbpe/regex.py, minbpe/basic.py, minbpe/base.py, minbpe/gpt4.py, and lineage_bench.py. I edited minbpe/regex.py lines 41-47 twice and lines 66-80 once. The final change keeps chunks without the selected pair unchanged, and rebuilds matched chunks from the first replacement point onward.
> evaluation 1 was rejected: no_improvement, worst ratio 1.0002 vs required 0.9900. equivalence: identical outputs. train_regex_ir ratio 1.0002. evaluation 3 was accepted in sandbox. equivalence: identical outputs. train_regex_ir parent 4443227073, candidate 3492259735, ratio 0.7860. train_basic_ir ratio 1.0032. encode_ir ratio 1.0004. decode_ir ratio 0.9998. save_load_ir ratio 1.0001.
> I now believe regex training was paying for allocation and copying on chunks that did not contain the selected pair. The first change did not help. The chunk filter and partial rebuild did.
> Next, wait for the independent replay verdict. If it holds, look at train_basic_ir, which moved to ratio 1.0032.
>
> 2026-10-13, episode `b39c7cc4d86a` (accepted)

> I read minbpe/base.py lines 1-174, minbpe/basic.py lines 1-113, minbpe/regex.py lines 1-206, minbpe/gpt4.py lines 1-131, and lineage_bench.py lines 1-219. I searched "merge\(" and got 4 matching lines. I changed RegexTokenizer.train in minbpe/regex.py, line 13 and lines 45-65.
> perf train_regex_ir returned outcome: accepted. guard: ok, 1 files, 21 lines. build: base ok, candidate ok. equivalence: identical outputs. train_regex_ir parent 5592314206, candidate 4829590786, ratio 0.8636. train_basic_ir ratio 0.9991. encode_ir ratio 1.0003. decode_ir ratio 1.0010. save_load_ir ratio 0.9999. The perf candidate was committed and revealed, status queued.
> Those numbers put the movement in RegexTokenizer.train. The shared merge helper call did not need to sit in that loop. Keeping only chunks that can still contribute pairs kept outputs identical in this sandbox.
> Next try separating minbpe/regex.py line 13 from lines 45-65 and rerun perf train_regex_ir, encode_ir, and decode_ir.
>
> 2026-10-13, episode `365a360ca9ed` (accepted)

> Changed get_stats and merge in minbpe/base.py, BasicTokenizer.encode in minbpe/basic.py, and RegexTokenizer._encode_chunk and RegexTokenizer.encode in minbpe/regex.py. Tried moving the replacement loop's trailing-element check out of encode, avoiding unused dict allocation in build_merge_map, sharing the merge map across special-token encode chunks, and localizing merge_map.get.
> evaluation 1: outcome: rejected (no_improvement: worst ratio 1.0593 vs required 0.9900). evaluation 3: outcome: rejected (no_improvement: worst ratio 0.9998 vs required 0.9900). evaluation 5: outcome: rejected (no_improvement: worst ratio 0.9979 vs required 0.9900). evaluation 7: outcome: rejected (no_improvement: worst ratio 1.0018 vs required 0.9900). All builds were ok. All outputs were identical.
> The encode_ir path did not move enough. Some variants were slower. The safe edits were still noise against the required 0.9900.
> Try a larger encode change next. Look past local variable moves and loop reshaping. No candidate was submitted.
>
> 2026-10-13, episode `92ac3a8f4943` (no candidate)

> I tried minbpe/base.py lines 124-147, then minbpe/base.py lines 124-172, for save_load_ir. I replaced that with minbpe/regex.py lines 89-98 in RegexTokenizer.decode, using the dense vocabulary list lookup first and special-token handling on out-of-range index.
> save_load_ir measured parent 2859177840, candidate 2923901591, ratio 1.0226, outcome: rejected (no_improvement: worst ratio 1.0226 vs required 0.9900). decode_ir measured parent 2605193717, candidate 2265680083, ratio 0.8697, outcome: accepted. Equivalence was identical outputs. Guard was ok, 1 files, 11 lines. Submission ended with no candidate because the pending candidate this change was built on was not accepted.
> I believe RegexTokenizer.decode was carrying an avoidable explicit upper-bound comparison on the valid-token path. The base.py save/load edit did not help.
> Try the same dense lookup shape only where vocab ids are dense. Keep invalid and special-token handling behind the failed list index path.
>
> 2026-10-13, episode `4c423c477fd8` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
