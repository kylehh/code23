---
title: Preprocessing config in vLLM
mathjax: true
toc: true
categories:
  - OSS
tags:
  - VLM
---

Here are preprocessing related code in vLLM

## 1 Read in `preprocessing_config.json`
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

## 2 The logic of `get_hf_processor`
```python
from vllm.multimodal.processing import BaseProcessingInfo
class BaseInternVLProcessingInfo(BaseProcessingInfo):
    @abstractmethod
    def get_hf_processor(...):
class InternVLProcessingInfo(BaseInternVLProcessingInfo):
    def get_hf_processor(...):
        return self.ctx.init_processor(
            InternVLProcessor,
            config=self.get_hf_config(),
            tokenizer=self.get_tokenizer(),
            **kwargs,
        )
```

## 3 The Context
In the `vllm/multimodal/processing.py`
```python
from vllm.inputs import InputProcessingContext
class BaseProcessingInfo:
    def __init__(self, ctx: InputProcessingContext) -> None:
        super().__init__()
        self.ctx = ctx
class BaseMultiModalProcessor(ABC, Generic[_I]):
    ...
```
and in `vllm/inputs/registry.py`, we have
```python
_T = TypeVar("_T") #Can be anything
_P = TypeVar("_P", bound=ProcessorMixin, default=ProcessorMixin) # can be any subtype of ProcessorMixin
@dataclass(frozen=True)
class InputContext:
    def get_hf_processor(...) -> _P:
    def init_processor(...) -> _T:
@dataclass(frozen=True)
class InputProcessingContext(InputContext):

```

