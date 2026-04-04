# VRAM Requirements for Local AI Models

Quick reference for estimating GPU memory needs. Use this before downloading a model to avoid OOM crashes.

## The Formula

```
VRAM (GB) ≈ Parameters (B) × Bytes per Parameter + Context Overhead
```

| Quantization | Bytes per Param | Example: 7B Model | Example: 70B Model |
|---|---|---|---|
| FP32 | 4.0 | 28 GB | 280 GB |
| FP16 / BF16 | 2.0 | 14 GB | 140 GB |
| Q8_0 (8-bit) | 1.0 | 7 GB | 70 GB |
| Q6_K | 0.75 | 5.3 GB | 53 GB |
| Q5_K_M | 0.63 | 4.4 GB | 44 GB |
| **Q4_K_M** | **0.50** | **3.5 GB** | **35 GB** |
| Q3_K_M | 0.38 | 2.7 GB | 27 GB |
| Q2_K | 0.25 | 1.8 GB | 18 GB |
| IQ4_XS | 0.47 | 3.3 GB | 33 GB |
| IQ3_M | 0.34 | 2.4 GB | 24 GB |
| IQ2_M | 0.22 | 1.5 GB | 15 GB |
| EXL2 4.0bpw | 0.50 | 3.5 GB | 35 GB |
| GPTQ 4-bit | 0.50 | 3.5 GB | 35 GB |
| AWQ 4-bit | 0.50 | 3.5 GB | 35 GB |

**Q4_K_M** is the community sweet spot: minimal quality loss, major VRAM savings.

**IQ quants** (importance matrix quantization) are newer llama.cpp formats that squeeze more quality from fewer bits. IQ4_XS approaches Q4_K_M quality at slightly less VRAM.

## What Fits on Your GPU

Add ~1-2 GB overhead for KV cache and system usage on top of model size.

| GPU | VRAM | Max Model (Q4_K_M) | Max Model (FP16) | Good For |
|---|---|---|---|---|
| RTX 4060 | 8 GB | 7B-13B | 3B | Personal chat, code assist |
| RTX 4060 Ti 16GB | 16 GB | 13B-20B | 7B | Solid all-rounder |
| RTX 4070 Ti Super | 16 GB | 13B-20B | 7B | Same VRAM, faster compute |
| RTX 4080 | 16 GB | 13B-20B | 7B | Faster throughput |
| RTX 4090 | 24 GB | 30B-34B | 13B | Prosumer sweet spot |
| RTX 5090 | 32 GB | 70B (tight) | 14B | Most open models fit |
| 2x RTX 4090 | 48 GB | 70B comfortable | 20B-34B | Enthusiast multi-GPU |
| A100 40GB | 40 GB | 70B (Q4) | 20B | Datacenter standard |
| A100 80GB | 80 GB | 70B (FP16) | 34B | Production serving |
| H100 80GB | 80 GB | 70B (FP16) | 34B | Fastest inference |
| H200 141GB | 141 GB | 120B+ (FP16) | 70B | Next-gen datacenter |
| Apple M2 Ultra | 192 GB (unified) | 120B+ | 70B+ | Unified memory advantage |
| Apple M4 Max | 128 GB (unified) | 70B+ | 34B+ | Desktop Mac option |

## Popular Models: Quick VRAM Check

| Model | Parameters | Q4_K_M Size | FP16 Size | Min GPU |
|---|---|---|---|---|
| Phi-3 Mini 3.8B | 3.8B | ~2.5 GB | ~7.6 GB | 4 GB (Q4) |
| Llama 3.1 8B | 8B | ~5 GB | ~16 GB | 8 GB (Q4) |
| Mistral 7B v0.3 | 7.2B | ~4.5 GB | ~14.5 GB | 8 GB (Q4) |
| Gemma 2 9B | 9.2B | ~5.5 GB | ~18.5 GB | 8 GB (Q4) |
| Qwen 2.5 14B | 14B | ~8.5 GB | ~28 GB | 12 GB (Q4) |
| Qwen 2.5 Coder 32B | 32B | ~19 GB | ~64 GB | 24 GB (Q4) |
| Mistral Small 24B | 24B | ~14 GB | ~48 GB | 16 GB (Q4) |
| Llama 3.3 70B | 70B | ~40 GB | ~140 GB | 48 GB (Q4) |
| Llama 3.1 70B | 70B | ~40 GB | ~140 GB | 48 GB (Q4) |
| Qwen 2.5 72B | 72B | ~41 GB | ~144 GB | 48 GB (Q4) |
| Mixtral 8x7B | 46.7B (active: 12.9B) | ~26 GB | ~93 GB | 32 GB (Q4) |
| DeepSeek R1 | 671B (37B active) | ~21 GB active | ~1.3 TB total | Special: MoE |
| DeepSeek V3 | 671B (37B active) | ~21 GB active | ~1.3 TB total | Special: MoE |
| Command R+ 104B | 104B | ~59 GB | ~208 GB | 2x A100 80GB |

