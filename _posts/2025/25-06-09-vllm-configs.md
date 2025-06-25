---
title: Configs in vLLM
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Some details on Config loading in vLLM

1. Configs
`hf_config`: The config.json from HF  
`hf_text_config`: `llm_config` from config.json

```python
# api_server.py -> run_server -> init_app -> serve_http
def init_app()
  ...
  engine = AsyncLLMEngine.from_engine_args()
class AsyncEngine:
  def from_engine_args(...)
    ...
    vllm_config = engine_args.create_engine_config(usage_context)
class EngineArgs:
  def create_engine_config(self):
      ...
      model_config = self.create_model_config()
      return VllmConfig(
        model_config=model_config,
        ...
        )
  def create_model_config(self):
    ...
    return ModelConfig(...)
class ModelConfig:
  def __post_init__(self):
    ...
    hf_config = get_config(...) # read from the config registered at vllm/transformers_utils/configs/
    # Read `auto_map` configs
    self.hf_text_config = get_hf_text_config(self.hf_config)
  def get_hf_text_config(config: PretrainedConfig):
      ...
      ## transformers lib
      text_config = config.get_text_config()
      # looking for decoder_possible_text_config_names = ("decoder", "generator", "text_config")
      # if found, return 
      # otherwise return config so that text_config == config is True
```
![Alt text](/code23/assets/images/2025/25-06-09-vllm-configs_files/configs.png)

## 2 Preprocessor_config
`preprocessor_config` is read by `AutoImageProcessor` and it's also implemented in the same file in vLLM `vllm.transformers_utils
.processor.get_image_processors`
```
processor = AutoImageProcessor.from_pretrained(
    processor_name,
    *args,
    revision=revision,
    trust_remote_code=trust_remote_code,
    **kwargs)
```

## 3 Decorators
MultiModel Registration decorator
```
@MULTIMODAL_REGISTRY.register_processor(
    InternVLMultiModalProcessor,
    info=InternVLProcessingInfo,
    dummy_inputs=InternVLDummyInputsBuilder)
```
0. Firstly `self.processing_info.get_allowed_mm_limits()` is called from `multimodal.profiler.MultiModalProfiler` by `profiler.get_mm_limits()`
1. `class InternVLProcessingInfo`
    - `class BaseInternVLProcessingInfo`
      - `class BaseProcessingInfo` from `vllm.multimodal.processing`
2. `class InternVLMultiModalProcessor`
    - `class BaseInternVLMultiModalProcessor[InternVLProcessingInfo]`
      - `class BaseMultiModalProcessor[_I]` from `vllm.multimodal.processing`
3. `class InternVLDummyInputsBuilder`
    - `class BaseInternVLDummyInputsBuilder[InternVLProcessingInfo]`
      - `class BaseDummyInputsBuilder[_I])` from `vllm.multimodal.profiling`
4. `_I = TypeVar("_I", bound=BaseInternVLProcessingInfo)`
5. `class InternVLProcessor`
    - `class BaseInternVLProcessor(ABC)`
    - This is used at `InternVLProcessingInfo.get_hf_processor`

![Alt text](/code23/assets/images/2025/25-04-21-4bitquant_files/bf16.png)

![Alt text](/code23/code23/assets/images/2025/25-04-21-4bitquant_files/bf16.png)

