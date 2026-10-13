# Lessons: minbpe

Lineage `20651398b7fa2afe214ef9e5b518fc28b043f29003bd379c8f09a104288353a4` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 7 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:encode_ir

- Attempts: 3 (2 accepted, 1 rejected)
- Best accepted effect: 34.31% (ratio 0.6569), episode `4297b89143e7`
- Accepted effects: 34.31%, 12.19%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.2023 USD (over 3 attempts with a recorded cost)

## Target perf:train_ir

- Attempts: 4 (3 accepted, 1 rejected)
- Best accepted effect: 15.17% (ratio 0.8483), episode `0bada18988c2`
- Accepted effects: 15.17%, 14.04%, 1.38%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.1834 USD (over 4 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Changed minbpe/regex.py RegexTokenizer.train at lines 70-80. It skips regex-training chunks shorter than two tokens when collecting pair counts and when applying a merge.
> Own sandbox evaluation perf train_ir had outcome: accepted. guard: ok, 1 files, 5 lines. build: base ok, candidate ok. equivalence: identical outputs. train_ir: parent 3093707638, candidate 3001903254, ratio 0.9703. encode_ir: parent 1122634032, candidate 1122743414, ratio 1.0001.
> Chunks shorter than two tokens cannot contribute adjacent pairs and cannot be changed by a pair merge. The old path spent helper calls and scans there.
> Submitted the perf candidate on train_ir. Status is waiting. Next, let independent replays decide it, then measure the same kind of skipped work before touching more code.
>
> 2026-10-13, episode `eb1be42ef413` (rejected)

> I read minbpe/base.py, minbpe/basic.py, minbpe/regex.py, minbpe/gpt4.py, and lineage_bench.py. I changed minbpe.regex.RegexTokenizer.train and the merge helper path in minbpe/regex.py.
> evaluation 1 was perf train_ir. outcome: accepted. equivalence: identical outputs. train_ir was parent 3518650345, candidate 2973852171, ratio 0.8452. encode_ir was parent 964998222, candidate 965493059, ratio 1.0005.
> I believe the win is from not allocating replacement lists for chunks that do not contain the selected pair. The encode path did not improve.
> Next try the same rule in the other training paths only if they copy unchanged chunks during merges. The candidate was submitted on train_ir and is queued. The verdict comes later from independent replays.
>
> 2026-10-13, episode `0bada18988c2` (accepted)

> BasicTokenizer.encode and RegexTokenizer.encode_ordinary changed. The encode-time min(zip(...), key=...) pair selection was replaced with an explicit adjacent-token scan that keeps the lowest merge rank found before calling merge.
> evaluation 1: perf encode_ir. outcome: accepted. guard: ok, 2 files, 44 lines. build: base ok, candidate ok. equivalence: identical outputs. train_ir: parent 3624834717, candidate 3625117532, ratio 1.0001. encode_ir: parent 1411355096, candidate 950213975, ratio 0.6733.
> The scan helped encode_ir in this sandbox. train_ir moved slightly the wrong way. The behavior stayed the same on the measured cases.
> Next, wait for independent replays. If it holds, check whether the same pair-scan shape belongs in any other encode path without touching training code.
>
> 2026-10-13, episode `4297b89143e7` (accepted)

> I read minbpe/base.py, minbpe/basic.py, minbpe/regex.py, lineage_bench.py, and minbpe/gpt4.py. I changed minbpe/base.py merge, lines 32-49. I cached the output append method, input length, and pair elements outside the scan loop.
> Sandbox evaluation 1 on perf train_ir reported outcome: accepted. Guard: ok, 1 files, 11 lines. Build: base ok, candidate ok. Equivalence: identical outputs. train_ir was parent 3654716726, candidate 3601435829, ratio 0.9854. encode_ir was parent 1780634910, candidate 1653029918, ratio 0.9283.
> I now believe merge was paying for repeated lookups and length computations inside the training scan. The change touched the shared helper and did not change token output.
> Next, wait for independent replays. If the replay holds, inspect the encode path around the same helper, because encode_ir moved in the same sandbox run.
>
> 2026-10-13, episode `f3f2543ac7ad` (accepted)

> I read minbpe/basic.py, minbpe/base.py, minbpe/regex.py, and lineage_bench.py. I changed minbpe/base.py lines 13-23 in get_stats. The edit replaced 11 lines by 18, using a list iterator instead of zip with ids[1:].
> perf train_ir said outcome: accepted. guard: ok, 1 files, 9 lines. build: base ok, candidate ok. equivalence: identical outputs. train_ir parent 4279562463, candidate 3682954929, ratio 0.8606. encode_ir parent 1746632871, candidate 1747529673, ratio 1.0005.
> I now believe get_stats paid for the sliced copy from ids[1:]. This change preserved pair counts and ordering. encode_ir did not help.
> The candidate was submitted on train_ir and is queued. The verdict comes later from independent replays. Next try: inspect other adjacent-id walks before changing behavior.
>
> 2026-10-13, episode `a4c0c5cddf09` (accepted)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
