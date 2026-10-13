# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 26 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 7 (6 accepted, 1 no candidate)
- Best accepted effect: 32.45% (ratio 0.6755), episode `c285e522f788`
- Accepted effects: 32.45%, 13.41%, 12.94%, 4.53%, 2.62%, 1.71%
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3996 USD (over 7 attempts with a recorded cost)

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

- Attempts: 6 (4 accepted, 1 no candidate, 1 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.14%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.3093 USD (over 6 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Changed minbpe/regex.py in RegexTokenizer.decode. Edits touched lines 112-128, 114-116, and 121-139. The decode path now uses a dense lookup table when vocabulary ids are contiguous, including registered special token ids. Invalid ids fall back to the existing validation path.
> Sandbox evaluation 1 for perf decode_ir said outcome: accepted. Guard: ok, 1 files, 39 lines. Build: base ok, candidate ok. Equivalence: identical outputs. decode_ir was parent 2235377357, candidate 1946395860, ratio 0.8707. train_basic_ir ratio 0.9970. train_regex_ir ratio 1.0008. encode_ir ratio 0.9998. save_load_ir ratio 1.0003.
> I now believe RegexTokenizer.decode has a valid-input path worth keeping when ids are contiguous. train_regex_ir and save_load_ir did not help. The invalid-id path should stay plain.
> Submitted the perf candidate on decode_ir. It was committed and revealed, status queued. Next, read the independent replays when they arrive and recheck decode_ir with the guarded metrics.
>
> 2026-10-13, episode `39b532f00c1c` (accepted)

> I read minbpe/basic.py, minbpe/base.py, minbpe/regex.py, and lineage_bench.py. I changed BasicTokenizer.train in minbpe/basic.py. I tried skipping output allocation until the selected pair is found, then copying the unchanged prefix with a slice and scanning the remainder.
> evaluation 1 on train_basic_ir was rejected: no_improvement, worst ratio 1.1033 vs required 0.9900. It had identical outputs. train_basic_ir parent 3145683643, candidate 3470542824, ratio 1.1033. evaluation 3 was accepted. It had identical outputs. train_basic_ir parent 3145683643, candidate 3095553199, ratio 0.9841. Guards were within limits. The submitted candidate had 18 changed lines and is queued.
> The early version made BasicTokenizer.train worse. The later version helped train_basic_ir in my sandbox and left train_regex_ir 0.9997, encode_ir 0.9993, decode_ir 1.0011, save_load_ir 1.0001.
> Next, wait for independent replays. If they agree, look for the same prefix skip pattern in train paths that build lists before knowing a replacement exists.
>
> 2026-10-13, episode `d3441478db55` (rejected)

> Changed minbpe/basic.py in BasicTokenizer.train. Tried three merge-loop shapes. The last one delayed allocation of ids until the selected pair was first seen, copied the unchanged prefix with a slice, then scanned the rest.
> evaluation 1 was rejected: train_basic_ir parent 2998607427, candidate 2989032044, ratio 0.9968, required 0.9900. evaluation 3 was rejected: train_basic_ir parent 2998607427, candidate 3018372262, ratio 1.0066. evaluation 5 was accepted: train_basic_ir parent 2998607190, candidate 2956730924, ratio 0.9860. Equivalence was identical outputs. Guards were ok.
> The useful part is avoiding per-element append work before the first selected pair in BasicTokenizer.train. The earlier forms did not help. The selected pair comes from current adjacent-pair counts, so the ids and merges stayed unchanged in sandbox.
> The perf candidate was submitted on train_basic_ir with 25 changed lines, committed and revealed, status queued. Next, wait for independent replays. If this holds there, inspect the remaining merge-pass work in BasicTokenizer.train.
>
> 2026-10-13, episode `20026b10bf31` (accepted)

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

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
