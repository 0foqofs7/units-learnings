# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 21 published attempts.

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

- Attempts: 2 (2 accepted)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%
- Files changed by accepted candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.3205 USD (over 2 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
