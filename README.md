# Agentic AI Reasoning Benchmark

This repository evaluates reasoning efficiency in Agentic AI using local vLLM servers and OpenAI GPT-4.1 as the evaluation judge.

---

# Environment

- Python 3.10+
- CUDA-enabled GPU(s)
- vLLM
- HuggingFace Datasets
- Azure OpenAI (for judging)

---

# Installation

Clone the repository.

```bash
git clone <your_repository_url>
cd <repository_name>
```

Install dependencies.

```bash
pip install -r requirements.txt
```

Install vLLM.

```bash
pip install vllm
```

---

# Start Tool Model

Run the tool model on GPU 0.

```bash
CUDA_VISIBLE_DEVICES=0 VLLM_USE_V1=0 vllm serve Qwen/Qwen3.5-4B \
  --host 0.0.0.0 \
  --port 8000 \
  --served-model-name qwen35-4b \
  --trust-remote-code \
  --max-model-len 8192 \
  --gpu-memory-utilization 0.75 \
  --enforce-eager \
  --enable-auto-tool-choice \
  --tool-call-parser qwen3_xml
```

---

# Start Final Model

## DeepSeek-R1-7B

```bash
CUDA_VISIBLE_DEVICES=1 VLLM_USE_V1=0 vllm serve deepseek-ai/DeepSeek-R1-Distill-Qwen-7B \
  --host 0.0.0.0 \
  --port 8001 \
  --served-model-name deepseek-r1-7b \
  --trust-remote-code \
  --max-model-len 8192 \
  --gpu-memory-utilization 0.75 \
  --enforce-eager
```

## Llama-3.1-8B-Instruct

```bash
CUDA_VISIBLE_DEVICES=1 VLLM_USE_V1=0 vllm serve meta-llama/Llama-3.1-8B-Instruct \
  --host 0.0.0.0 \
  --port 8001 \
  --served-model-name llama31-8b \
  --trust-remote-code \
  --max-model-len 8192 \
  --gpu-memory-utilization 0.85 \
  --enforce-eager
```

---

# Run Agentic AI Benchmark

```bash
python main.py \
  --tool_model_choice qwen35-4b \
  --model_choice llama31-8b \
  --tool_base_url http://localhost:8000/v1 \
  --final_base_url http://localhost:8001/v1 \
  --api_key EMPTY \
  --dataset_id HuggingFaceH4/MATH-500 \
  --split test \
  --max_samples 500 \
  --max_tokens 4096
```

---

# Configure Azure OpenAI

```bash
export AZURE_OPENAI_API_KEY="<YOUR_API_KEY>"
export AZURE_OPENAI_BASE_URL="<YOUR_AZURE_ENDPOINT>"
export AZURE_OPENAI_DEPLOYMENT="gpt-4.1"
```

---

# Evaluate Results

```bash
python judge.py \
  --input_file \
  agentic_ai_benchmark/vanilla/tool_qwen35-4b/final_llama31-8b/HuggingFaceH4_MATH_500/vanilla_tool_qwen35-4b_final_llama31-8b_HuggingFaceH4_MATH_500_latest.json \
  --judge_model gpt-4.1
```

---

# Output Directory

```
agentic_ai_benchmark/
└── vanilla/
    └── tool_qwen35-4b/
        └── final_llama31-8b/
            └── HuggingFaceH4_MATH_500/
                ├── *_latest.json
                ├── *_summary.json
                ├── *_memory.sqlite
                └── *_checkpoints.sqlite
```

---

# Supported Models

| Model Choice | HuggingFace Model |
|--------------|-------------------|
| qwen35-4b | Qwen/Qwen3.5-4B |
| qwen3-4b | Qwen/Qwen3-4B |
| qwen3-8b | Qwen/Qwen3-8B |
| llama31-8b | meta-llama/Llama-3.1-8B-Instruct |
| deepseek-r1-7b | deepseek-ai/DeepSeek-R1-Distill-Qwen-7B |

---

# Dataset Example

```bash
--dataset_id HuggingFaceH4/MATH-500
--split test
--max_samples 500
```

---

# Citation

If you use this repository, please cite our paper.

```bibtex
@article{YOUR_CITATION,
  title={Reasoning Efficiency in Agentic AI},
  author={...},
  year={2026}
}
```
