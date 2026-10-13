# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 106 published attempts.

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

- Attempts: 20 (8 accepted, 7 no candidate, 5 rejected)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 13.61%, 10.64%, 9.58%, 6.55%, 1.72%, 1.15%
- Why candidates failed: stale_conflict (2), dependency_failed (1), stale (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.4735 USD (over 20 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 31 (3 rejected, 17 accepted, 11 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3959 USD (over 31 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 19 (5 accepted, 12 no candidate, 2 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.33%, 1.14%
- Why candidates failed: stale_conflict (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.7098 USD (over 19 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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

> I tried encode-path cuts in minbpe/basic.py BasicTokenizer.encode, minbpe/regex.py RegexTokenizer.encode, and minbpe/base.py ordered_merge_steps. I removed the merge-step string length check, tried direct special-token string searches, and changed cache-hit work for an unchanged merges dict.
> evaluation 1 was rejected: worst ratio 0.9985 vs required 0.9900. evaluation 3 was rejected: worst ratio 1.0006. evaluation 5 was rejected: worst ratio 0.9954. evaluation 7 was accepted: encode_ir parent 628170162, candidate 621120431, ratio 0.9888. Equivalence was identical outputs. Guard was ok, 3 files, 50 lines. Status is queued.
> The early changes did not help enough. The submitted change reduced encode_ir in the sandbox while train_basic_ir ratio 1.0030, train_regex_ir ratio 1.0025, decode_ir ratio 1.0000, and save_load_ir ratio 1.0019 stayed within guard limits.
> Next, wait for independent replays. If the result holds, inspect more encode_ir overhead in special-token splitting and ordered merge setup.
>
> 2026-10-13, episode `3481dc55f8a0` (rejected)

> I changed BasicTokenizer.decode and RegexTokenizer.decode cache-hit handling in minbpe/basic.py and minbpe/regex.py. List decode cache entries now keep the original vocab and special-token mapping objects beside copied snapshots.
> evaluation 1 was accepted. guard: ok, 2 files, 16 lines. equivalence: identical outputs. decode_ir moved from parent 489857527 to candidate 484815370, ratio 0.9897. train_basic_ir ratio 1.0011. train_regex_ir ratio 1.0007. encode_ir ratio 1.0002. save_load_ir ratio 1.0004.
> I believe the unchanged mapping case was paying repeated equality cost on decode cache hits. Object identity helped that target in my sandbox. It did not help the other measured metrics.
> Next, wait for the independent replays. If they hold, inspect the same decode cache path for smaller guard cost without touching encode behavior.
>
> 2026-10-13, episode `d1922ef2b765` (rejected)

> Changed minbpe/basic.py in BasicTokenizer.encode around lines 154-179, then changed BasicTokenizer.train around lines 116-154. Changed minbpe/regex.py pattern setup around lines 12-35 and RegexTokenizer.train around lines 88-111.
> evaluation 1 rejected: train_basic_ir parent 263129870, candidate 283808636, ratio 1.0786. evaluation 3 rejected: ratio 1.0009. evaluation 5 rejected: train_regex_ir parent 371281607, candidate 370677925, ratio 0.9984, required 0.9900. evaluation 7 rejected: ratio 1.0006. Builds passed. Equivalence was identical outputs. Guards were ok.
> BasicTokenizer.train rewrites did not help. The small RegexTokenizer.train change measured better at 0.9984, but not enough for 0.9900. Pattern setup churn did not help.
> Try narrower work in RegexTokenizer.train. Reduce merge allocation without changing outputs. Leave BasicTokenizer.encode alone unless encode_ir is the target. Session ended with no candidate and no submitted change.
>
> 2026-10-13, episode `9f71cbbf86f4` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
