---
title: Quantization
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Quantization with TRT-LLM can be achieved by customized engine built. You can get INT8 on A100 and FP8 on H100.
This step is replacing `convert_checkpoint.py`
```sh
python3 /app/tensorrt_llm/examples/quantization/quantize.py --model_dir ${MODEL_CHECKPOINT} \
  --output_dir ${CONVERTED_CHECKPOINT} \
  --dtype ${DTYPE} \
  --tp_size ${TP} \
  --qformat int8_sq \
  --kv_cache_dtype int8 \
  --calib_size 512
```
and the engine built is following similar steps
```sh
trtllm-build --checkpoint_dir ${CONVERTED_CHECKPOINT} \
             --output_dir ${ENGINE}  \
             --max_batch_size ${MAX_BATCH} \
             --max_num_tokens ${MAX_LEN} \
             --gemm_plugin auto \
             --workers 2
```

