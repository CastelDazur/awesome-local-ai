# Field notes: MTP speculative decoding in llama.cpp

If your GGUF contains the model's multi-token-prediction heads, llama.cpp can draft tokens with them and verify in one pass. On our setup that gave +39% tokens/s. Many GGUFs do not contain the heads, and then the flag does nothing.

**Measured on:** RTX 5090 32 GB, Windows 11, llama.cpp b9608 (12 June 2026), Qwen3.6-27B Q4_K_M with the heads present, q8_0 KV cache, Russian conversational prompts. June 2026. One machine.

## Flags

```bash
llama-server -m model.gguf --spec-type draft-mtp --spec-draft-n-max 2
```

No separate draft model is needed. The heads are small tensors named `blk.N.nextn.*` inside the model file.

## Results (our measurements)

| Setting | Speed vs no drafting |
|---|---|
| `draft-mtp`, n-max 2 | **+39%** on average, up to +55% on long answers |
| n-max 3 | same as n-max 2 |
| n-max 3-4 with `--spec-draft-p-min` 0.1-0.2 | 0.78x to 0.94x of the n-max 2 speed (slower) |
| `ngram-map-k` (no heads needed) | 9% slower than no drafting, on Russian text |

By design the verified output is what the model would produce without drafting.

## The trap: no heads in the file

A GGUF without the heads loads and runs normally, and drafting does nothing. Many published quants we checked had no `nextn` tensors at all. Check before you download or benchmark:

```python
open("model.gguf", "rb").read(4_000_000).count(b"nextn") > 0
```

Or use `merge-mtp verify model.gguf` from [merge-quantize-keep-mtp](https://github.com/CastelDazur/merge-quantize-keep-mtp).

## Quantizing does not remove them

When we quantized our own model with `llama-quantize` to Q5_K_M, all four `blk.64.nextn.*` tensors were still there. The heads are missing when the source model lacked them or the checkpoint was assembled without them, not because of the quantization step.

## Our current setup does not use it

On the Qwen 3.8-based model we run now, MTP drafting is off. We did not see a worthwhile gain for our workload. The numbers above are for Qwen3.6 and do not carry over, so measure on your own model.
