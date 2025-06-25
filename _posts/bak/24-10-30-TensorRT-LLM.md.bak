---
title: TensorRL-LLM
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

To understand NIM, you can not avoid deep undertanding of TRT-LLM, Triton and even vLLM. Those will be focus for the near future.

This is more like a note of how to hosting a LLM on Triton with TRTLLM backend
 
## 1 Build the Container
The workspace is a container built for Triton Server. The [instructions](https://github.com/triton-inference-server/tensorrtllm_backend/blob/main/docs/build.md) is for TensorRT-LLM backend. 
1. Build the TensorRT-LLM Backend from source  
-> This should only build TRTLLM backend 
2. Build the Docker Container
Option 1. Build the NGC Triton TRT-LLM container    
-> This build the exactly same container on NGC
Option 2. Build via Docker  
-> I actually built the container with this method

## 2 Build the engine 
The engine built is with `trtllm-build` command and multiple [examples](https://github.com/NVIDIA/TensorRT-LLM/tree/f6821ee393be6ec92234f9bb47a4b09f6738050b/examples) can be found

## 3 Host with Triton
This Llama [example](https://github.com/triton-inference-server/tensorrtllm_backend/blob/main/docs/llama.md) shows the whole process, including engine building. The triton host part is about creating proper `config.pbtxt` files for each process steps
1. Preprocessing
2. Postprocessing
3. tensorrt_llm_bls or ensamble
  - Choose one with pre/postprocessing
4. tensorrt_llm 
  - This is standalone

## 4 Endpoint test
```sh
# Choose one model
MODEL_NAME='ensamble' 
MODEL_NAME='tensorrt_llm_bls'

curl -X POST localhost:8000/v2/models/${MODEL_NAME}/generate \
  -d '{ 
    "text_input": "What is machine learning?", 
    "max_tokens": 20,
    "bad_words": "",
    "stop_words": ""
    }'
```

## 5 Real examples
```shell
MODEL_CHECKPOINT="mixtral-8x7b-instruct-v01_vhf-a60832c-b"
OUTPUT_MODEL="MRG-Mistral8x7"

CONVERTED_CHECKPOINT="${MODEL_CHECKPOINT}-converted"
TOKENIZER=${MODEL_CHECKPOINT}


DTYPE=float16
TP=2
PP=1
MAX_LEN=13000
MAX_BATCH=128
MAX_LORA_RANK=32


SUFFIX=/trtllm_engine
ENGINE=${OUTPUT_MODEL}/${SUFFIX}

# Build lora enabled engine
python3 /app/tensorrt_llm/examples/llama/convert_checkpoint.py --model_dir ${MODEL_CHECKPOINT} \
  --output_dir ${CONVERTED_CHECKPOINT} \
  --dtype ${DTYPE} \
  --tp_size ${TP} \
  --moe_tp_size ${TP}

trtllm-build \
  --checkpoint_dir ${CONVERTED_CHECKPOINT} \
  --output_dir ${ENGINE} \
  --max_num_tokens $MAX_LEN --use_paged_context_fmha enable \
  --gemm_plugin float16
```