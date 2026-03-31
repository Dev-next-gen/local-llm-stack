# local-llm-stack

> Production-grade local LLM deployment — 14B to 80B parameters, AMD ROCm, zero cloud dependency.

## Overview

Battle-tested configurations and tooling for running large language models locally at scale.
Built and maintained by Léo Camus — 10+ years of infrastructure, deployed on real AMD GPU clusters.

## Models Supported

| Model | Parameters | Quantization | VRAM Required |
|-------|-----------|--------------|---------------|
| Mistral / Mixtral | 7B / 8x7B | Q4_K_M, Q5_K_S | 6–48 GB |
| LLaMA 3 | 8B / 70B | Q4_K_M → Q8_0 | 6–80 GB |
| Qwen2.5 | 14B / 72B | Q4_K_M | 10–80 GB |
| DeepSeek-R1 | 14B / 70B | Q4_K_M | 10–80 GB |
| Command-R+ | 104B | Q3_K_S | 80 GB+ |

## Stack

```
┌─────────────────────────────────────────────────────┐
│                   Applications                       │
├──────────────┬──────────────┬────────────────────────┤
│  OpenAI API  │  REST API    │  LangChain / CrewAI    │
│  Compatible  │  (FastAPI)   │  Integrations          │
├──────────────┴──────────────┴────────────────────────┤
│               Inference Engine                        │
│   llama.cpp  │  Ollama  │  KoboldCpp  │  LM Studio  │
├─────────────────────────────────────────────────────-┤
│              Model Layer (GGUF/GGML)                  │
├──────────────────────────────────────────────────────┤
│              GPU Backend                              │
│     AMD ROCm 6.x    │    NVIDIA CUDA    │   CPU      │
└──────────────────────────────────────────────────────┘
```

## Quick Start

```bash
# Clone and setup
git clone https://github.com/Dev-next-gen/local-llm-stack
cd local-llm-stack

# Install llama.cpp with ROCm support
./scripts/install-llamacpp-rocm.sh

# Launch server (14B model, 128k context)
./scripts/launch-server.sh --model models/qwen2.5-14b-q4_k_m.gguf --ctx 131072 --gpu-layers 99

# Or with Ollama
./scripts/launch-ollama.sh
```

## AMD ROCm Configuration

```bash
# Environment setup for AMD GPUs
export HSA_OVERRIDE_GFX_VERSION=11.0.0  # RX 7900 XTX
export ROCR_VISIBLE_DEVICES=0,1,2,3,4   # 5x GPU setup
export HIP_VISIBLE_DEVICES=0,1,2,3,4

# Optimal flags for llama.cpp on ROCm
CMAKE_ARGS="-DGGML_HIPBLAS=on -DAMDGPU_TARGETS=gfx1100"
```

## Performance Benchmarks

| Setup | Model | Tokens/sec | Context |
|-------|-------|-----------|---------|
| 5x RX 7900 XTX | Qwen2.5-72B Q4 | ~18 t/s | 131072 |
| 5x RX 7900 XTX | DeepSeek-14B Q8 | ~45 t/s | 131072 |
| Single RX 7900 XTX | Mistral-7B Q5 | ~120 t/s | 32768 |

## Directory Structure

```
local-llm-stack/
├── scripts/
│   ├── install-llamacpp-rocm.sh
│   ├── launch-server.sh
│   ├── launch-ollama.sh
│   └── benchmark.sh
├── configs/
│   ├── server-14b.json
│   ├── server-80b.json
│   └── ollama-modelfiles/
├── models/          # GGUF files (git-ignored)
├── docker/
│   ├── Dockerfile.rocm
│   └── docker-compose.yml
└── docs/
    ├── rocm-setup.md
    ├── multi-gpu.md
    └── benchmarks.md
```

## License

MIT — Léo Camus / [NextGen Labs](https://nextgen-labs.net)
