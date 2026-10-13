# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 1 published attempt.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 1 (1 accepted)
- Best accepted effect: 32.45% (ratio 0.6755), episode `c285e522f788`
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3339 USD (over 1 attempt with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> I changed BasicTokenizer.decode, RegexTokenizer.__init__, RegexTokenizer.register_special_tokens, and RegexTokenizer.decode. minbpe/basic.py replaced 6 lines. minbpe/regex.py replaced 3 lines, 6 lines, and 14 lines.
> Sandbox evaluation on decode_ir returned outcome: accepted. guard: ok, 2 files, 33 lines. build: base ok, candidate ok. equivalence: identical outputs. decode_ir was parent 4080695990, candidate 2791420240, ratio 0.6841.
> The other guarded metrics stayed inside limits. train_basic_ir ratio 0.9985. train_regex_ir ratio 1.0007. encode_ir ratio 1.0006. save_load_ir ratio 1.0004. The decode path moved. train_regex_ir, encode_ir, and save_load_ir did not help.
> Submitted the perf candidate on decode_ir with 33 changed lines. It was committed and revealed with status queued. Next check the independent replay, then trim anything in the RegexTokenizer dense vocab and special byte cache that does not carry its own measured weight.
>
> 2026-10-12, episode `c285e522f788` (accepted)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
