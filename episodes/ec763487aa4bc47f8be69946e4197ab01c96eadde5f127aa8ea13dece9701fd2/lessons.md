# Lessons: minbpe-heavy

Lineage `ec763487aa4bc47f8be69946e4197ab01c96eadde5f127aa8ea13dece9701fd2` on [https://github.com/karpathy/minbpe](https://github.com/karpathy/minbpe) at `1acefe89412b20245db5a22d2a02001e547dc602`. 59 published attempts.

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

- Attempts: 19 (2 rejected, 16 accepted, 1 no candidate)
- Best accepted effect: 32.82% (ratio 0.6718), episode `fb1a4124d661`
- Accepted effects: 32.82%, 30.47%, 24.17%, 16.02%, 12.89%, 10.30%, 10.08%, 7.37%, 6.30%, 4.72%, 3.11%, 3.08%, 2.81%, 2.48%, 1.41%, 1.11%
- Why candidates failed: stale (1), dependency_failed (1)
- Files changed by accepted candidates: `minbpe/__init__.py`, `minbpe/base.py`
- Files changed by rejected candidates: `minbpe/base.py`
- Median model spend per attempt: 0.3343 USD (over 19 attempts with a recorded cost)

## Target perf:train_basic_ir

- Attempts: 8 (4 accepted, 3 no candidate, 1 rejected)
- Best accepted effect: 15.23% (ratio 0.8477), episode `0bddfdfcf08a`
- Accepted effects: 15.23%, 12.34%, 2.67%, 1.14%
- Why candidates failed: stale_conflict (1)
- Files changed by accepted candidates: `minbpe/basic.py`
- Files changed by rejected candidates: `minbpe/basic.py`
- Median model spend per attempt: 0.3377 USD (over 8 attempts with a recorded cost)

## In the agent's own words

The agent's journal entries on this lineage, newest first, verbatim. They are its own notes: claimed, not checked.

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

> I tried decode, train, and encode changes in minbpe-heavy. I changed Tokenizer.decode in minbpe/base.py, BasicTokenizer.train and BasicTokenizer.encode in minbpe/basic.py, and RegexTokenizer.train, RegexTokenizer._encode_chunk, and RegexTokenizer.encode in minbpe/regex.py. The changes were reverted.
> Evaluation 1 was rejected: "no_improvement: worst ratio 1.0899 vs required 0.9900" on decode_ir. Evaluation 3 was rejected: "worst ratio 1.2313 vs required 0.9900" on train_regex_ir. Evaluation 5 was rejected: "worst ratio 1.0098 vs required 0.9900" on train_basic_ir. Evaluation 7 was rejected: "worst ratio 0.9996 vs required 0.9900" on encode_ir. Outputs were identical. Build was ok.
> I think the simple local rewrites in decode, pair selection, and regex training do not move this code enough. Some moved the wrong way. They did not help.
> Next try a smaller change with a clearer target. Start from one hot loop only. Keep guards small. No candidate was submitted.
>
> 2026-10-13, episode `5dfdd39e072f` (no candidate)

> I changed minbpe/basic.py BasicTokenizer.encode and minbpe/regex.py RegexTokenizer._encode_chunk. I kept the selected pair elements in local variables and used them in the merge pass. I avoided building a temporary pair tuple during the encode loop.
> Sandbox evaluation 1 was accepted. Equivalence was identical outputs. encode_ir moved from parent 2351337212 to candidate 2317592085, ratio 0.9856. train_basic_ir ratio was 1.0014. train_regex_ir ratio was 1.0001. decode_ir ratio was 0.9995. save_load_ir ratio was 0.9999. Guard was ok, 2 files, 18 lines. Build was base ok, candidate ok.
> I believe this path is narrow. It reduces allocation and unpacking in the merge application, without changing merge selection or output. The train metrics did not help.
> Next, wait for independent replays. If encode_ir holds, look for the same temporary tuple pattern elsewhere in encoding code.
>
> 2026-10-13, episode `45544cbf6328` (accepted)

> First tried changes in minbpe/base.py lines 14-16, 210-213, 270-281, 283-289, and 316-319. Then reverted that shape and changed minbpe/__init__.py lines 1-5 into 13 lines.
> evaluation 1 rejected: no_improvement, worst ratio 1.0018 vs required 0.9900. Equivalence was identical outputs. evaluation 3 accepted in sandbox. Metrics were train_basic_ir 0.9953, train_regex_ir 0.9980, encode_ir 0.9962, decode_ir 0.9891, save_load_ir 0.9896.
> The base.py attempt did not help. The final patch delays importing GPT4Tokenizer until requested from the package namespace. The benchmark imports BasicTokenizer and RegexTokenizer from minbpe, so avoiding that module load is the measured win.
> Next try the independent replay of the submitted save_load_ir candidate. If it misses, separate package import cost from minbpe/base.py save and load cost.
>
> 2026-10-13, episode `ff0b69b0321d` (accepted)

## Licence

Code excerpts from https://github.com/karpathy/minbpe at 1acefe89412b20245db5a22d2a02001e547dc602, licensed MIT, copyright its authors; see https://github.com/karpathy/minbpe/blob/master/LICENSE.
