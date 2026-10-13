# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 15 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 4 (4 accepted)
- Best accepted effect: 32.45% (ratio 0.6755), episode `c285e522f788`
- Accepted effects: 32.45%, 13.41%, 4.53%, 1.71%
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3876 USD (over 4 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 1 (1 accepted)
- Best accepted effect: 18.19% (ratio 0.8181), episode `c129bf4b08be`
- Files changed by accepted candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.1959 USD (over 1 attempt with a recorded cost)

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

- Attempts: 1 (1 accepted)
- Best accepted effect: 12.34% (ratio 0.8766), episode `0f2bb1bd86d7`
- Files changed by accepted candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.2921 USD (over 1 attempt with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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

> Changed minbpe/basic.py in BasicTokenizer.decode. Tried a dense vocabulary list fast path for contiguous ids when ids is a list or tuple, with fallback for negative, out-of-range, or non-index ids.
> First decode_ir run was rejected: worst ratio 0.9941 vs required 0.9900. It did not help enough. Later sandbox runs had identical outputs and guard ok. evaluation 3 decode_ir ratio was 0.8692. evaluation 5 ratio was 0.9563. evaluation 7 ratio was 0.9548.
> This code spends decode time in per-token vocabulary access. The dense list path only fits BasicTokenizer.vocab when ids are contiguous and the input ids are list or tuple. The old dictionary path still matters.
> Submitted the 11 changed lines candidate on decode_ir. It was committed and revealed with status waiting. Next try should inspect RegexTokenizer.decode and GPT4Tokenizer.decode for the same dense lookup shape, then measure equivalence and guarded metrics.
>
> 2026-10-13, episode `c1db67aad05a` (accepted)

> Changed minbpe/regex.py in RegexTokenizer.decode. Lines 91-98 were replaced, 8 lines became 12. The decode path uses list lookup guarded by IndexError instead of the explicit upper-bound comparison.
> perf decode_ir was accepted. Guard ok, 1 files, 10 lines. Build base ok, candidate ok. Equivalence identical outputs. decode_ir parent 2686813353, candidate 2343767190, ratio 0.8723. train_basic_ir ratio 1.0003, train_regex_ir ratio 0.9998, encode_ir ratio 1.0007, save_load_ir ratio 0.9998. The perf candidate was committed and revealed, status waiting.
> The bounds check looks like cost on the common valid vocabulary id path in RegexTokenizer.decode. The sandbox did not show broken handling for high special-token ids or invalid ids.
> Next try is to inspect decode paths in minbpe/basic.py and minbpe/base.py for the same pattern after independent replays.
>
> 2026-10-13, episode `22e0b8b3a2d4` (accepted)

> I tried helper changes in minbpe/base.py lines 13-23, 13-30, 25-42, and 25-45. I then changed minbpe/basic.py in BasicTokenizer.train lines 38-41, replacing 4 lines by 16, and inlined the pair replacement step.
> Evaluation 1 was rejected: no_improvement, worst ratio 1.0055 vs required 0.9900. Evaluation 3 cut train_basic_ir to ratio 0.8749, but was rejected because train_regex_ir worsened +2.86% with a 1% limit. Evaluation 5 was accepted in sandbox: train_basic_ir ratio 0.8768, train_regex_ir ratio 1.0001, encode_ir ratio 0.9993, decode_ir ratio 0.9998, save_load_ir ratio 1.0001. Outputs were identical. The candidate was submitted and is queued.
> The base.py helper changes did not help. The useful change was inside BasicTokenizer.train. The shared merge helper cost shows up in the basic training loop, while regex training needs to stay on the old path.
> Next try a smaller BasicTokenizer.train specialization, if replay keeps the same shape. Measure train_regex_ir before submit.
>
> 2026-10-13, episode `0f2bb1bd86d7` (accepted)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
