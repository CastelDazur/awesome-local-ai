# Awesome Local AI [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

[![GitHub stars](https://img.shields.io/github/stars/CastelDazur/awesome-local-ai?style=social)](https://github.com/CastelDazur/awesome-local-ai/stargazers)
[![Last commit](https://img.shields.io/github/last-commit/CastelDazur/awesome-local-ai)](https://github.com/CastelDazur/awesome-local-ai/commits/main)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: CC0](https://img.shields.io/badge/License-CC0_1.0-lightgrey.svg)](LICENSE)

> A curated list of tools, frameworks, and resources for running AI models on your own hardware. No cloud required.

If you run models on your own GPU instead of calling an API, this list is for you. Every tool listed here works offline after initial setup.

## Contents

- [Inference Engines](#inference-engines)
- [Python & Language Bindings](#python--language-bindings)
- [Quantization & Optimization](#quantization--optimization)
- [Fine-Tuning](#fine-tuning)
- [RAG & Knowledge](#rag--knowledge)
- [Orchestration & Agents](#orchestration--agents)
- [Model Management](#model-management)
- [Monitoring & Profiling](#monitoring--profiling)
- [UI & Interfaces](#ui--interfaces)
- [Code Assistants](#code-assistants)
- [Voice & Audio](#voice--audio)
- [Image & Video](#image--video)
- [Privacy-First Tools](#privacy-first-tools)
- [Unified Gateways](#unified-gateways)
- [Guides](#guides)
- [Hardware Guides](#hardware-guides)

---

## Inference Engines

Run LLMs on your machine.

- [llama.cpp](https://github.com/ggml-org/llama.cpp) - LLM inference in C/C++ with GGUF quantization. The backbone of most local AI setups. Supports NVIDIA, AMD, Intel, Apple Silicon.
- [Ollama](https://github.com/ollama/ollama) - Docker-like experience for running local models. One command to download and run. Wraps llama.cpp with a model registry.
- [vLLM](https://github.com/vllm-project/vllm) - High-throughput serving engine with PagedAttention for efficient VRAM usage. Production-grade, supports GGUF and most formats.
- [MLX LM](https://github.com/ml-explore/mlx-lm) - Apple Silicon optimized inference. Uses unified memory efficiently. If you have an M-series Mac, start here.
- [LocalAI](https://github.com/mudler/LocalAI) - OpenAI API-compatible server supporting 35+ backends. Drop-in replacement for any OpenAI client.
- [ExLlamaV2](https://github.com/turboderp/exllamav2) - Fast single-user inference on NVIDIA GPUs for EXL2 and GPTQ quantized models. Low VRAM overhead.
- [llamafile](https://github.com/mozilla-ai/llamafile) - Distribute LLMs as single executable files. No install, just run. Cross-platform.
- [KoboldCpp](https://github.com/LostRuins/koboldcpp) - One-file GGUF inference with built-in UI. Good for creative writing and roleplay.
- [SGLang](https://github.com/sgl-project/sglang) - Structured generation with RadixAttention for fast multi-turn conversations. Good for agentic workloads.
- [TensorRT-LLM](https://github.com/NVIDIA/TensorRT-LLM) - NVIDIA optimized inference. High throughput on NVIDIA hardware, more setup required.
- [Candle](https://github.com/huggingface/candle) - Minimalist ML framework in Rust. Low memory footprint, good for embedded use cases.
- [GPT4All](https://github.com/nomic-ai/gpt4all) - Desktop app for local models. Download a model, start chatting. Includes document Q&A.
- [LM Studio](https://lmstudio.ai/) - Desktop app for discovering, downloading, and running local LLMs. Built-in chat UI and local API server. Good for non-technical users.
- [IPEX-LLM](https://github.com/intel/ipex-llm) - Optimized inference for Intel GPUs (Arc, Flex, Max series). Required for native Intel GPU acceleration.
- [mistral.rs](https://github.com/EricLBuehler/mistral.rs) - Rust-based inference with ISQ quantization and multimodal support. Low memory usage.
- [MLC LLM](https://github.com/mlc-ai/mlc-llm) - Universal deployment of LLMs across hardware backends. Compile once, run anywhere (GPU, mobile, browser).
- [Xinference](https://github.com/xorbitsai/inference) - Distributed inference platform for LLMs, speech, images. Built-in model hub and OpenAI-compatible API.
- [FastChat](https://github.com/lm-sys/FastChat) - Serving platform from LMSYS (creators of Chatbot Arena). Multi-model serving with OpenAI-compatible API.

## Python & Language Bindings

Use inference engines from your code.

- [llama-cpp-python](https://github.com/abetlen/llama-cpp-python) - Python bindings for llama.cpp with OpenAI-compatible API server. The most popular way to use llama.cpp from Python.
- [Ollama Python](https://github.com/ollama/ollama-python) - Python client for Ollama API. Simple, well-documented.
- [Ollama JS](https://github.com/ollama/ollama-js) - JavaScript/TypeScript client for Ollama.
- [ctransformers](https://github.com/marella/ctransformers) - Python bindings for GGML models with GPU acceleration. Lighter alternative to llama-cpp-python.

## Quantization & Optimization

Make models smaller and faster without retraining.

- [GPTQ](https://github.com/AutoGPTQ/AutoGPTQ) - Post-training quantization for GPT models. 4-bit with minimal quality loss. Widely used on HuggingFace.
- [AWQ](https://github.com/mit-han-lab/llm-awq) - Activation-aware weight quantization. Often better quality than GPTQ at same bit width.
- [bitsandbytes](https://github.com/bitsandbytes-foundation/bitsandbytes) - 8-bit and 4-bit quantization for PyTorch. Required for QLoRA fine-tuning.
- [xformers](https://github.com/facebookresearch/xformers) - Memory-efficient attention implementations. Saves VRAM on long contexts.
- [NVIDIA Model Optimizer](https://github.com/NVIDIA/TensorRT-Model-Optimizer) - Quantization, pruning, and distillation tools for TensorRT and vLLM.
- [llama.cpp convert scripts](https://github.com/ggml-org/llama.cpp/blob/master/convert_hf_to_gguf.py) - Convert HuggingFace models to GGUF format. Essential for llama.cpp and Ollama.

## Fine-Tuning

Train and adapt models on your own data locally.

- [Unsloth](https://github.com/unslothai/unsloth) - 2x faster fine-tuning with 80% less memory. QLoRA and LoRA on a single GPU.
- [axolotl](https://github.com/axolotl-ai-cloud/axolotl) - Streamlined fine-tuning with YAML configs. Supports LoRA, QLoRA, DPO, RLHF. Handles multi-GPU setups.
- [PEFT](https://github.com/huggingface/peft) - Parameter-Efficient Fine-Tuning from HuggingFace. LoRA, prefix tuning, adapters.
- [TRL](https://github.com/huggingface/trl) - Transformer Reinforcement Learning. RLHF, DPO, PPO training for language models. Works with PEFT.
- [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory) - Web UI for fine-tuning 100+ models. No coding needed. Supports LoRA, QLoRA, full fine-tuning.
- [torchtune](https://github.com/pytorch/torchtune) - PyTorch-native fine-tuning library. Clean, hackable, well-documented. From the PyTorch team.

## RAG & Knowledge

Build local knowledge systems without sending your data anywhere.

- [LlamaIndex](https://github.com/run-llama/llama_index) - Data framework for RAG pipelines. Index your docs, query with LLMs. Supports 160+ data sources.
- [LangChain](https://github.com/langchain-ai/langchain) - Framework for chaining LLM calls with tools, memory, and retrieval. Large ecosystem of integrations.
- [ChromaDB](https://github.com/chroma-core/chroma) - Embedded vector database. pip install, start storing embeddings. No server needed.
- [Qdrant](https://github.com/qdrant/qdrant) - Vector database with filtering, payload storage, and GPU acceleration. Good for production workloads.
- [Milvus](https://github.com/milvus-io/milvus) - Scalable vector database. Overkill for personal use, good for teams and large datasets.
- [Weaviate](https://github.com/weaviate/weaviate) - Vector database with hybrid search (vectors + BM25). Built-in modules for text/image.
- [FAISS](https://github.com/facebookresearch/faiss) - Similarity search library from Meta. Fast, battle-tested. The underlying engine many tools use.
- [Sentence-Transformers](https://github.com/huggingface/sentence-transformers) - Compute embeddings locally. Pair with any vector DB above. 5000+ pre-trained models.
- [Unstructured](https://github.com/Unstructured-IO/unstructured) - Extract text from PDFs, Word docs, HTML, images. Pre-processing for RAG pipelines.

## Orchestration & Agents

Coordinate multiple models and tools locally.

- [CrewAI](https://github.com/crewaiinc/crewai) - Multi-agent framework with role-based collaboration. Works with local models via Ollama.
- [LangGraph](https://github.com/langchain-ai/langgraph) - Stateful multi-actor orchestration. Build complex agent workflows as graphs.
- [AutoGen](https://github.com/microsoft/autogen) - Microsoft multi-agent framework. Note: AG2 is the community fork with active development.
- [AG2](https://github.com/ag2ai/ag2) - Community-driven fork of AutoGen. Active development, multi-agent orchestration.
- [Semantic Kernel](https://github.com/microsoft/semantic-kernel) - Microsoft SDK for AI agent apps. .NET, Python, Java. Enterprise-focused.
- [DSPy](https://github.com/stanfordnlp/dspy) - Programming framework for LM pipelines. Optimizes prompts and weights automatically. From Stanford NLP.
- [Dify](https://github.com/langgenius/dify) - Visual workflow builder for LLM apps. Drag-and-drop RAG, agents, chatbots. Self-hostable with local model support.
- [Flowise](https://github.com/FlowiseAI/Flowise) - Low-code LLM app builder with drag-and-drop UI. Connects to Ollama and local APIs.

## Model Management

Download, convert, and organize your local model collection.

- [HuggingFace Hub](https://github.com/huggingface/huggingface_hub) - Download models from HuggingFace. CLI and Python API. Where most open models are hosted.
- [Transformers](https://github.com/huggingface/transformers) - Load and run most open models. Foundation library for HuggingFace ecosystem.
- [GGML](https://github.com/ggml-org/ggml) - Tensor library behind llama.cpp. Low-level but fast. Powers the GGUF ecosystem.
- [Ollama Model Library](https://ollama.com/library) - Pre-quantized models ready to pull with one command. Easiest way to get started.

## Monitoring & Profiling

Know what your GPU is actually doing.

- [GPUStack](https://github.com/gpustack/gpustack) - Manage GPU clusters. Scheduling, monitoring, multi-node inference.
- [nvitop](https://github.com/XuehaiPan/nvitop) - Interactive NVIDIA GPU monitoring. Like htop for GPUs. Real-time process and memory tracking.
- [Triton Inference Server](https://github.com/triton-inference-server/server) - NVIDIA production inference server with model analytics and dynamic batching.
- [Langfuse](https://github.com/langfuse/langfuse) - Open-source LLM observability. Tracing, evals, and prompt management. Self-hostable.
- [LangSmith](https://smith.langchain.com/) - LLM tracing and evaluation platform. Cloud-based (not local), but useful for debugging local model pipelines.
- [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) - Built-in NVIDIA tool. `watch -n 1 nvidia-smi` for real-time GPU monitoring.

## UI & Interfaces

Chat with your local models through a proper interface.

- [Open WebUI](https://github.com/open-webui/open-webui) - ChatGPT-like interface for Ollama and OpenAI-compatible APIs. RAG, web search, model switching built in. Active development.
- [Text Generation WebUI](https://github.com/oobabooga/text-generation-webui) - Gradio UI with extensions. Supports many backends and model formats. Highly configurable.
- [Jan](https://github.com/janhq/jan) - Desktop app for local AI. Clean UI, offline-first. Downloads and manages models for you.
- [Msty](https://msty.app) - Desktop app connecting to local and remote models. Free tier available. Clean, native feel.
- [Hollama](https://github.com/fmaclen/hollama) - Minimal web UI for Ollama. Lightweight alternative to Open WebUI. Quick to set up.
- [SillyTavern](https://github.com/SillyTavern/SillyTavern) - Chat interface with character cards, group chats, extensions. Popular in the creative/RP community.

## Code Assistants

AI-powered coding without sending your code to the cloud.

- [Continue](https://github.com/continuedev/continue) - VS Code / JetBrains AI pair programmer. Works with Ollama, llama.cpp, any local model.
- [Tabby](https://github.com/TabbyML/tabby) - Self-hosted code completion and chat. Runs on your GPU. GitHub Copilot alternative.
- [Aider](https://github.com/Aider-AI/aider) - AI pair programming in your terminal. Edit files, run tests, commit. Works with local models via Ollama/LiteLLM.
- [llm](https://github.com/simonw/llm) - CLI tool for interacting with LLMs. Plugins for local models. From the creator of Datasette.

## Voice & Audio

Speech recognition and synthesis on your hardware.

- [Whisper](https://github.com/openai/whisper) - OpenAI speech-to-text. Runs locally despite the name. Multiple model sizes from tiny to large-v3.
- [Faster-Whisper](https://github.com/SYSTRAN/faster-whisper) - 4x faster Whisper using CTranslate2. Same quality, less VRAM. Drop-in replacement.
- [WhisperX](https://github.com/m-bain/whisperX) - Whisper with word-level timestamps and speaker diarization. Good for transcription workflows.
- [Piper](https://github.com/rhasspy/piper) - Fast local text-to-speech. Runs on a Raspberry Pi. Many voice models available.
- [Speaches](https://github.com/speaches-ai/speaches) - OpenAI-compatible speech API server. Drop-in local replacement for OpenAI TTS/STT.
- [WhisperKit](https://github.com/argmaxinc/WhisperKit) - On-device speech recognition optimized for Apple Silicon. Swift framework.
- [WhisperLive](https://github.com/collabora/WhisperLive) - Real-time streaming transcription. Low latency, works with microphone input.
- [Bark](https://github.com/suno-ai/bark) - Text-to-audio generation. Voice cloning, music, sound effects. Runs locally on GPU.
- [F5-TTS](https://github.com/SWivid/F5-TTS) - High-quality text-to-speech with voice cloning from 15s reference audio. Diffusion-based.

## Image & Video

Generate and edit images locally.

- [ComfyUI](https://github.com/Comfy-Org/ComfyUI) - Node-based workflow for Stable Diffusion, FLUX, SD3, SDXL. Visual pipeline editor.
- [Stable Diffusion WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) - Feature-rich Gradio interface for Stable Diffusion. Large extension ecosystem.
- [Forge](https://github.com/lllyasviel/stable-diffusion-webui-forge) - Optimized SD WebUI fork. Better VRAM efficiency and speed. Good for new setups.
- [Fooocus](https://github.com/lllyasviel/Fooocus) - Simplified image generation. Fewer options, faster results. Good for non-technical users.
- [InvokeAI](https://github.com/invoke-ai/InvokeAI) - Creative engine for Stable Diffusion with professional UI. Node-based workflows.
- [FLUX.1](https://github.com/black-forest-labs/flux) - Image generation from Black Forest Labs. FLUX.1-dev runs locally on 12GB+ VRAM.

## Privacy-First Tools

Interact with documents without data leaving your machine.

- [PrivateGPT](https://github.com/zylon-ai/private-gpt) - Chat with your documents 100% privately. RAG pipeline built in. No data leaves your machine.
- [LocalGPT](https://github.com/PromtEngineer/localGPT) - Chat with documents using local models. Inspired by PrivateGPT with more model options.
- [Danswer](https://github.com/danswer-ai/danswer) - Search and chat over internal documents. Can use local models. Self-hosted.
- [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) - All-in-one AI desktop app with local RAG and agent support. Connects to Ollama.

## Unified Gateways

Route requests across multiple model backends.

- [LiteLLM](https://github.com/BerriAI/litellm) - Call 100+ LLM APIs through one interface. Route between local and cloud. Load balancing, fallbacks, cost tracking.
- [OpenRouter](https://openrouter.ai) - Unified API for many providers. Cloud-based (not local), but useful as fallback when local GPU is busy.

## Guides

Step-by-step guides for common tasks.

- [Getting Started](guides/getting-started.md) - Install Ollama, run your first model, add a chat UI, use the API. Under 10 minutes.
- [Choosing an Inference Engine](guides/choosing-inference-engine.md) - Decision tables, performance benchmarks, and model format compatibility across 10 engines.
- [VRAM Requirements](guides/vram-requirements.md) - GPU memory calculator, what fits on your card, popular model sizes, and tips for fitting larger models.

---

## Hardware Guides

Figure out what you need and what fits.

- [GPU Benchmarks for LLM Inference](https://github.com/XiongjieDai/GPU-Benchmarks-on-LLM-Inference) - Performance comparison across consumer GPUs. Real benchmarks, not marketing.
- [Can I Run This Model?](https://huggingface.co/spaces/Vokturz/can-it-run-llm) - VRAM calculator. Enter your GPU, see what fits.
- [LocalLLaMA Wiki](https://www.reddit.com/r/LocalLLaMA/wiki/) - Community-maintained knowledge base for local AI hardware and software.
- [LLM VRAM Requirements](https://www.substratus.ai/blog/calculating-gpu-memory-for-llm/) - How to calculate VRAM needs for any model.

---

## Contributing

Contributions welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) first.

Rules:
- Only include actively maintained projects (commit within last 6 months)
- One tool per line, one line description
- No marketing language. Describe what it does, not how great it is
- Include a personal note if you've used it ("Good for X", "Overkill for Y")
- Local-first means it runs on your hardware. Cloud-only tools don't belong here

## Related Lists

- [awesome-llm](https://github.com/Hannibal046/Awesome-LLM) - Broader LLM resources
- [awesome-generative-ai](https://github.com/steven2358/awesome-generative-ai) - Generative AI tools and research
- [awesome-self-hosted](https://github.com/awesome-selfhosted/awesome-selfhosted) - Self-hosted software

---

*Maintained by [CastelDazur](https://github.com/CastelDazur). 100+ tools across 15 categories. Updated April 2026.*
