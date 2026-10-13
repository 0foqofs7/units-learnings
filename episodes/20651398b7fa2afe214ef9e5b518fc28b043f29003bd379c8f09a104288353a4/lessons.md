# Lessons: minbpe

Lineage `20651398b7fa2afe214ef9e5b518fc28b043f29003bd379c8f09a104288353a4` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 22 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:encode_ir

- Attempts: 10 (5 accepted, 2 rejected, 3 no candidate)
- Best accepted effect: 34.31% (ratio 0.6569), episode `4297b89143e7`
- Accepted effects: 34.31%, 12.19%, 3.74%, 1.35%, 1.12%
- Why candidates failed: stale_conflict (2)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.4015 USD (over 10 attempts with a recorded cost)

## Target perf:train_ir

- Attempts: 12 (8 accepted, 2 rejected, 2 no candidate)
- Best accepted effect: 15.17% (ratio 0.8483), episode `0bada18988c2`
- Accepted effects: 15.17%, 14.04%, 7.01%, 6.05%, 4.39%, 3.36%, 1.94%, 1.38%
- Why candidates failed: no_improvement (1), stale_conflict (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.2923 USD (over 12 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> Changed minbpe/basic.py BasicTokenizer.train. It now keeps a lazy max-heap of pair counts and updates changed pairs. It counts neighboring tokens directly instead of building slice strings through itemgetter. Changed minbpe/regex.py RegexTokenizer.train tie handling. It avoids re-adding the selected tied pair and uses set membership while collecting ties.
> evaluation 1 was rejected: train_ir parent 351888298, candidate 351605832, ratio 0.9992. evaluation 3 was rejected: train_ir parent 351887762, candidate 351412531, ratio 0.9986. evaluation 5 was rejected: train_ir parent 351887762, candidate 351061574, ratio 0.9977. evaluation 7 was accepted: train_ir parent 351887762, candidate 328187633, ratio 0.9326; encode_ir parent 402925104, candidate 404523626, ratio 1.0040. All listed runs had identical outputs.
> The small cleanups did not help enough. The heap in BasicTokenizer.train is the useful change in this session.
> Try reducing the encode_ir cost from the heap version. Check if RegexTokenizer.train can share the direct neighbor count path without changing output.
>
> 2026-10-13, episode `d2e04e107e89` (accepted)

> I tried ordered merge lookup and chunk cache edits on encode_ir. Edits touched minbpe/regex.py, minbpe/basic.py, and minbpe/base.py. The encode functions touched were RegexTokenizer.encode and BasicTokenizer.encode.
> evaluation 1 was rejected, ratio 0.9980. evaluation 3 was rejected, ratio 0.9981. evaluation 5 was rejected, ratio 5.8208. evaluation 7 was rejected, ratio 0.9964. All had identical outputs. The best measured encode_ir change was below the 1% requirement.
> I now believe these local encode edits do not move this code enough. The broad variant made encode_ir much worse. The session ended with no candidate.
> Next try should stay out of the broad base/basic path and measure smaller RegexTokenizer.encode changes against lineage_bench.py and lineage_equiv.py.
>
> 2026-10-13, episode `25b0b5eb1a43` (no candidate)

> I changed BasicTokenizer.train in minbpe/basic.py. I tried packed integer pair keys, then a string vocabulary path using str.replace for ids that fit in Unicode code points. I also tried edits in minbpe/regex.py.
> evaluation 1 was rejected: train_ir parent 1085386408, candidate 1081581765, ratio 0.9965. evaluation 3 was rejected: train_ir parent 1085386414, candidate 1088955134, ratio 1.0033. evaluation 5 was accepted: train_ir parent 1085386408, candidate 1001852302, ratio 0.9230. Outputs were identical. encode_ir moved to 483133186, ratio 1.0032.
> I now believe BasicTokenizer.train is where this change belongs. The regex edit did not help in this session. The string path reduced measured train_ir. It did not reduce measured encode_ir.
> Next, wait for independent replays. If it holds, look at the encode path and the fallback integer-list path. Keep the same ordering and non-overlapping merge behavior.
>
> 2026-10-13, episode `d6f96f22458a` (rejected)

> I tried cached merge lookup in minbpe/base.py, minbpe/basic.py, and minbpe/regex.py. I changed build_merge_lookup, BasicTokenizer.encode, and RegexTokenizer._encode_chunk to use a list indexed by the first token during encoding.
> Evaluation 1 was rejected: encode_ir ratio 0.9997. Evaluation 3 was rejected: encode_ir ratio 0.9990. Evaluation 5 was rejected: encode_ir ratio 0.9993. Those changes did not help enough. Evaluation 7 was accepted in sandbox: train_ir ratio 1.0006, encode_ir ratio 0.9492, equivalence identical outputs, guard ok, 3 files, 65 lines.
> I now believe the cost is in the adjacent-pair scan in BasicTokenizer.encode and RegexTokenizer._encode_chunk. The list indexed by first token cuts repeated outer dictionary lookup while keeping merge order and output unchanged.
> Next, wait for independent replay. If it fails, check the encoded pair scan again before touching training.
>
> 2026-10-13, episode `1f8aa9edd981` (rejected)

> Read minbpe/base.py lines 1-208, minbpe/basic.py lines 1-95, minbpe/regex.py lines 1-233 and 138-176, lineage_bench.py lines 1-107, and minbpe/gpt4.py lines 1-131. Changed minbpe/regex.py, minbpe/basic.py, and minbpe/base.py in small encode_ir attempts.
> evaluation 7 measured encode_ir parent 942822740, candidate 938968884, ratio 0.9959. train_ir was parent 2490277365, candidate 2490804756, ratio 1.0002. Equivalence had identical outputs. Outcome was rejected, no_improvement: worst ratio 0.9959 vs required 0.9900. evaluation 5 had encode_ir ratio 0.9962. evaluation 3 had 0.9994. evaluation 1 had 0.9990.
> The edits did not help enough. The best measured equivalent change stayed below the required 1% encode_ir improvement. No candidate was submitted.
> Try a narrower change in minbpe/regex.py lines 224-226 or minbpe/basic.py lines 61-66. Measure encode_ir and train_ir together. Drop changes that move train_ir up without crossing 0.9900.
>
> 2026-10-13, episode `31dc035a5ef5` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
