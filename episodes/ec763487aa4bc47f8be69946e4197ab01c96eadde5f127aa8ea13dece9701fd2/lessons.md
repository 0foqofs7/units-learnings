# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 109 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 21 (13 accepted, 6 no candidate, 2 rejected)
- Best accepted effect: 56.44% (ratio 0.4356), episode `71c36b66692b`
- Accepted effects: 56.44%, 32.45%, 13.41%, 12.94%, 11.01%, 9.45%, 6.64%, 4.53%, 2.62%, 2.57%, 1.71%, 1.24%, 1.10%
- Why candidates failed: no_improvement (2)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.4886 USD (over 21 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 15 (6 accepted, 8 no candidate, 1 rejected)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%, 2.02%, 1.44%, 1.02%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.5486 USD (over 15 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 21 (8 accepted, 8 no candidate, 5 rejected)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 13.61%, 10.64%, 9.58%, 6.55%, 1.72%, 1.15%
- Why candidates failed: stale_conflict (2), dependency_failed (1), stale (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.6038 USD (over 21 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 32 (3 rejected, 17 accepted, 12 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.4027 USD (over 32 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 20 (5 accepted, 13 no candidate, 2 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.33%, 1.14%
- Why candidates failed: stale_conflict (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.7464 USD (over 20 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> I read minbpe/basic.py, minbpe/base.py, lineage_bench.py, minbpe/regex.py, and minbpe/__init__.py. I searched "TODO|cache|heap|train_basic". I changed BasicTokenizer.train variants in minbpe/basic.py and one regex.py path.
> evaluation 1 train_basic_ir was parent 268417699, candidate 268957295, ratio 1.0020. Outcome: rejected, no_improvement. evaluation 3 train_basic_ir was parent 268417699, candidate 268514082, ratio 1.0004. Outcome: rejected, no_improvement. Builds passed. Equivalence was identical outputs. Guards were ok.
> These BasicTokenizer.train edits were neutral or slower. The local rewrites did not move train_basic_ir enough. The regex.py edit did not matter for the target.
> Try a different shape in BasicTokenizer.train. Do not keep the tested variants. The session stopped without submitting a change.
>
> 2026-10-14, episode `b842dfad01ea` (no candidate)

> I changed RegexTokenizer.encode for special-token pattern caching. I changed merge and Tokenizer.__init__ in minbpe/base.py for ordered merge cache validation. I changed BasicTokenizer.train for a small count tweak.
> evaluation 1: outcome: rejected, worst ratio 1.0047 vs required 0.9900. evaluation 3: outcome: rejected, worst ratio 1.0039 vs required 0.9900. evaluation 5: outcome: rejected, worst ratio 0.9993 vs required 0.9900. evaluation 7: outcome: rejected, worst ratio 1.2940 vs required 0.9900. Equivalence was identical outputs.
> The encode special-token pattern cache did not help. The ordered merge cache validation did not help. The BasicTokenizer.train count tweak measured only 0.9993 on train_basic_ir and still failed the required 0.9900.
> Try a different path in BasicTokenizer.train or RegexTokenizer.encode_ordinary. Do not keep the cache changes from this session.
>
> 2026-10-13, episode `8f1cb2209d23` (no candidate)

> I tried one save/load cache change in minbpe/base.py. I touched the def save and def load path and the _LOAD_CACHE, _MODEL_BYTES_PREFIX_CACHE, and _VOCAB_BYTES_PREFIX_CACHE area.
> evaluation 1: perf save_load_ir returned outcome: rejected (no_improvement: worst ratio 1.0013 vs required 0.9900). Outputs were identical. save_load_ir was parent 437674408, candidate 438231692, ratio 1.0013. train_basic_ir ratio 1.0025, train_regex_ir ratio 1.0018, encode_ir ratio 1.0003, decode_ir ratio 1.0018.
> I now believe this cache edit did not help. It made the target worse and did not break equivalence.
> Next try a different source of save/load work, not this cache shape. Re-read minbpe/base.py lines 260-380 before editing.
>
> 2026-10-13, episode `7838832bc3fb` (no candidate)

> I worked in minbpe/regex.py. I changed RegexTokenizer.train and the encode path. I also read minbpe/base.py, lineage_bench.py, minbpe/basic.py, and minbpe/__init__.py.
> evaluation 1 was rejected, with train_regex_ir ratio 0.9998. evaluation 3 was rejected, with ratio 1.0003. evaluation 5 was rejected, with ratio 1.0066. Outputs stayed identical. Guards were ok. The best train_regex_ir attempt was only a 0.02% reduction.
> I now believe these local changes in minbpe/regex.py did not move the target enough. Some edits made train_regex_ir worse. encode_ir also moved worse in the measured attempts.
> Try a different shape next. Look past the small line edits in RegexTokenizer.train. Check get_stats and merge use from minbpe/base.py, then measure train_regex_ir again.
>
> 2026-10-13, episode `62b0dcd76f05` (no candidate)

> I tried edits in minbpe/regex.py lines 316-324, 381-386, 406-411, 381-391, 401-411, and 527-529. I tried edits in minbpe/basic.py lines 67-70, 166-168, and 252-256. The session facts do not name functions.
> evaluation 1 decode_ir was rejected: worst ratio 1.0006 vs required 0.9900. decode_ir was parent 532746210, candidate 533066424, ratio 1.0006. evaluation 3 train_basic_ir was rejected: ratio 0.9994. evaluation 5 train_basic_ir was rejected: ratio 0.9998. evaluation 7 encode_ir was rejected: ratio 0.9948. Builds passed. Outputs were identical.
> Decode cache lookup was neutral/slower. The small train and encode edits improved some metrics and did not meet the required 0.9900. No candidate was submitted.
> Next try: avoid the decode cache path. Measure each basic.py train or encode change apart. Stop if the target reads like evaluation 3 or evaluation 5.
>
> 2026-10-13, episode `d586fa494cf6` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
