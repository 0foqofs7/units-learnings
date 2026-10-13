# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 88 published attempts.

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

- Attempts: 17 (8 accepted, 6 no candidate, 3 rejected)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 13.61%, 10.64%, 9.58%, 6.55%, 1.72%, 1.15%
- Why candidates failed: stale_conflict (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.3377 USD (over 17 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 26 (3 rejected, 17 accepted, 6 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3561 USD (over 26 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 15 (5 accepted, 8 no candidate, 2 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.33%, 1.14%
- Why candidates failed: stale_conflict (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.5765 USD (over 15 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Tried encode-path changes in BasicTokenizer.encode and RegexTokenizer.encode, then reverted them. Changed get_stats and merge in minbpe/base.py. Changed RegexTokenizer.encode again in minbpe/regex.py.
> Evaluation 1 was rejected: encode_ir ratio 1.0143 vs required 0.9900. Evaluation 3 was rejected: 0.9973. Evaluation 5 was rejected: 0.9931. Evaluation 7 was rejected: 0.9922. All had build base ok, candidate ok, and identical outputs.
> The encode path stayed equivalent. The changes did not help enough. The lowest measured encode_ir ratio was 0.9922, short of 0.9900. No candidate was submitted.
> Try a smaller change inside the hot encode loop. Leave get_stats and merge alone unless the target is not encode_ir.
>
> 2026-10-13, episode `740171ff8049` (no candidate)

> Read minbpe/basic.py, minbpe/base.py, minbpe/regex.py, and lineage_bench.py. Searched ord(c), heap push/pop/replace, BYTE_VOCAB, and stats.update. Changed BasicTokenizer.train in minbpe/basic.py. Tried a vocabulary/heap-binding variant, then tie and neighbour update variants.
> evaluation 1 was rejected: train_basic_ir parent 279100809, candidate 359851447, ratio 1.2893. evaluation 3 was rejected: ratio 0.9958. evaluation 5 was rejected: ratio 0.9960. evaluation 7 was rejected: ratio 1.0676. Builds were ok. Outputs were identical. No measured change reached the 1% threshold.
> The viable BasicTokenizer.train vocabulary/heap-binding variant only improved train_basic_ir by about 0.4%. Other attempted train tie/neighbour updates regressed. The local edits in minbpe/basic.py did not clear the required bar.
> Next, try a different BasicTokenizer.train path around pair-stat updates. Keep the equivalence checks. Do not submit the current patch.
>
> 2026-10-13, episode `40078a4082b1` (no candidate)

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

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
