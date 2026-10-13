# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 23 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 6 (5 accepted, 1 no candidate)
- Best accepted effect: 32.45% (ratio 0.6755), episode `c285e522f788`
- Accepted effects: 32.45%, 13.41%, 4.53%, 2.62%, 1.71%
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.4204 USD (over 6 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 4 (3 accepted, 1 no candidate)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%
- Files changed by accepted candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.2608 USD (over 4 attempts with a recorded cost)

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

- Attempts: 4 (3 accepted, 1 no candidate)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%
- Files changed by accepted candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.3205 USD (over 4 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Tried changes in minbpe/basic.py in BasicTokenizer.train around lines 33-56. Tried count lookup and loop shape changes. Also tried minbpe/regex.py in RegexTokenizer.train around lines 53-60 and RegexTokenizer.encode around lines 222-234.
> evaluation 1: train_basic_ir ratio 1.0094. evaluation 3: train_basic_ir ratio 1.1039. evaluation 5: train_basic_ir ratio 1.0007. evaluation 7: encode_ir ratio 0.9974. All had identical outputs. All were rejected as no_improvement.
> BasicTokenizer.train is touchy here. The tested edits either regressed or moved too little. The RegexTokenizer.encode edit moved encode_ir, but not enough for the required 0.9900.
> Try a different area. Look at get_stats, merge, or the repeated build_merge_map path. Keep equivalence checks tight. Do not keep the BasicTokenizer.train rewrites from this session.
>
> 2026-10-13, episode `ca754602b3d0` (no candidate)

> In minbpe/basic.py I changed BasicTokenizer.train. Lines 12-15 and 36-37 were edited. Adjacent pair counting now uses itertools.pairwise, so ids[1:] is not built for every merge iteration.
> Sandbox evaluation perf train_basic_ir had outcome: accepted. Guard: ok, 1 files, 3 lines. Build: base ok, candidate ok. Equivalence: identical outputs. train_basic_ir was parent 3034462134, candidate 2958400850, ratio 0.9749. train_regex_ir ratio 0.9999. encode_ir ratio 1.0005. decode_ir ratio 0.9990. save_load_ir ratio 1.0000.
> The change is narrow. The measured target moved. The guarded metrics stayed within limit. The same pair order and Counter selection behavior were preserved in the sandbox outputs.
> The perf candidate was submitted on train_basic_ir with 3 changed lines. It was committed and revealed with status queued. Next, read the independent replay result when it arrives.
>
> 2026-10-13, episode `f4377fb56148` (accepted)

> I tried to cut decode_ir. I edited BasicTokenizer.encode in minbpe/basic.py, RegexTokenizer.decode in minbpe/regex.py, and Tokenizer._build_vocab and Tokenizer.load in minbpe/base.py.
> evaluation 1 was rejected: "no_improvement: worst ratio 0.9972 vs required 0.9900". evaluation 3 was rejected: "tests_fail: 1 stable tests fail: tests.test_tokenizer::test_save_load[special_tokens1]". evaluation 5 was rejected: "no_improvement: worst ratio 0.9908 vs required 0.9900". evaluation 7 was rejected: "no_improvement: worst ratio 0.9919 vs required 0.9900".
> I now believe the decode path moved, but not enough. The best measured decode_ir was parent 2247254575, candidate 2226682947, ratio 0.9908. The save/load edits were not clean. save_load_ir reached ratio 1.0188, and one variant failed tests.test_tokenizer::test_save_load[special_tokens1].
> Try next with decode-only changes. Leave Tokenizer.load out until decode_ir passes without save_load_ir movement. No candidate was submitted.
>
> 2026-10-13, episode `5c568f9fc04d` (no candidate)

> I tried BasicTokenizer.decode in minbpe/basic.py. First I added a dense-vocabulary path with negative-id fallback and then backed it down. Then I moved the fallback into the list comprehension, removing the separate min(ids) pass.
> evaluation 1 on decode_ir was rejected: no_improvement: worst ratio 1.0070 vs required 0.9900. equivalence: identical outputs. decode_ir parent 2161121793, candidate 2176193342, ratio 1.0070. evaluation 3 result: outcome: accepted. equivalence: identical outputs. decode_ir parent 2161121799, candidate 2103335642, ratio 0.9733.
> The separate min(ids) pass is the bad part for long positive id lists. Keeping negative, out-of-range, and invalid id fallback inside the lookup path keeps behavior and removes that scan.
> Next, wait for independent replays on the submitted perf candidate. If they disagree, inspect the decode paths found by the "decode\(" search and keep the fallback behavior fixed.
>
> 2026-10-13, episode `87b663865499` (accepted)

> Tried Counter-based pair counting in minbpe/regex.py RegexTokenizer.train. Tried reshaping the merge loop in minbpe/basic.py BasicTokenizer.train. Tried batching vocab-file writes in minbpe/base.py Tokenizer.save. Tried an ASCII fast path in minbpe/base.py render_token.
> Evaluation 1 rejected train_regex_ir: worst ratio 1.3526 vs required 0.9900. Evaluation 3 rejected train_basic_ir: worst ratio 1.1035 vs required 0.9900. Evaluation 5 rejected save_load_ir: worst ratio 1.0072 vs required 0.9900. Evaluation 7 rejected save_load_ir: worst ratio 1.1457 vs required 0.9900. Builds were ok. Equivalence had identical outputs. No candidate was submitted.
> These paths are tight in this shape. Counter use in RegexTokenizer.train added cost. The BasicTokenizer.train loop rewrite added cost. Tokenizer.save batching did not help. render_token ASCII fast path made save_load_ir worse.
> Next try smaller allocation changes. Measure by function. Keep encode_ir and decode_ir guarded.
>
> 2026-10-13, episode `f482022d3fb5` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
