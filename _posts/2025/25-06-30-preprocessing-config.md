---
title: Preprocessing config in vLLM
mathjax: true
toc: true
categories:
  - OSS
tags:
  - VLM
---

There are `preprocessing_config.json` in HF weight

## 1 Read in
The file is read in by `AutoImageProcessor` and code is [here](https://github.com/vllm-project/vllm/blob/main/vllm/transformers_utils/processor.py#L185-L190)
```python
def get_image_processor(...):
    """Load an image processor for the given model name via HuggingFace."""
    try:
        processor = AutoImageProcessor.from_pretrained(...)
    ...
```
Here is a similar class `AutoProcessor` and the code is [here](https://github.com/vllm-project/vllm/blob/main/vllm/transformers_utils/processor.py#L71-L92)
```python
def get_processor(...) -> _P:
    """Load a processor for the given model name via HuggingFace."""
    processor_factory = (AutoProcessor if processor_cls == ProcessorMixin or
                         isinstance(processor_cls, tuple) else processor_cls)
    try:
        processor = processor_factory.from_pretrained(...)
    ...
```