**Notes on MoE models** (Mixtral, DeepSeek): These use Mixture of Experts architecture. Total parameter count is high, but only a fraction activates per token. You need VRAM for all parameters (they must be loaded), but compute is proportional to active parameters only. DeepSeek R1/V3 require ~350 GB VRAM minimum for all layers on GPU, or can offload to CPU RAM with significant speed penalty.

**Distilled variants**: DeepSeek R1 also has distilled versions (1.5B, 7B, 8B, 14B, 32B, 70B) based on Llama/Qwen that run like standard dense models. These are much more practical for local use.

## Context Length and VRAM

Context window (the amount of text the model can process at once) consumes additional VRAM via the KV cache:

| Context Length | Extra VRAM (7B model) | Extra VRAM (70B model) |
|---|---|---|
| 2K tokens | ~0.1 GB | ~0.5 GB |
| 4K tokens | ~0.2 GB | ~1 GB |
| 8K tokens | ~0.5 GB | ~2 GB |
| 16K tokens | ~1 GB | ~4 GB |
| 32K tokens | ~2 GB | ~8 GB |
| 128K tokens | ~8 GB | ~32 GB |

Rule of thumb: if you need long context (32K+), budget extra 20-30% VRAM.

KV cache size depends on model architecture (number of KV heads, head dimension). Models with Grouped Query Attention (GQA) like Llama 3 use less KV cache than older Multi-Head Attention models.

## Tips for Fitting Larger Models

1. **Quantize lower**: Q3_K_M instead of Q4_K_M saves ~25% VRAM with noticeable but often acceptable quality loss
2. **Try IQ quants**: IQ4_XS and IQ3_M can squeeze more quality from fewer bits than standard K-quants
3. **Reduce context**: 4K context instead of 8K cuts KV cache in half
4. **Offload layers to CPU**: llama.cpp `-ngl` flag lets you split between GPU and CPU RAM (slower but works)
5. **Use Flash Attention**: Reduces memory for long contexts. Enabled by default in vLLM, ExLlamaV2, and recent llama.cpp
6. **Enable KV cache quantization**: llama.cpp supports Q8_0 and Q4_0 KV cache (`--cache-type-k q8_0`) reducing context memory by 2-4x
7. **Close other GPU apps**: Browsers, video players, and display rendering all consume VRAM
8. **CPU offloading for MoE**: For models like DeepSeek V3, offload expert layers to CPU while keeping attention on GPU

## Multi-GPU Configurations

| Setup | Total VRAM | Use Case |
|---|---|---|
| 2x RTX 3090 | 48 GB | Budget 70B inference |
| 2x RTX 4090 | 48 GB | Fast 70B inference |
| 4x RTX 4090 | 96 GB | 70B FP16 or 120B+ Q4 |
| 2x A100 80GB | 160 GB | Production 70B+ serving |
| 8x H100 80GB | 640 GB | Full DeepSeek V3/R1, large-scale serving |

**Tensor parallelism** (vLLM, TGI) splits layers across GPUs for lower latency. Requires NVLink or high-bandwidth interconnect for best results.

**Pipeline parallelism** (llama.cpp) loads different layers on different GPUs. Works over PCIe but adds latency per layer boundary.

## Useful Tools

