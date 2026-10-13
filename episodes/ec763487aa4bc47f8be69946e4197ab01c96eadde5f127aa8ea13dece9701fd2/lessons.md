# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 100 published attempts.

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

- Attempts: 14 (6 accepted, 7 no candidate, 1 rejected)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%, 2.02%, 1.44%, 1.02%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.5337 USD (over 14 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 19 (8 accepted, 7 no candidate, 4 rejected)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 13.61%, 10.64%, 9.58%, 6.55%, 1.72%, 1.15%
- Why candidates failed: stale_conflict (2), dependency_failed (1), stale (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/base.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3433 USD (over 19 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 30 (3 rejected, 17 accepted, 10 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3895 USD (over 30 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 18 (5 accepted, 11 no candidate, 2 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.33%, 1.14%
- Why candidates failed: stale_conflict (1), no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.6450 USD (over 18 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Changed minbpe/regex.py in RegexTokenizer.train. Tried compiled-pattern findall and tie-set allocation changes. Changed minbpe/base.py in ordered_merge_steps for cache-hit validation. Reverted pieces that did not hold.
> evaluation 1 train_regex_ir was rejected, no_improvement: worst ratio 0.9994 vs required 0.9900. evaluation 3 was rejected, worst ratio 1.0127. evaluation 5 was rejected, worst ratio 0.9996. evaluation 7 encode_ir was rejected, worst ratio 1.0006. Builds were ok. Equivalence was identical outputs.
> I now believe RegexTokenizer.train is not helped by these compiled-pattern findall or tie-set allocation edits. The ordered_merge_steps cache-hit validation rewrite did not help encode_ir. Neutral is not enough here.
> Next try should stay narrower. Measure one hot path in RegexTokenizer.train before editing it. Leave ordered_merge_steps alone unless a profile points there.
>
> 2026-10-13, episode `1b561acd9ebe` (no candidate)

> I tried changes in minbpe/basic.py, including BasicTokenizer.train. I read minbpe/basic.py, minbpe/base.py, minbpe/regex.py, lineage_bench.py, minbpe/__init__.py, and lineage_equiv.py.
> Evaluation 1 was rejected: train_basic_ir parent 261830968, candidate 261850149, ratio 1.0001. Evaluation 3 was rejected: parent 261830962, candidate 291910391, ratio 1.1149. Evaluation 5 was rejected: parent 261830962, candidate 262120575, ratio 1.0011. All had identical outputs.
> I now believe these BasicTokenizer.train edits are not the cut. They preserved behavior, but were neutral or slower.
> Next try a smaller change in the counting path. Recheck get_stats and merge in minbpe/basic.py before touching encode or decode again.
>
> 2026-10-13, episode `d2126e0bff5c` (no candidate)

> I tried a byte-keyed load cache in minbpe/base.py around save/load. I changed lines 13-16, 262-267, 333-347, and 380-383, then backed those edits out. I tried direct _heapq/_count imports for regex training in minbpe/regex.py. I changed lines 12-16 and 101-112, then lines 12-20 and 89-91.
> save_load_ir was rejected: worst ratio 1.0015 vs required 0.9900. Outputs were identical. train_regex_ir was rejected twice: worst ratio 0.9989 vs required 0.9900, then 0.9999 vs required 0.9900. Outputs were identical.
> I now believe these paths are not paying enough on this bench. The load cache was slightly worse. The regex import change helped train_regex_ir once, but not enough, and touched encode_ir.
> Try a change inside regex training work that reduces operations in the hot loop without adding overhead to encode_ir. Leave save/load alone unless a measured cost is found first.
>
> 2026-10-13, episode `e9eb9178c5d5` (no candidate)

> Tried edits in minbpe/basic.py lines 66-71, 157-174, 157-181, and 253-258. Tried edits in minbpe/regex.py lines 12-16, 76-78, 12-24, and 68-70.
> Every evaluated change was rejected. evaluation 1: train_basic_ir parent 267150978, candidate 267124724, ratio 0.9999 vs required 0.9900. evaluation 3: train_basic_ir parent 267150984, candidate 289813349, ratio 1.0848. evaluation 5: train_regex_ir parent 366186451, candidate 366467049, ratio 1.0008. evaluation 7: train_basic_ir parent 267150984, candidate 267704387, ratio 1.0021. Builds passed. Equivalence was identical outputs. Guards were within limits.
> The edits did not help. The larger minbpe/basic.py variant hurt train_basic_ir. The regex-side variant did not move train_regex_ir enough.
> Next try a smaller change in minbpe/basic.py or minbpe/regex.py. Measure train_basic_ir and train_regex_ir before keeping it. Session stopped with no candidate.
>
> 2026-10-13, episode `02a04fdab3f3` (no candidate)

> I tried changes in minbpe/basic.py around lines 66-70, 149-172, and 194-196, then backed part of that out at lines 149-181 and 185-187. I changed minbpe/base.py ordered_merge_steps at lines 76-80, then reduced the import at lines 12-14. The submitted change removed the type-set validation from the ordered_merge_steps cache hit.
> evaluation 1 on train_basic_ir was rejected: train_basic_ir parent 263036613, candidate 262922713, ratio 0.9996. evaluation 3 was worse: train_basic_ir parent 263036619, candidate 314724049, ratio 1.1965. evaluation 5 on encode_ir was accepted: encode_ir parent 638115499, candidate 630766592, ratio 0.9885. evaluation 7 on encode_ir was accepted: encode_ir parent 638115499, candidate 630688242, ratio 0.9884. Equivalence was identical outputs each time.
> I now believe rebuilding key and value type sets on a cache hit in ordered_merge_steps is encode work. The minbpe/basic.py train changes did not help train_basic_ir.
> Try the next encode path after ordered_merge_steps. Wait for independent replays on the queued encode_ir candidate.
>
> 2026-10-13, episode `350fca6f3a76` (rejected)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
