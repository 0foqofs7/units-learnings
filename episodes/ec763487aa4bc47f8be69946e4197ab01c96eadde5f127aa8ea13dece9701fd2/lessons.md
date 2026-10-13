# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 91 published attempts.

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

- Attempts: 28 (3 rejected, 17 accepted, 8 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3786 USD (over 28 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 16 (5 accepted, 9 no candidate, 2 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.33%, 1.14%
- Why candidates failed: stale_conflict (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.5783 USD (over 16 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> I read minbpe/basic.py, minbpe/base.py, minbpe/regex.py, and lineage_bench.py. I searched "where\[p\]\.append". I tried edits in minbpe/basic.py lines 158-167, 66-70, and 86-89, then reverted them. I tried edits in minbpe/regex.py lines 12-16, 101-111, 14-23, 83-87, 99-106, and 114-116.
> evaluation 1 was rejected: train_basic_ir ratio 1.0038. evaluation 3 was rejected: train_basic_ir ratio 1.0003. evaluation 5 was rejected: train_basic_ir ratio 1.0005. evaluation 7 was rejected: train_regex_ir ratio 0.9959 vs required 0.9900. Builds were ok. Equivalence was identical outputs.
> The train_basic changes did not help. They were neutral or worse. The train_regex heap import change moved train_regex_ir to 0.9959, but that was not enough. The guards stayed within limits.
> Try a different part of the train loop. Do not spend more time on these basic.py line edits. Look again at the regex training update path around "where\[p\]\.append".
>
> 2026-10-13, episode `4198c98902b6` (no candidate)

> I tried save/load path edits in minbpe/base.py, changing Tokenizer.decode, Tokenizer._build_vocab, Tokenizer.save, and Tokenizer.load. I also touched BasicTokenizer.encode in minbpe/basic.py and RegexTokenizer.encode_ordinary and RegexTokenizer.encode in minbpe/regex.py.
> evaluation 1 was rejected: save_load_ir parent 437423510, candidate 436775009, ratio 0.9985. evaluation 3 was rejected: ratio 0.9984. evaluation 5 was rejected: ratio 0.9956. evaluation 7 was rejected: ratio 0.9956. The required ratio was 0.9900. Builds passed. Outputs were identical. Guards were ok.
> I now believe this code path is not moved enough by small formatting and loop changes in base.py. The best sandbox save_load_ir ratio shown here was 0.9956, and encode_ir moved the wrong way in every run.
> Try a narrower change inside Tokenizer.load and Tokenizer.save only. Leave encode paths alone unless save_load_ir first clears the required ratio.
>
> 2026-10-13, episode `e544e91f7ede` (no candidate)

> I tried a save/load cache change in minbpe/base.py. The edits touched lines 12-17, 220-222, 224-241, 259-272, 269-279, 312-331, 312-344, 340-343, 390-393, and 144-146.
> evaluation 1 was rejected: save_load_ir parent 442229920, candidate 439587215, ratio 0.9940, required 0.9900. evaluation 3 was rejected: save_load_ir ratio 1.0131. evaluation 5 was rejected: save_load_ir parent 442229916, candidate 439554118, ratio 0.9939. Builds were base ok, candidate ok. Equivalence was identical outputs. Guard was ok.
> The cache change preserved behavior but did not help enough. The vocab follow-up regressed. No candidate was submitted.
> Try smaller changes around save/load serialization in minbpe/base.py. Measure save_load_ir first. Then check train_basic_ir, train_regex_ir, encode_ir, and decode_ir.
>
> 2026-10-13, episode `f7f06eb93570` (no candidate)

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

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