- [Can I Run This Model?](https://huggingface.co/spaces/Vokturz/can-it-run-llm) - Web calculator for VRAM estimation
- [gpu-memory-guard](https://github.com/CastelDazur/gpu-memory-guard) - CLI tool to check VRAM before loading
- `nvidia-smi` - Check current VRAM usage: `watch -n 1 nvidia-smi`
- `ollama ps` - See loaded models and their VRAM usage
- [LM Studio](https://lmstudio.ai) - Shows estimated VRAM in the model browser before download

---

*Part of [awesome-local-ai](https://github.com/CastelDazur/awesome-local-ai). Data current as of April 2026. Corrections welcome via PR.*# VRAM Requirements for Local AI Models

Quick reference for estimating GPU memory needs. Use this before downloading a model to avoid OOM crashes.

## The Formula

```
VRAM (GB) ≈ Parameters (B) × Bytes per Parameter + Context Overhead
```

| Quantization | Bytes per Param | Example: 7B Model | Example: 70B Model |
|---|---|---|---|
| FP32 | 4.0 | 28 GB | 280 GB |
| FP16 / BF16 | 2.0 | 14 GB | 140 GB |
| Q8_0 (8-bit) | 1.0 | 7 GB | 70 GB |
| Q6_K | 0.75 | 5.3 GB | 53 GB |
| Q5_K_M | 0.63 | 4.4 GB | 44 GB |
| **Q4_K_M** | **0.50** | **3.5 GB** | **35 GB** |
| Q3_K_M | 0.38 | 2.7 GB | 27 GB |
| Q2_K | 0.25 | 1.8 GB | 18 GB |
| EXL2 4.0bpw | 0.50 | 3.5 GB | 35 GB |
| GPTQ 4-bit | 0.50 | 3.5 GB | 35 GB |
| AWQ 4-bit | 0.50 | 3.5 GB | 35 GB |

**Q4_K_M** is the community sweet spot: minimal quality loss, major VRAM savings.

## What Fits on Your GPU

Add ~1-2 GB overhead for KV cache and system usage on top of model size.

| GPU | VRAM | Max Model (Q4_K_M) | Max Model (FP16) | Good For |
|---|---|---|---|---|
| RTX 4060 | 8 GB | 7B-13B | 3B | Personal chat, code assist |
| RTX 4060 Ti 16GB | 16 GB | 13B-20B | 7B | Solid all-rounder |
| RTX 4070 Ti Super | 16 GB | 13B-20B | 7B | Same VRAM, faster compute |
| RTX 4080 | 16 GB | 13B-20B | 7B | Faster throughput |
| RTX 4090 | 24 GB | 30B-34B | 13B | Prosumer sweet spot |
| RTX 5090 | 32 GB | 70B (tight) | 14B | Most open models fit |
| 2× RTX 4090 | 48 GB | 70B comfortable | 20B-34B | Enthusiast multi-GPU |
| A100 40GB | 40 GB | 70B (Q4) | 20B | Datacenter standard |
| A100 80GB | 80 GB | 70B (FP16) | 34B | Production serving |
| H100 80GB | 80 GB | 70B (FP16) | 34B | Fastest inference |
| Apple M2 Ultra | 192 GB (unified) | 120B+ | 70B+ | Unified memory advantage |
| Apple M4 Max | 128 GB (unified) | 70B+ | 34B+ | Desktop Mac option |

## Popular Models: Quick VRAM Check

| Model | Parameters | Q4_K_M Size | FP16 Size | Min GPU |
|---|---|---|---|---|
| Llama 3.1 8B | 8B | ~5 GB | ~16 GB | 8 GB (Q4) |
| Mistral 7B | 7.2B | ~4.5 GB | ~14.5 GB | 8 GB (Q4) |
| Gemma 2 9B | 9.2B | ~5.5 GB | ~18.5 GB | 8 GB (Q4) |
| Llama 3.1 70B | 70B | ~40 GB | ~140 GB | 48 GB (Q4) |
| Qwen 2.5 72B | 72B | ~41 GB | ~144 GB | 48 GB (Q4) |
| Mixtral 8x7B | 46.7B (active: 12.9B) | ~26 GB | ~93 GB | 32 GB (Q4) |
| Phi-3 Mini 3.8B | 3.8B | ~2.5 GB | ~7.6 GB | 4 GB (Q4) |
| Command R+ 104B | 104B | ~59 GB | ~208 GB | 2× A100 80GB |
| DeepSeek V3 | 671B (37B active) | ~21 GB active | ~1.3 TB total | Special: MoE |

## Context Length and VRAM

Context window (the amount of text the model can process at once) consumes additional VRAM:

| Context Length | Extra VRAM (7B model) | Extra VRAM (70B model) |
|---|---|---|
| 2K tokens | ~0.1 GB | ~0.5 GB |
| 4K tokens | ~0.2 GB | ~1 GB |
| 8K tokens | ~0.5 GB | ~2 GB |
| 16K tokens | ~1 GB | ~4 GB |
| 32K tokens | ~2 GB | ~8 GB |
| 128K tokens | ~8 GB | ~32 GB |

Rule of thumb: if you need long context (32K+), budget extra 20-30% VRAM.

## Tips for Fitting Larger Models

1. **Quantize lower**: Q3_K_M instead of Q4_K_M saves ~25% VRAM with noticeable but often acceptable quality loss
2. **Reduce context**: 4K context instead of 8K cuts KV cache in half
3. **Offload layers to CPU**: llama.cpp `-ngl` flag lets you split between GPU and CPU RAM (slower but works)
4. **Use Flash Attention**: Reduces memory for long contexts. Enabled by default in vLLM and ExLlamaV2
5. **Close other GPU apps**: Browsers, video players, and display rendering all consume VRAM

## Useful Tools

- [Can I Run This Model?](https://huggingface.co/spaces/Vokturz/can-it-run-llm) - Web calculator
- [gpu-memory-guard](https://github.com/CastelDazur/gpu-memory-guard) - CLI tool to check VRAM before loading
- `nvidia-smi` - Check current VRAM usage: `watch -n 1 nvidia-smi`
- `ollama ps` - See loaded models and their VRAM usage

---

*Part of [awesome-local-ai](https://github.com/CastelDazur/awesome-local-ai). Data current as of April 2026. Corrections welcome via PR.*
