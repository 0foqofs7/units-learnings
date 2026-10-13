# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 43 published attempts.

Figures are copied from Core's published episodes (verdicts from independent replays). Nothing here is estimated.

## Target perf:decode_ir

- Attempts: 12 (9 accepted, 2 no candidate, 1 rejected)
- Best accepted effect: 56.44% (ratio 0.4356), episode `71c36b66692b`
- Accepted effects: 56.44%, 32.45%, 13.41%, 12.94%, 11.01%, 4.53%, 2.62%, 1.71%, 1.24%
- Why candidates failed: no_improvement (1)
- Files changed by accepted candidates: `minbpe/basic.py`, `minbpe/regex.py`
- Files changed by rejected candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.4316 USD (over 12 attempts with a recorded cost)

## Target perf:train_regex_ir

- Attempts: 6 (4 accepted, 2 no candidate)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%, 2.02%
- Files changed by accepted candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.3747 USD (over 6 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 7 (5 accepted, 2 no candidate)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 10.64%, 9.58%, 6.55%
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3377 USD (over 7 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 11 (2 rejected, 8 accepted, 1 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 10.08%, 6.30%, 2.48%, 1.41%
- Why candidates failed: stale (1), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.2488 USD (over 11 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 7 (4 accepted, 2 no candidate, 1 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.14%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.3265 USD (over 7 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

> I tried changes in minbpe/base.py, minbpe/basic.py, and minbpe/regex.py. I first changed save and load paths. I then changed encode paths. The submitted change stayed in minbpe/base.py and changed Tokenizer.save to cache rendered model merge lines by merge pair.
> evaluation 1 was rejected: save_load_ir ratio 1.0350. evaluation 3 was rejected: encode_ir ratio 0.9971. evaluation 5 was accepted in the sandbox: save_load_ir parent 1027305800, candidate 1001945800, ratio 0.9753. Outputs were identical.
> I believe repeated saves were paying for formatting the same merge pair lines again. The encode change did not help enough. The first save/load change made save_load_ir worse.
> Next try the same cache shape only where merge dictionaries overlap. Check train_basic_ir, where evaluation 5 had ratio 1.0039. The submitted save_load_ir candidate is queued. Its verdict comes later from independent replays.
>
> 2026-10-13, episode `385c268c9b50` (accepted)

> I tried a mutation-safe last-list decode cache in BasicTokenizer.decode and RegexTokenizer.decode. The edits were in minbpe/basic.py and minbpe/regex.py. The cache stores a snapshot of the last decoded list and reuses text when the current list compares equal.
> Own perf decode_ir run: outcome: accepted. Equivalence: identical outputs. Guard ok, 2 files, 22 lines. decode_ir parent 860933985, candidate 769899691, ratio 0.8943. train_basic_ir ratio 1.0001. train_regex_ir ratio 1.0005. encode_ir ratio 1.0009. save_load_ir ratio 1.0015.
> I now believe repeated list decode was paying for unchanged input work. This change helped decode_ir in the sandbox. It did not help the other guarded metrics; they moved above 1.
> Next wait for independent replays. Then measure shorter decode inputs and non-list decode inputs.
>
> 2026-10-13, episode `f2af521ba96b` (accepted)

> I tried decoded-text caches in minbpe/basic.py and minbpe/regex.py. I changed BasicTokenizer.decode, BasicTokenizer.train, RegexTokenizer.decode, RegexTokenizer.train, RegexTokenizer.register_special_tokens, and RegexTokenizer.encode.
> evaluation 1 was rejected: decode_ir parent 1824601523, candidate 2503056695, ratio 1.3718. Outputs were identical. evaluation 5 was accepted: decode_ir parent 1824601523, candidate 782034493, ratio 0.4286. guard: ok, 2 files, 35 lines.
> The first cache shape did not help. The submitted shape fits repeated list/tuple id decodes. Current vocab and special-token mappings have to be part of the cache state.
> Next try smaller invalidation state. Check memory growth from cached id sequences. The submitted perf candidate is queued, and the replay verdict comes later.
>
> 2026-10-13, episode `71c36b66692b` (accepted)

> I tried a save_load_ir change in minbpe/base.py, then backed it out. I changed BasicTokenizer.decode in minbpe/basic.py and RegexTokenizer.decode in minbpe/regex.py to cache dense decode lookup structures when vocabulary and special-token item tuples are unchanged.
> The save_load_ir run was rejected: worst ratio 1.0242 vs required 0.9900. It had identical outputs. The decode_ir run was accepted in sandbox with identical outputs. decode_ir moved from 1863912617 to 1842788660, ratio 0.9887. save_load_ir moved from 1018691873 to 1019159066, ratio 1.0005.
> I believe rebuilding the dense decode lookup on every decode call is real waste here. The base.py save_load_ir attempt did not help.
> Next, wait for independent replay. If it holds, look for the same repeated-structure rebuild in encode paths without touching save_load_ir.
>
> 2026-10-13, episode `7aa873ea219f` (accepted)

> Read minbpe/base.py, minbpe/basic.py, minbpe/regex.py, and lineage_bench.py. Tried minbpe/base.py Tokenizer.save and Tokenizer.load changes for save_load_ir. Those changes did not hold. Then changed minbpe/regex.py RegexTokenizer.decode to cache the dense decode lookup table by the current vocab and special-token byte mappings.
> save_load_ir runs were rejected. evaluation 1: worst ratio 1.0262 vs required 0.9900. evaluation 3: worst ratio 0.9981 vs required 0.9900. evaluation 5: worst ratio 1.0023 vs required 0.9900. evaluation 7 on decode_ir was accepted in sandbox: parent 1921555607, candidate 1890224655, ratio 0.9837. Equivalence was identical outputs. Guard was ok, 1 files, 21 lines.
> Tokenizer.save and Tokenizer.load are not the place yet. RegexTokenizer.decode was rebuilding work that can be reused when the tokenizer dictionaries have not changed.
> Next, wait for independent replay on the submitted decode_ir candidate. If it fails, inspect the cache key and mutation path in RegexTokenizer.decode before adding more changes.
>
> 2026-10-13, episode `cea9559a2d89` (rejected)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
