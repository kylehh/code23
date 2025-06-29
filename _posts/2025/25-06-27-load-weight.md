---
title: Weight loading in vLLM
mathjax: true
toc: true
categories:
  - OSS
tags:
  - LLM
---

Fixed a weight loading bug
## 1 Weight loading workflow
```
## worker/gpu_worker.py
class Worker(Workerbase):
  def load_model(self) -> None:
    ...
    self.model_runner.load_model()
## worker/gpu_model_runner.py
class GPUModelRunner()
  def load_model(self) -> None:
    ...
    model_loader = get_model_loader(self.load_config)
    self.model = model_loader.load_model(
                    vllm_config=self.vllm_config,
                    model_config=self.model_config)
    ...
## model_loader/base_loader.py
class BaseModelLoader(ABC):
  def load_model(self, vllm_config: VllmConfig,
                  model_config: ModelConfig) -> nn.Module:
      ...
      self.load_weights(model, model_config)
      process_weights_after_loading(model, model_config, target_device)
      ...
## model_loader/default_loader.py
class DefaultModelLoader(BaseModelLoader):
  def load_weights(self, model: nn.Module,
                    model_config: ModelConfig) -> None:
      # List all the weights needs to be loaded
      weights_to_load = {name for name, _ in model.named_parameters()}
      loaded_weights = model.load_weights(
          self.get_all_weights(model_config, model))
      ...
## models/model_name.py
def load_weights(self, weights: Iterable[tuple[str,
                torch.Tensor]]) -> set[str]:
    loader = AutoWeightsLoader(self)
    return loader.load_weights(weights)
## models/utils.py
class AutoWeightsLoader:
  def load_weights(
          self,
          weights: Iterable[tuple[str, torch.Tensor]],
          *,
          mapper: Optional[WeightsMapper] = None,
      ) -> set[str]:
      ...
      autoloaded_weights = set(self._load_module("", self.module, weights))
      ...

  def _load_module(
        self,
        base_prefix: str,
        module: nn.Module,
        weights: Iterable[tuple[str, torch.Tensor]],
    ) -> Iterable[str]:
      # call recursively
      ...