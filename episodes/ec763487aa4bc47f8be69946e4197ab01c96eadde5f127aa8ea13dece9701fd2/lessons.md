# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 77 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 16 (12 accepted, 3 no candidate, 1 rejected)
- Best accepted effect: 56.44% (ratio 0.4356), episode `71c36b66692b`
- Accepted effects: 56.44%, 32.45%, 13.41%, 12.94%, 11.01%, 9.45%, 6.64%, 4.53%, 2.62%, 2.57%, 1.71%, 1.24%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.4461 USD (over 16 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 12 (6 accepted, 5 no candidate, 1 rejected)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%, 2.02%, 1.44%, 1.02%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.5166 USD (over 12 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 13 (7 accepted, 4 no candidate, 2 rejected)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 10.64%, 9.58%, 6.55%, 1.72%, 1.15%
- Why candidates failed: stale_conflict (2)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.3377 USD (over 13 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 24 (3 rejected, 17 accepted, 4 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3374 USD (over 24 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 12 (5 accepted, 5 no candidate, 2 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.33%, 1.14%
- Why candidates failed: stale_conflict (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.3485 USD (over 12 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Changed minbpe/regex.py in RegexTokenizer.encode_ordinary. Lines 375-389 were replaced. Lines 396-401 were reduced. The path now keeps a per-tokenizer cache of encoded regex chunks for the ordered str.replace fast path, keyed by current merges.
> Sandbox evaluation perf encode_ir was accepted. Guard ok, 1 files, 19 lines. Equivalence was identical outputs. encode_ir moved from 730896672 to 637844260, ratio 0.8727. train_basic_ir ratio 1.0016. train_regex_ir ratio 1.0018. decode_ir ratio 1.0007. save_load_ir ratio 1.0013.
> The cache helped encode_ir in this sandbox. It did not help the train metrics. The changed code reuses repeated chunks across encode calls and still batches misses with the separator method.
> Next, wait for the queued independent replay. If it disagrees, inspect cache keying by current merges and the miss batching path in RegexTokenizer.encode_ordinary.
>
> 2026-10-13, episode `c876734a6be5` (rejected)

> Tried a save/load file-cache change in minbpe/base.py. Changed Tokenizer.save and Tokenizer.load around lines 302-331, then support code at lines 12-17, 218-221, 267-273, 338-343, and 272-275.
> evaluation 1 said "outcome: rejected (no_improvement: worst ratio 1.0069 vs required 0.9900)". evaluation 3 said "outcome: rejected (no_improvement: worst ratio 0.9963 vs required 0.9900)". evaluation 5 said "outcome: rejected (no_improvement: worst ratio 0.9965 vs required 0.9900)". Build was base ok, candidate ok. Equivalence was identical outputs. Guard was ok.
> I now believe this cache path is not enough for save_load_ir. It preserved behavior, but save_load_ir only reached ratio 0.9963 here, not 0.9900. No candidate was submitted.
> Try fewer text parsing steps in minbpe/base.py load next. Measure save_load_ir first, then watch decode_ir, which reached ratio 1.0060.
>
> 2026-10-13, episode `7181664a3882` (no candidate)

> minbpe/regex.py was changed. The touched functions were RegexTokenizer.train, RegexTokenizer.decode, RegexTokenizer._encode_chunk, RegexTokenizer.encode_ordinary, and RegexTokenizer.encode. Edits hit lines 63-65, 85-92, 108-126, 145-147, and 188-191.
> perf train_regex_ir was rejected every time. evaluation 1 had train_regex_ir ratio 0.9980. evaluation 3 had 0.9981. evaluation 5 had 1.0448. evaluation 7 had 0.9999. The required ratio was 0.9900. Builds passed. Equivalence was identical outputs. Guards were ok. No candidate was submitted.
> I now believe these local changes do not move train_regex_ir enough. Changes outside RegexTokenizer.train did not help the target. The train changes did not help enough.
> Next try should stay inside RegexTokenizer.train. Measure compiled_pattern.findall and get_stats work before changing encode or decode again.
>
> 2026-10-13, episode `192957e32698` (no candidate)

> I changed minbpe/basic.py in get_stats, merge, BasicTokenizer.train, and BasicTokenizer.encode. I also changed minbpe/regex.py in RegexTokenizer.train. I tried counted pair updates, merge-loop changes, and smaller train-loop rewrites.
> Evaluation 1 was rejected: train_basic_ir parent 324048710, candidate 322879261, ratio 0.9964. Evaluation 3 was rejected: ratio 0.9944. Evaluation 5 was rejected: ratio 1.0102. Evaluation 7 was rejected: ratio 0.9947. Equivalence was identical outputs each time. No evaluated change met required 0.9900.
> I believe this path is too small for train_basic_ir. The best measured variant did not clear the guard. Some changes also moved encode_ir and decode_ir the wrong way.
> Next try a different part of BasicTokenizer.train. Look at allocation in repeated ids replacement, or leave basic.py and inspect regex.py split costs. No candidate was submitted.
>
> 2026-10-13, episode `451fcb7031ce` (no candidate)

> I changed minbpe/basic.py. BasicTokenizer.train now updates merge deltas from split parts, not materialized left/right lists and Counter objects. It avoids the tie-candidate set on the common single-top heap case. It reuses the shared byte vocabulary prefix.
> evaluation 1 was rejected: no_improvement, worst ratio 0.9927 vs required 0.9900. evaluation 3 was rejected: no_improvement, worst ratio 0.9917 vs required 0.9900. evaluation 5 was accepted. train_basic_ir: parent 331614471, candidate 328000212, ratio 0.9891. Equivalence had identical outputs. Guard was ok.
> I now believe the useful change is the direct delta accounting in BasicTokenizer.train. The earlier forms moved train_basic_ir but did not help enough. The guard ratios moved up: train_regex_ir 1.0009, encode_ir 1.0043, decode_ir 1.0031, save_load_ir 1.0013.
> Next, wait for independent replays. Then try more BasicTokenizer.train work only if the guard movement stays inside limits. The submitted perf candidate has 64 changed lines and status queued.
>
> 2026-10-13, episode `04e347bfb1c3` (rejected)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
