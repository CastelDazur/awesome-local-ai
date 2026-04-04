# Getting Started with Local AI

Run AI models on your own hardware in under 10 minutes. No cloud account needed.

## Prerequisites

- A computer with at least 8 GB RAM (16 GB+ recommended)
- A GPU helps but is not required (CPU inference works, just slower)
- NVIDIA GPU users: install [CUDA drivers](https://developer.nvidia.com/cuda-downloads)
- 10-50 GB free disk space depending on models you want to run

## Step 1: Install Ollama (2 minutes)

[Ollama](https://github.com/ollama/ollama) is the fastest way to get a local model running.

**Linux / WSL:**
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**macOS:**
```bash
brew install ollama
```

**Windows:**
Download from [ollama.com/download](https://ollama.com/download)

## Step 2: Run Your First Model (3 minutes)

```bash
ollama run llama3.1:8b
```

This downloads Llama 3.1 8B (~4.7 GB) and starts an interactive chat. Type a message and press Enter.

```
>>> What is the capital of France?
The capital of France is Paris.

>>> Explain quicksort in simple terms
...
```

Type `/bye` to exit.

## Step 3: Try Different Models

| Command | Model | Size | Good For |
|---|---|---|---|
| `ollama run llama3.1:8b` | Llama 3.1 8B | 4.7 GB | General chat, reasoning |
| `ollama run mistral` | Mistral 7B | 4.1 GB | Fast, good quality |
| `ollama run codellama:7b` | Code Llama 7B | 3.8 GB | Code generation |
| `ollama run phi3:mini` | Phi-3 Mini | 2.3 GB | Small, surprisingly capable |
| `ollama run llama3.1:70b` | Llama 3.1 70B | 40 GB | Best open model (needs 48GB+ VRAM) |
| `ollama run gemma2:9b` | Gemma 2 9B | 5.4 GB | Google model, good at facts |

Full model list: [ollama.com/library](https://ollama.com/library)

## Step 4: Add a Chat UI (5 minutes)

[Open WebUI](https://github.com/open-webui/open-webui) gives you a ChatGPT-like interface for your local models.

```bash
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

Open http://localhost:3000 in your browser. Create an account (stored locally), select a model, and start chatting.

No Docker? Try [Jan](https://jan.ai) (desktop app) or [Msty](https://msty.app) instead.

## Step 5: Use the API

Ollama runs an OpenAI-compatible API at `http://localhost:11434`:

```bash
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.1:8b",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

Works with any OpenAI SDK by changing the base URL:

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:11434/v1", api_key="unused")
response = client.chat.completions.create(
    model="llama3.1:8b",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response.choices[0].message.content)
```

## What to Explore Next

| Topic | Guide |
|---|---|
| Which inference engine should I use? | [Choosing an Inference Engine](choosing-inference-engine.md) |
| Will this model fit on my GPU? | [VRAM Requirements](vram-requirements.md) |
| Build a RAG pipeline | Check RAG & Knowledge section in [README](../README.md#rag--knowledge) |
| Code completion in VS Code | Look at [Continue](https://github.com/continuedev/continue) or [Tabby](https://github.com/TabbyML/tabby) |
| Speech-to-text locally | Start with [Faster-Whisper](https://github.com/SYSTRAN/faster-whisper) |
| Image generation | Try [ComfyUI](https://github.com/comfyanonymous/ComfyUI) or [Fooocus](https://github.com/lllyasviel/Fooocus) |

## Troubleshooting

**Model downloads slowly**: Ollama stores models in `~/.ollama/models`. Make sure you have enough disk space.

**Out of memory (OOM)**: Your model is too large for your GPU. Try a smaller quantization (`ollama run llama3.1:8b-q4_0`) or a smaller model.

**No GPU acceleration**: Run `ollama ps` to check if GPU is being used. For NVIDIA, ensure CUDA drivers are installed (`nvidia-smi` should work).

**Slow generation**: CPU inference is 5-20x slower than GPU. If you only have CPU, stick with 3B-7B models.

---

*Part of [awesome-local-ai](https://github.com/CastelDazur/awesome-local-ai). Corrections welcome via PR.*
