# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 86 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 17 (12 accepted, 4 no candidate, 1 rejected)
- Best accepted effect: 56.44% (ratio 0.4356), episode `71c36b66692b`
- Accepted effects: 56.44%, 32.45%, 13.41%, 12.94%, 11.01%, 9.45%, 6.64%, 4.53%, 2.62%, 2.57%, 1.71%, 1.24%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.4511 USD (over 17 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 13 (6 accepted, 6 no candidate, 1 rejected)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%, 2.02%, 1.44%, 1.02%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.5189 USD (over 13 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 16 (8 accepted, 5 no candidate, 3 rejected)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 13.61%, 10.64%, 9.58%, 6.55%, 1.72%, 1.15%
- Why candidates failed: stale_conflict (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.3297 USD (over 16 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 26 (3 rejected, 17 accepted, 6 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3561 USD (over 26 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 14 (5 accepted, 7 no candidate, 2 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.33%, 1.14%
- Why candidates failed: stale_conflict (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.4627 USD (over 14 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> I tried a save/load cache change in minbpe/base.py. The changed code was in Tokenizer.save and Tokenizer.load, with nearby vocab handling. I read base.py, basic.py, regex.py, gpt4.py, __init__.py, lineage_bench.py, and lineage_equiv.py.
> evaluation 1 was rejected: save_load_ir ratio 1.0006. evaluation 3 was rejected: save_load_ir ratio 0.9961. evaluation 5 was rejected: save_load_ir ratio 0.9957. Builds passed. Equivalence was identical outputs. Guards were ok.
> The cache change did not help enough. It preserved behavior. It moved save_load_ir by about 0.4%, below the required 1% threshold. It also added small cost to train_basic_ir, train_regex_ir, and decode_ir.
> Next try a smaller change in Tokenizer.load only. Do not keep extra cache state unless the load path pays for it. Measure save_load_ir first, then check encode_ir and decode_ir.
>
> 2026-10-13, episode `3cf8d4f87ca6` (no candidate)

> I tried import and counting changes in minbpe/regex.py, including RegexTokenizer.train. I replaced edits across lines 12-15, 68-70, 84-103, 113-131, and 262-271. I also read minbpe/base.py, minbpe/basic.py, lineage_bench.py, and minbpe/__init__.py.
> Evaluation 1 was rejected: train_regex_ir ratio 1.0119. Evaluation 3 was rejected: decode_ir ratio 1.0002. Evaluation 5 was rejected: train_regex_ir ratio 0.9979. Evaluation 7 was rejected: train_regex_ir ratio 0.9971. Outputs were identical. Builds were ok. Guards were ok. The best train_regex_ir result was 0.9971, below required 0.9900.
> I now believe these regex.py import and counting edits are too small for this benchmark. Some changes regressed. The useful movement was only about 0.29% on train_regex_ir.
> Next try a different path in RegexTokenizer.train or leave regex.py alone and inspect base.py/basic.py hot loops against lineage_bench.py.
>
> 2026-10-13, episode `cf1d166dbdd0` (no candidate)

> I tried two paths. First I changed minbpe/base.py around Tokenizer.save and Tokenizer.load to cache byte encodings on load. Then I changed minbpe/base.py, minbpe/basic.py and minbpe/regex.py around BasicTokenizer.decode and RegexTokenizer.decode to use decoded string subclass caching.
> evaluation 1 on save_load_ir was rejected: no_improvement: worst ratio 1.0547 vs required 0.9900. Outputs were identical. save_load_ir was parent 467772212, candidate 493371045, ratio 1.0547. evaluations 3 and 5 on decode_ir were rejected for equivalence_changed. Both said OUTPUTS DIFFER from parent.
> I now believe the byte-cache load change is the wrong direction for this code. The decoded string subclass path is not equivalent to the parent on seeded inputs.
> Next try decode work that returns the same output shape as the parent. Measure decode_ir first. Then check save_load_ir.
>
> 2026-10-13, episode `6012e6d51d5c` (no candidate)

> I changed minbpe/regex.py. I tried regex train counting and import changes. I changed the code around encode_ordinary and encode. I also tried caching the special-token split pattern.
> evaluation 1 on train_regex_ir was outcome: rejected, worst ratio 1.0126 vs required 0.9900. evaluation 3 on train_regex_ir was outcome: rejected, worst ratio 1.0009 vs required 0.9900. evaluation 5 on encode_ir was outcome: rejected, worst ratio 1.0000 vs required 0.9900. Outputs stayed identical.
> I believe this path is dry. The counting/import changes regressed or sat still. The special-token split cache did not move encode_ir enough. No candidate was submitted.
> Next try should leave regex.py micro-caches alone and look at the hot merge path shared with base.py and basic.py.
>
> 2026-10-13, episode `ae34d2650306` (no candidate)

> Tried save_load_ir on minbpe-heavy. Read minbpe/base.py, lineage_bench.py, minbpe/basic.py, minbpe/regex.py, and lineage_equiv.py. Edited minbpe/base.py around save and load, lines 220-265, and later lines 327-338.
> Evaluation 1 was rejected: no_improvement, worst ratio 0.9973 vs required 0.9900. save_load_ir was parent 463446721, candidate 462183770, ratio 0.9973. Equivalence was identical outputs. Evaluation 3 was rejected: tests_fail, tests.test_tokenizer::test_save_load[special_tokens1]. Equivalence was OUTPUTS DIFFER.
> The first valid edit moved save_load_ir by 0.27%. That did not help enough. The follow-up broke save/load behavior around special tokens.
> Next try should stay inside minbpe/base.py save and load, but keep tests.test_tokenizer::test_save_load[special_tokens1] fixed first. Then measure save_load_ir again.
>
> 2026-10-13, episode `dd06e691fe71` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
