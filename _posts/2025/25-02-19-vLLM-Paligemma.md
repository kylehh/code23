---
title: vLLM update - Paligemma
mathjax: true
toc: true
categories:
  - OSS
tags:
  - VLM
---

Notes of updating MM Processor for Paligemma model
Initial [PR](https://github.com/vllm-project/vllm/pull/13584) w `PromptReplacement` class.
It worked except for language feature is not working. After debugging, found out that HF processor always add \<bos\> at the very beginning for Paligemma 1, which should be removed. So worked on a new [PR](https://github.com/vllm-project/vllm/pull/14015) using `PromptUpdate` class.  

## 0 vLLM local Install and test
The Python-only build installation is
```sh
VLLM_USE_PRECOMPILED=1 pip install --editable .
```  
and all tests can be run by     
```
pip install -r requirements-dev.txt

# Linting, formatting and static type checking
pre-commit install --hook-type pre-commit --hook-type commit-msg

# You can manually run pre-commit with
pre-commit run --all-files

# Unit tests
pytest tests/
```
Or the specific VLM test can be found [here](https://docs.vllm.ai/en/latest/getting_started/examples/vision_language.html)

## 1 Multi-Modal Processing
The design doc for MM is [here](https://docs.vllm.ai/en/latest/design/mm_processing.html) and the examples can be found [here](https://docs.vllm.ai/en/latest/contributing/model/multimodal.html)

The pytest is under `vllm/tests/models/multimodal/processing/test_common.py` path. 

## 2 HuggingFace Processor
The logic is that it will call HF processor for the first time through this stack
![Alt text](/code23/assets/images/2025/25-02-19-vLLM-Paligemma_files/hf.png)

```
    def _cached_apply_hf_processor(
        self,
        prompt: Union[str, list[int]],
        mm_data_items: MultiModalDataItems,
        hf_processor_mm_kwargs: Mapping[str, object],
    ) -> tuple[list[int], MultiModalKwargs, bool]:
        """
        Apply the HF processor on the full prompt text,
        caching the results and reusing cached results.
        """
        cache = self.cache
        model_id = self.info.model_id

        _, passthrough_data = self._get_hf_mm_data(mm_data_items)
        if cache is None or passthrough_data:
            return self._apply_hf_processor_main(
                prompt=prompt,
                mm_items=mm_data_items,
                hf_processor_mm_kwargs=hf_processor_mm_kwargs,
                enable_hf_prompt_update=True,
            )
        ...
        return prompt_ids, mm_kwargs, is_update_applied 
```
`enabel_hf_prompt_update` is set to True, and `is_update_applied` will be True

## 3 Cached Processor
For the second time, a cached processor will be called
![Alt text](/code23/assets/images/2025/25-02-19-vLLM-Paligemma_files/cache.png)
```
    def _cached_apply_hf_processor(...)
      ...
        (
            prompt_ids,
            mm_missing_kwargs,
            is_update_applied,
        ) = self._apply_hf_processor_main(
            prompt=prompt,
            mm_items=mm_missing_data_items,
            hf_processor_mm_kwargs=hf_processor_mm_kwargs,
            enable_hf_prompt_update=False,
        )
        return prompt_ids, mm_kwargs, is_update_applied 
```
Notice `enable_hf_prompt_update` and `is_update_applied` both will be False

## 4 Apply Prompt Update
Based on `is_update_applied`, the code will decide if apply `_apply_prompt_updates`.
Always override `_get_prompt_updates`.  
For Paligemma, I have to override `apply` as well to add "\n" at the end of prompt.  
```
class BaseMultiModeProcessor(ABC, Generic[_I]):
    def apply(...)
        (
            prompt_ids,
            mm_kwargs,
            is_update_applied,
        ) = self._cached_apply_hf_processor(
            prompt,
            mm_items,
            hf_processor_mm_kwargs,
        )

        unbound_prompt_updates = self._get_prompt_updates(
            mm_items,
            hf_processor_mm_kwargs,
            mm_kwargs,
        )

        ...
        
        if is_update_applied:
            mm_placeholders = self._find_mm_placeholders(
                mm_prompt_updates,
                prompt_ids,
                mm_item_counts,
            )
            self._validate_mm_placeholders(mm_placeholders, mm_item_counts)

            tokenizer = self.info.get_tokenizer()
            prompt = decode_tokens(tokenizer, prompt_ids)
        else:
            (
                prompt_ids,
                prompt,
                mm_placeholders,
            ) = self._apply_prompt_updates(
                prompt_ids,
                mm_prompt_updates,
                mm_item_counts,
            )
            self._validate_mm_placeholders(mm_placeholders, mm_item_counts)

```
For both Peligemma 1 and 2, the final prompt format should be     
```
<image><image>...<image><image><bos><promtp_characters>\n
```
Peligemma 1 has `add_bos_token` as True in tokenizer_config.html, so we can call `PromptReplacement`  
For Peligemma 2, `add_bos_token` is Falce, so we call `PromptInsertion` to add \<image\> tokens

