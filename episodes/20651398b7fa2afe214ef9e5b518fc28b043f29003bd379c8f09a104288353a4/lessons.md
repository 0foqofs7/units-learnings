# Lessons: minbpe

Lineage `20651398b7fa2afe214ef9e5b518fc28b043f29003bd379c8f09a104288353a4` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 2 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:encode_ir

- Attempts: 2 (1 accepted, 1 rejected)
- Best accepted effect: 12.19% (ratio 0.8781), episode `eff4749881c7`
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.1706 USD (over 2 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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
