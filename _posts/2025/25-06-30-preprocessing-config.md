---
title: Preprocessing/Processor in vLLM
mathjax: true
toc: true
categories:
  - OSS
tags:
  - VLM
---

Here are preprocessing related code in vLLM

## 0 Processors vs Image Processors
1. [Processors](https://huggingface.co/docs/transformers/en/main_classes/processors)
- Multimodal models require a preprocessor capable of handling inputs that combine more than one modality. For example, **PaliGemma** is a vision-language model that uses the **SigLIP** image processor and the **Llama** tokenizer. 
- A `ProcessorMixin(PushtoPubMixin)` class wraps both of these preprocessor types
- [AutoClass](https://huggingface.co/docs/transformers/en/model_doc/auto#transformers.AutoProcessor) API provides a simple interface to load processors 
```python
from transformers import AutoProcessor
processor = AutoProcessor.from_pretrained("google/paligemma-3b-pt-224")
```
2. [Image Processors](https://huggingface.co/docs/transformers/en/main_classes/image_processor#image-processor)
- Image processors converts images into pixel values and tensors
- `ImageProcessingMixin(PushtoPubMixin)` class can do `center_crop`, `normalize` and `rescale`.`BaseImageProcessor(ImageProcessingMixin)` is a Python implementation and `BaseImageProcessorFast(BaseImageProcessor)` is a faster torchvision-backed version.
- Loaded by [AutoClass](https://huggingface.co/docs/transformers/en/model_doc/auto#transformers.AutoImageProcessor) as well.
```python
from transformers import AutoImageProcessor
image_processor = AutoImageProcessor.from_pretrained("google/vit-base-patch16-224", use_fast=True)
```

## 1 Read in `preprocessing_config.json`
The file is read in by `AutoImageProcessor` and code is [here](https://github.com/vllm-project/vllm/blob/main/vllm/transformers_utils/processor.py#L185-L190)
```python
from transformers import AutoImageProcessor
def get_image_processor(...):
    """Load an image processor for the given model name via HuggingFace."""
    try:
        processor = AutoImageProcessor.from_pretrained(...)
    ...
```
Here is a similar class `AutoProcessor` and the code is [here](https://github.com/vllm-project/vllm/blob/main/vllm/transformers_utils/processor.py#L71-L92)
```python
from functools import lru_cache
from transformers.processing_utils import ProcessorMixin
def cached_processor_from_config(
    model_config: "ModelConfig",
    processor_cls = ProcessorMixin,) -> _P:
    return cached_get_processor(...)
cached_get_processor = lru_cache(get_processor)
def get_processor(...) -> _P:
    """Load a processor for the given model name via HuggingFace."""
    processor_factory = (AutoProcessor if processor_cls == ProcessorMixin or
                         isinstance(processor_cls, tuple) else processor_cls)
    try:
        processor = processor_factory.from_pretrained(...)
    ...
```

## 2 The logic of `get_hf_processor`
Define `get_hf_processor` in the model registry code. The logic is that you will get 
- HF processor by calling `self.ctx.get_hf_processor`. Examle in Qwen2 model
```python
from transformers.models.qwen2_vl import (Qwen2VLImageProcessor,Qwen2VLProcessor)
class Qwen2VLProcessingInfo(BaseProcessingInfo):
    def get_hf_config(self):
        return self.ctx.get_hf_config(Qwen2VLConfig)
    def get_hf_processor(...) -> Qwen2VLProcessor:
        return self.ctx.get_hf_processor(
            Qwen2VLProcessor,
            image_processor=...Qwen2VLImageProcessor...
        )
```
Here both processor and image processor are already defined in tranformers
- Self defined HF-lish processor by `self.ctx.init_processor`
In this way, you need to define your processor
```python
class BaseInternVLProcessor(ABC):
  ...
class InternVLProcessor(BaseInternVLProcessor):
  ...
```
Then initialize the processor in the processorinfo class
```python
from vllm.multimodal.processing import BaseProcessingInfo
class BaseInternVLProcessingInfo(BaseProcessingInfo):
    @abstractmethod
    def get_hf_processor(...):
        raise NotImplementedError
class InternVLProcessingInfo(BaseInternVLProcessingInfo):
    def get_hf_processor(...):
        ...
        return self.ctx.init_processor(
            InternVLProcessor,
            config=self.get_hf_config(),
            tokenizer=self.get_tokenizer(),
            **kwargs,
        )
```

## 3 The Context
Defined in the `vllm/multimodal/processing.py`. Also notice that `ProcessingInfo` is passed into the `MultiModalProcessor`
```python
from vllm.inputs import InputProcessingContext
class BaseProcessingInfo:
    def __init__(self, ctx: InputProcessingContext) -> None:
        super().__init__()
        self.ctx = ctx
class BaseMultiModalProcessor(ABC, Generic[_I]):
    def __init__(self,...) -> None:
        super().__init__()
        self.info = info
        self.dummy_inputs = dummy_inputs
        self.cache = cache
        self.data_parser = self._get_data_parser()
    def _call_hf_processor():
        return self.info.ctx.call_hf_processor(
            self.info.get_hf_processor(**mm_kwargs),
            ...args...)
    ...
```
and the context is defined in `vllm/inputs/registry.py`:
```python
_T = TypeVar("_T") #Can be anything
_P = TypeVar("_P", bound=ProcessorMixin, default=ProcessorMixin) # can be any subtype of ProcessorMixin
@dataclass(frozen=True)
class InputContext:
    def get_hf_processor(...) -> _P:
        ...
        return cached_processor_from_config()
    def init_processor(self, typ, ...) -> _T:
        ...
        return typ(**merged_kwargs)
@dataclass(frozen=True)
class InputProcessingContext(InputContext):
    def call_hf_processor(hf_processor: ProcessorMixin,
        data: Mapping[str, object],
        kwargs: Mapping[str, object] = {},):
        ...
        output = hf_processor(**data, **merged_kwargs, return_tensors="pt")
        ...
```

