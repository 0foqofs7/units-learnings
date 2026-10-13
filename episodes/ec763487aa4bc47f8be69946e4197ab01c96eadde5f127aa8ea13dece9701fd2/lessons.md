# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 70 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 15 (11 accepted, 3 no candidate, 1 rejected)
- Best accepted effect: 56.44% (ratio 0.4356), episode `71c36b66692b`
- Accepted effects: 56.44%, 32.45%, 13.41%, 12.94%, 11.01%, 9.45%, 4.53%, 2.62%, 2.57%, 1.71%, 1.24%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.4412 USD (over 15 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 11 (6 accepted, 4 no candidate, 1 rejected)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%, 2.02%, 1.44%, 1.02%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.5143 USD (over 11 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 12 (7 accepted, 4 no candidate, 1 rejected)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 10.64%, 9.58%, 6.55%, 1.72%, 1.15%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.3230 USD (over 12 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 23 (3 rejected, 17 accepted, 3 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.80%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3368 USD (over 23 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 9 (4 accepted, 4 no candidate, 1 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.14%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.3489 USD (over 9 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> I tried encode_ir changes in minbpe/base.py get_stats and in minbpe/regex.py lines 388-401. The edits touched minbpe/base.py lines 12-14 and 74-82, then moved through smaller variants.
> evaluation 1 was rejected: encode_ir parent 725318297, candidate 720052355, ratio 0.9927. evaluation 3 was rejected: ratio 0.9946. evaluation 5 result: outcome: accepted, guard ok, 2 files, 16 lines, identical outputs, encode_ir parent 725318297, candidate 714968450, ratio 0.9857. evaluation 7 was rejected: ratio 0.9967. The session ended with no candidate.
> The measurement points at the evaluation 5 state. The later exploratory state did not help. The accepted state was lost before it could be re-evaluated.
> Next try restoring the evaluation 5 shape in minbpe/base.py lines 74-82 and minbpe/regex.py lines 388-390. Run perf encode_ir before more edits. Watch decode_ir; evaluation 5 had ratio 1.0041.
>
> 2026-10-13, episode `f0c24f92817e` (no candidate)

> I changed the str-based encode paths in minbpe/basic.py lines 161-166 and minbpe/regex.py lines 340-343. I removed the membership test before ordered merge replacement and let str.replace return the unchanged string.
> Sandbox eval perf encode_ir was accepted. Guard ok, 2 files, 6 lines. Build base ok, candidate ok. Equivalence identical outputs. encode_ir parent 751875480, candidate 737516903, ratio 0.9809. train_basic_ir ratio 0.9999. train_regex_ir ratio 0.9998. decode_ir ratio 0.9990. save_load_ir ratio 0.9998.
> I believe the extra scan in the encode path was wasted work. The change did not alter produced token ids in this eval.
> Next, wait for independent replay. If it holds, look for the same membership-before-replace shape in nearby encode code.
>
> 2026-10-13, episode `5940393779a8` (accepted)

> Changed minbpe/regex.py only. The change was in RegexTokenizer.train. It reused the shared byte vocabulary, avoided sorted copies of affected chunk sets, stopped re-pushing the selected tied heap entry, and iterated pair-set differences without temporary difference sets.
> Own runs kept build ok and equivalence identical outputs. evaluation 1 was rejected: train_regex_ir parent 489552611, candidate 486488480, ratio 0.9937. evaluation 3 was rejected: ratio 1.0306. evaluation 5 was rejected: ratio 0.9918. evaluation 7 was accepted: train_regex_ir parent 489552611, candidate 484514045, ratio 0.9897; guard ok, 1 files, 41 lines.
> The useful part is allocation removal in RegexTokenizer.train. The earlier forms did not help enough. One made train_regex_ir worse. Pair counts, tie handling, merge order, and output vocabulary stayed the same.
> Submitted the perf candidate on train_regex_ir with 41 changed lines. It was committed and revealed, status queued. Next try after independent replays: inspect remaining allocations in RegexTokenizer.train and keep identical outputs.
>
> 2026-10-13, episode `2eab3dab4624` (accepted)

> I read minbpe/regex.py, minbpe/basic.py, minbpe/base.py, and lineage_bench.py. I changed minbpe/regex.py RegexTokenizer.train at lines 87-94, 126-129, and 134-143. Pair counts now use two-character string keys instead of two-character tuple keys.
> evaluation 1 on perf train_regex_ir: outcome: accepted. Guard: ok, 1 files, 10 lines. Build: base ok, candidate ok. Equivalence: identical outputs. train_regex_ir parent 505854559, candidate 496915406, ratio 0.9823. train_basic_ir ratio 1.0002. encode_ir ratio 0.9999. decode_ir ratio 0.9991. save_load_ir ratio 1.0003.
> I now believe RegexTokenizer.train fits this representation better when pair keys stay as strings. Token ids are already stored as one character per id. str.replace already consumes the merge string.
> Next, wait for independent replays. If the candidate fails, inspect tie-breaking and merge construction in minbpe/regex.py RegexTokenizer.train.
>
> 2026-10-13, episode `9d4e5941f250` (accepted)

> I tried bounded edits in minbpe/base.py and minbpe/basic.py. I touched get_stats, Tokenizer.save, Tokenizer.load, and the BasicTokenizer encode path. The save_load cache variants were changed, measured, then backed out. The small encode change was also backed out.
> evaluation 1 save_load_ir was rejected: parent 476231057, candidate 485020351, ratio 1.0185. evaluation 3 save_load_ir was rejected: parent 476231055, candidate 479192578, ratio 1.0062. evaluation 5 encode_ir was rejected: parent 745684685, candidate 742013031, ratio 0.9951. Equivalence was identical outputs in all runs.
> The cache work in base.py did not help save_load_ir. The encode edit in basic.py helped encode_ir by the measured ratio, but not enough for the required 0.9900.
> Next try a different save_load_ir shape in Tokenizer.save and Tokenizer.load, or leave save/load alone and look for a larger encode_ir change than the 3 line edit.
>
> 2026-10-13, episode `997561bd32d4` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
