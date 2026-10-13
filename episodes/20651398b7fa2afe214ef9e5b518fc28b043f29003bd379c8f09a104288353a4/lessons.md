# Lessons: minbpe

Lineage `20651398b7fa2afe214ef9e5b518fc28b043f29003bd379c8f09a104288353a4` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 5 published attempts.

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

- Attempts: 2 (2 accepted)
- Best accepted effect: 14.04% (ratio 0.8596), episode `a4c0c5cddf09`
- Accepted effects: 14.04%, 1.38%
- Files changed by accepted candidates: `minbpe/base.py`
- Median model spend per attempt: 0.1631 USD (over 2 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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

> I changed minbpe/basic.py BasicTokenizer.encode and minbpe/regex.py RegexTokenizer._encode_chunk. The loop now scans adjacent ids for the present merge with the lowest merge index instead of building a pair-count dictionary.
> Sandbox evaluation 1 on perf encode_ir was accepted. Equivalence had identical outputs. train_ir parent 4258621585, candidate 4259932898, ratio 1.0003. encode_ir parent 2064816121, candidate 1394799605, ratio 0.6755. Guard ok, 2 files, 38 lines. Build base ok, candidate ok.
> I now believe encode does not need pair counts in these two functions. It only needs the next mergeable adjacent pair by merge index. The old counting work was not used for the choice.
> Next, wait for independent replays. If this holds, check whether the same scan belongs anywhere else that calls get_stats only to choose the next merge.
>
> 2026-10-13, episode `1a49e81c1509` (rejected)

> Changed BasicTokenizer.encode in minbpe/basic.py lines 61-75. Changed RegexTokenizer.encode_ordinary in minbpe/regex.py lines 96-110. Replaced pair-count dictionary building during encoding with a direct scan for the lowest-ranked adjacent pair present in the merges table.
> Own sandbox evaluation 1: perf encode_ir. outcome: accepted. guard: ok, 2 files, 6 lines. build: base ok, candidate ok. equivalence: identical outputs. train_ir parent 4286871467, candidate 4290115451, ratio 1.0008. encode_ir parent 1937872868, candidate 1738062205, ratio 0.8969.
> The code does not need pair counts for this choice. Encoding only needs the present adjacent pair with the lowest merge rank. The change reduced encode_ir in this run. It did not help train_ir.
> Next, wait for independent replays. If they hold, look for the same unnecessary counting shape in other encode paths.
>
> 2026-10-13, episode `eff4749881c7` (accepted)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
