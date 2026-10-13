# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 62 published attempts.

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

- Attempts: 8 (4 accepted, 4 no candidate)
- Best accepted effect: 21.19% (ratio 0.7881), episode `b39c7cc4d86a`
- Accepted effects: 21.19%, 18.19%, 14.36%, 2.02%
- Files changed by accepted candidates: `minbpe/regex.py`
- Median model spend per attempt: 0.5029 USD (over 8 attempts with a recorded cost)

## Target perf:encode_ir

- Attempts: 9 (6 accepted, 3 no candidate)
- Best accepted effect: 24.45% (ratio 0.7555), episode `a9cb1663eff2`
- Accepted effects: 24.45%, 20.23%, 10.64%, 9.58%, 6.55%, 1.15%
- Files changed by accepted candidates: `minbpe/base.py`, `minbpe/basic.py`, `minbpe/gpt4.py`, `minbpe/regex.py`
- Median model spend per attempt: 0.3377 USD (over 9 attempts with a recorded cost)

## Target perf:save_load_ir

- Attempts: 21 (3 rejected, 16 accepted, 2 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (2), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3368 USD (over 21 attempts with a recorded cost)

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

> I tried save/load caching variants in minbpe/base.py and a regex training counting/replacement rewrite in minbpe/regex.py. The edits touched base.py lines 1-2, 7-10, 14-16, 23-26, 72-76, 148-167, 217-223, 273-295, 308-325, and regex.py lines 88-100.
> save_load_ir was rejected twice: 1.0254 and 1.0008 vs required 0.9900. decode_ir was rejected at 1.0004. train_regex_ir was rejected at 1.1635. Builds were ok. Equivalence was identical outputs. Guards were ok.
> I now believe these variants did not help this code. The save/load changes moved too little or got slower. The regex rewrite made train_regex_ir slower.
> Next try a smaller change around encode_ir, since it showed 0.9989, 0.9987, and 0.9994 in rejected runs. Do not carry over the regex rewrite.
>
> 2026-10-13, episode `abb69b21704c` (no candidate)

> Tried BasicTokenizer.train changes in minbpe/basic.py: replace most_common with max, reuse BYTE_VOCAB, and adjust loop lines. Tried base._build_vocab prefix caching in minbpe/base.py. Tried RegexTokenizer.train short-chunk filtering in minbpe/regex.py.
> evaluation 1 train_basic_ir was rejected: train_basic_ir ratio 1.0110. evaluation 3 train_basic_ir was rejected: train_basic_ir ratio 0.9995. evaluation 5 save_load_ir was rejected: save_load_ir ratio 1.0258. evaluation 7 train_regex_ir was rejected: train_regex_ir ratio 1.0542. Each build was base ok, candidate ok, with identical outputs.
> BasicTokenizer.train did not move enough from these edits. BYTE_VOCAB reuse only showed ratio 0.9995, below required 0.9900. _build_vocab prefix caching adds work on save/load. Filtering chunks in RegexTokenizer.train hurts train_regex_ir.
> Next try fewer allocations inside get_stats or merge paths in minbpe/basic.py. Or measure pretokenization costs in RegexTokenizer.train before changing the chunk loop.
>
> 2026-10-13, episode `c3cb8ce878fd` (no candidate)

> Changed minbpe/base.py. Changed _build_vocab to cache the most recent merge-only vocabulary prefix and reuse it when a tokenizer's merges extend that sequence. The submitted candidate touched 26 changed lines for save_load_ir.
> perf save_load_ir outcome: accepted. guard: ok, 1 files, 26 lines. build: base ok, candidate ok. equivalence: identical outputs. train_basic_ir ratio 1.0005, train_regex_ir ratio 1.0008, encode_ir ratio 1.0005, decode_ir ratio 1.0022, save_load_ir ratio 0.9698.
> The cache fits this workload. save_load_ir repeatedly builds vocabularies from extending merge prefixes. The unchanged prefix does not need the same byte concatenations again. The other guarded metrics moved up, so the change is not free.
> Watch independent replay next. If it holds, inspect whether _build_vocab cache scope can stay simple. If it does not hold, remove the cache or narrow it to save/load construction.
>
> 2026-10-13, episode `803791462295` (rejected)

> Changed RegexTokenizer.train in minbpe/regex.py and BasicTokenizer.train in minbpe/basic.py. Tried integer-packed pair counts, Counter-based counting, and smaller preprocessing/findall edits. All attempted edits were reverted.
> Evaluation 1 was rejected, train_regex_ir ratio 1.0155. Evaluation 3 was rejected, train_regex_ir ratio 1.2240. Evaluation 5 was rejected, train_regex_ir ratio 0.9998. Evaluation 7 was rejected, train_regex_ir ratio 0.9994. Equivalence was identical outputs each time.
> The measured changes in RegexTokenizer.train did not help enough. The smaller changes moved train_regex_ir by only about 0.06%, below the 1% minimum. The guarded metrics stayed within limit.
> Try a different path in RegexTokenizer.train. Keep the output identical. Measure train_regex_ir before touching more surface area.
>
> 2026-10-13, episode `08e29116de7c` (no candidate)

> Tried merge-map reuse in minbpe/base.py with build_merge_map and get_cached_merge_map. Wired it through minbpe/basic.py and minbpe/regex.py. Tried special-token encode inlining in regex.py.
> evaluation 1 was rejected: no_improvement, worst ratio 0.9988 vs required 0.9900. encode_ir was parent 2273935302, candidate 2271294091, ratio 0.9988. evaluation 3 was rejected: no_improvement, worst ratio 0.9970 vs required 0.9900. encode_ir was parent 2273884202, candidate 2267009808, ratio 0.9970. evaluation 5 was rejected: tests_fail, 7 stable tests fail. Earlier guarded metrics were within limits and outputs were identical.
> The encode path has little spare cost in this shape. Reusing merge maps helped, but not enough. The special-token path is easy to break.
> Next try should stay inside _encode_chunk or the byte-pair loop. Keep GPT4Tokenizer tests first. Avoid touching special-token splitting unless equality with tiktoken is checked immediately.
>
> 2026-10-13, episode `012322d66144` (no candidate)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
