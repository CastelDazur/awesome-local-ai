# Field notes: KV cache q8_0 vs q4_0

On one machine, q8_0 decoded 65% faster than q4_0 at long context. This is a measurement from one workstation, not a published benchmark.

**Measured on:** RTX 5090 32 GB, Windows 11, llama.cpp b9608 (12 June 2026), Qwen3.6-27B Q4_K_M, context 49152, flash attention on (the default in that build), one slot, `-ngl 999`. Only the cache type changed between runs. June 2026.

## Result

| Prompt | Change with q8_0 instead of q4_0 |
|---|---|
| Long (about 5.3K tokens in) | **+65%**: 35.7 vs 21.6 tokens/s |
| Reasoning | +16% |
| Short | -11% |

q8_0 used about 0.75 GB more VRAM at 49152 context.

## Why, probably

With q4_0 the attention step has to unpack a large cache on every token. At long context that cost is bigger than the memory it saves. This is our reading; we did not profile it.

## How to use it

```bash
llama-server -m model.gguf -c 49152 -ngl 999 \
  --cache-type-k q8_0 --cache-type-v q8_0
```

## Quality

We compared replies to three prompts (short, reasoning, long) in Russian and found q8_0 no worse. Three prompts is not a quality benchmark; treat it as "no obvious loss", not proof.

## One trap

llama.cpp issue [#24166](https://github.com/ggml-org/llama.cpp/issues/24166) (now closed) reported a large slowdown and VRAM thrashing with q8_0 cache on big contexts, after a flash-attention change. It did not show up on b9608. If decode speed falls off a cliff at long context with q8_0, check your build against that issue first.

## When q4_0 is still the right choice

When VRAM is the limit. We had room; if you do not, q4_0 keeps more of it free.

## Measure it yourself

Send one long and one short prompt to `llama-server` and read `timings.predicted_per_second` from the `/completion` response. Change nothing else between runs.
