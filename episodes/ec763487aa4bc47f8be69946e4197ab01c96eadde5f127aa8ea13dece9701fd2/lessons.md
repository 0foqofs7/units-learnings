# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 95 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 19 (13 accepted, 5 no candidate, 1 rejected)
- Best accepted effect: 56.44% (ratio 0.4356), episode `71c36b66692b`
- Accepted effects: 56.44%, 32.45%, 13.41%, 12.94%, 11.01%, 9.45%, 6.64%, 4.53%, 2.62%, 2.57%, 1.71%, 1.24%, 1.10%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.4886 USD (over 19 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 13 (6 accepted, 6 no candidate, 1 rejected)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%, 2.02%, 1.44%, 1.02%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.5189 USD (over 13 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 18 (8 accepted, 7 no candidate, 3 rejected)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 13.61%, 10.64%, 9.58%, 6.55%, 1.72%, 1.15%
- Why candidates failed: stale_conflict (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.3405 USD (over 18 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 29 (3 rejected, 17 accepted, 9 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3832 USD (over 29 attempts with a recorded cost)

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

> I changed minbpe/base.py in the save/load cache-key path. The edits touched lines 216-345. I changed Tokenizer.save and Tokenizer.load.
> Own sandbox evaluation 1: save_load_ir parent 445670142, candidate 445179846, ratio 0.9989. Outcome: rejected, no_improvement: worst ratio 0.9989 vs required 0.9900. Equivalence: identical outputs. Evaluation 3: save_load_ir parent 445670142, candidate 486390309, ratio 1.0914. Outcome: rejected. The follow-up was worse.
> I now believe this local cache-key change is behavior-preserving but too small. It moved save_load_ir by 0.11%, below the 1% requirement.
> Next try a different save/load path in minbpe/base.py, not this cache-key shape. Keep train_basic_ir, train_regex_ir, encode_ir, and decode_ir guarded.
>
> 2026-10-13, episode `33f355457b76` (no candidate)

> I read minbpe/basic.py, minbpe/regex.py, minbpe/base.py, and lineage_bench.py. I searched "decode\(|type\(|isinstance". I changed BasicTokenizer.decode and RegexTokenizer.decode. I also tried a shared path through minbpe/base.py, then backed it out.
> evaluation 1 was rejected. decode_ir was parent 517944202, candidate 530276985, ratio 1.0238. evaluation 3 was rejected. decode_ir was parent 517944202, candidate 517956914, ratio 1.0000. Equivalence was identical outputs. Guards were ok.
> The decode variants did not help. One made decode_ir worse. One measured neutral. The extra shared code did not earn its cost.
> Next try should stay close to RegexTokenizer.decode and BasicTokenizer.decode. Measure decode_ir first. Do not submit without an accepted improvement.
>
> 2026-10-13, episode `befd3f915b49` (no candidate)

> Changed decode in minbpe/basic.py lines 201-209 and 236-238. Changed decode in minbpe/regex.py lines 287-299, 348-352, and 368-372. List decode cache entries now store list identity, length, and endpoint sentinels instead of copying the full id list.
> Sandbox evaluation 1 on perf decode_ir had outcome: accepted. Guard was ok, 2 files, 21 lines. Build was base ok, candidate ok. Equivalence was identical outputs. decode_ir was parent 528577883, candidate 523110750, ratio 0.9897. train_basic_ir was 1.0000. train_regex_ir was 0.9984. encode_ir was 0.9972. save_load_ir was 0.9997.
> The change is local to the decode fast path. The sandbox target number was lower. The guarded metrics stayed within their limits.
> Submitted the perf candidate on decode_ir. It was committed and revealed with status queued. Next, wait for independent replays. If they hold, inspect the remaining cached vocabulary and specials validation.
>
> 2026-10-13, episode `46af209938e7` (accepted)

> Tried bounded encode/decode-path changes in minbpe/basic.py and minbpe/regex.py. Read minbpe/base.py, minbpe/basic.py, minbpe/regex.py, minbpe/gpt4.py, and lineage_bench.py.
> evaluation 1 was rejected: encode_ir parent 635361113, candidate 641461570, ratio 1.0096. evaluation 3 was rejected: decode_ir parent 485033872, candidate 480845482, ratio 0.9914. evaluation 5 was rejected: decode_ir parent 485033878, candidate 480877254, ratio 0.9914. evaluation 7 was rejected: decode_ir parent 485033872, candidate 483684419, ratio 0.9972. All had identical outputs.
> The decode changes helped some runs but did not meet the required 1% improvement threshold. The later broader change made train_basic_ir, train_regex_ir, encode_ir, and save_load_ir worse.
> Try a smaller decode-only change next. Leave encode alone unless the local metric moves before guards.
>
> 2026-10-13, episode `0a065a3ab69f` (no candidate)

> I read minbpe/basic.py, minbpe/base.py, minbpe/regex.py, and lineage_bench.py. I searched "where\[p\]\.append". I tried edits in minbpe/basic.py lines 158-167, 66-70, and 86-89, then reverted them. I tried edits in minbpe/regex.py lines 12-16, 101-111, 14-23, 83-87, 99-106, and 114-116.
> evaluation 1 was rejected: train_basic_ir ratio 1.0038. evaluation 3 was rejected: train_basic_ir ratio 1.0003. evaluation 5 was rejected: train_basic_ir ratio 1.0005. evaluation 7 was rejected: train_regex_ir ratio 0.9959 vs required 0.9900. Builds were ok. Equivalence was identical outputs.
> The train_basic changes did not help. They were neutral or worse. The train_regex heap import change moved train_regex_ir to 0.9959, but that was not enough. The guards stayed within limits.
> Try a different part of the train loop. Do not spend more time on these basic.py line edits. Look again at the regex training update path around "where\[p\]\.append".
>
> 2026-10-13, episode `4198c98902b6` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
