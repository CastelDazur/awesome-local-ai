# Choosing an Inference Engine

Not sure which inference engine to use? This guide helps you pick the right one based on your hardware, use case, and experience level.

## Quick Decision Table

| Your Situation | Best Choice | Why |
|---|---|---|
| Just getting started | [Ollama](https://github.com/ollama/ollama) | One command install, model library, works everywhere |
| Apple Silicon Mac | [MLX LM](https://github.com/ml-explore/mlx-lm) | Native Metal acceleration, best perf per watt on M-series |
| NVIDIA GPU, single user | [llama.cpp](https://github.com/ggml-org/llama.cpp) | GGUF models, low VRAM usage, huge community |
| NVIDIA GPU, max speed | [ExLlamaV2](https://github.com/turboderp/exllamav2) | Fastest single-user inference for EXL2/GPTQ models |
| Serving multiple users | [vLLM](https://github.com/vllm-project/vllm) | PagedAttention, continuous batching, production-ready |
| Multi-turn chat app | [SGLang](https://github.com/sgl-project/sglang) | RadixAttention for KV cache reuse across conversations |
| Intel GPU (Arc/Flex/Max) | [IPEX-LLM](https://github.com/intel/ipex-llm) | Only option with native Intel GPU optimization |
| Need OpenAI API compat | [LocalAI](https://github.com/mudler/LocalAI) | Drop-in replacement, supports 35+ backends |
| Distribute to end users | [llamafile](https://github.com/Mozilla-Ocho/llamafile) | Single executable, no dependencies, cross-platform |
| Embedded / low resources | [Candle](https://github.com/huggingface/candle) | Rust, minimal footprint, no Python dependency |

## Decision Flowchart

```
Do you have a GPU?
├── No → Ollama (CPU mode) or llama.cpp (CPU)
├── NVIDIA
│   ├── Single user? → ExLlamaV2 or llama.cpp
│   ├── Multiple users? → vLLM or SGLang
│   └── Need API server? → LocalAI or vLLM
├── AMD (ROCm)
│   ├── Supported? → llama.cpp or vLLM (ROCm build)
│   └── Not sure? → Ollama (auto-detects)
├── Intel Arc/Flex/Max
│   └── IPEX-LLM
└── Apple Silicon
    └── MLX LM or llama.cpp (Metal)
```

## Model Format Compatibility

| Engine | GGUF | GPTQ | EXL2 | AWQ | FP16 | SafeTensors |
|---|---|---|---|---|---|---|
| llama.cpp | Yes | No | No | No | Convert first | No |
| Ollama | Yes (via llama.cpp) | No | No | No | No | No |
| vLLM | Yes (since v0.6) | Yes | No | Yes | Yes | Yes |
| ExLlamaV2 | No | Yes | Yes | No | Yes | Yes |
| SGLang | No | Yes | No | Yes | Yes | Yes |
| MLX LM | MLX format | No | No | No | Convert first | Convert first |
| LocalAI | Yes | Yes (via backends) | No | No | Yes | Yes |

**Note:** vLLM added GGUF support in late 2024. For GGUF models with batched serving, vLLM is now a strong option alongside llama.cpp.

## Performance Comparison (Rough Guidelines)

Tokens per second for Llama 3 8B on RTX 4090:

| Engine | Prompt Processing | Generation | Notes |
|---|---|---|---|
| ExLlamaV2 (EXL2) | ~8000 t/s | ~140 t/s | Fastest single-user |
| llama.cpp (Q4_K_M) | ~4000 t/s | ~120 t/s | Most flexible |
| vLLM (FP16) | ~6000 t/s | ~100 t/s | Better with batching |
| SGLang | ~6000 t/s | ~110 t/s | Shines in multi-turn |
| Ollama | ~3500 t/s | ~110 t/s | Convenience overhead |

*Numbers vary significantly by model, quantization, context length, and GPU driver version. Run your own benchmarks.*

## Memory Usage Patterns

**llama.cpp / Ollama**: Load model once, VRAM stays constant. Context uses additional memory that scales with length.

**vLLM**: Uses PagedAttention. Allocates VRAM in blocks as needed. More efficient for multiple concurrent requests. Pre-allocates remaining GPU memory by default.

**ExLlamaV2**: Pre-allocates based on max context. Very efficient per-token, but fixed memory footprint.

**SGLang**: Similar to vLLM but with RadixAttention cache that reduces redundant computation in multi-turn conversations.

## Getting Started Commands

### Ollama (easiest)
```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama run llama3.1:8b
```

### llama.cpp (most control)
```bash
git clone https://github.com/ggml-org/llama.cpp
cd llama.cpp
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release -j
./build/bin/llama-cli -m model.gguf -p "Hello" -n 128
```

### vLLM (for serving)
```bash
pip install vllm
vllm serve meta-llama/Llama-3.1-8B-Instruct
```

### ExLlamaV2 (fastest single-user)
```bash
pip install exllamav2
python -m exllamav2.server -m ./model-exl2/ -p 8000
```

---

*Part of [awesome-local-ai](https://github.com/CastelDazur/awesome-local-ai). Corrections and additions welcome via PR.*
