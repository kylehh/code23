---
title: Torchtune
mathjax: true
toc: true
categories:
  - Code
tags:
  - Torch
---

A customer request, show a OSS solution for LoRA finetune. Open sourced NeMo is the backup plan and Pytorch PEFT is preferred

I looked up this [example](https://pytorch.org/torchtune/stable/tutorials/llama3.html) from Pytorch's website and it worked it out. There are quite some pitfalls alone the way

## 1 Torchtune
**torchtune** is the Torch offical tuning lib, and it provides OOTB support for Llama3 and Llama3.1 LoRA FT. Couple of things to pay attention:
1. The default checkpoint file to work with is `consolidated.00.pth` file under `original` folder
2. The tokenizer has to be `tokenizer.model`, which also under `original` folder
3. You can define `batch_size` and `device` for GPU
```shell
tune run lora_finetune_single_device --config llama3/8B_lora_single_device \
    checkpointer.checkpoint_dir=/Meta-Llama-3-8B-Instruct/original \
    tokenizer.path=/Meta-Llama-3-8B-Instruct/original/tokenizer.model \
    checkpointer.output_dir=/_loras \
    batch_size=20 \
    device=1
```
After FT, it outputs following files. Still didn't figor out how to use it with NIM
```shell
         597 Aug 21 16:43 config.json
 16060616659 Aug 21 20:32 meta_model_0.pt
     6857850 Aug 21 20:32 adapter_0.pt
```
## 2 HF weight format
To directly work with HF weight format, safetensor files. There are prebuilt class to take it
```shell
tune cp llama3/8B_lora_single_device ./my_config.yaml
#default checkpoint class
#  _component_: torchtune.utils.FullModelMetaCheckpointer
tune run ... 
checkpointer._component_="torchtune.utils.FullModelHFCheckpointer" \
```

## 3 Tokenizer JSON intake
The most challenage part is to take in `tokenzier.json` instead of the model file. The solution ended up to be overriding `PreTrainedTokenizerFast` class, and add missing attributes from `llama3_tokenizer` class

```shell
#tokenizer:
#  _component_: torchtune.models.llama3.llama3_tokenizer
tokenizer._component_="kh_lib.kh_tokenizer.kh_PreTrainedTokenizerFast"
```
Thanks to my VREY first blog of python packages, I can quickly set up a local library `kh_lib` and get it installed locally to test.
Couple of tricks in the code
1. The default argument is `path`, which doesn't work. So replace it with `tokenizer_file`
2. Missing attributes, added `bos_id`, `eos_id`, `pad_id` etc
3. The `encode` doesn't not support `add_bos=False`, so force to return from [1:]
4. Add  LLAMA3_SPECIAL_TOKENS

The output files are
```shell
 4976716084 Aug 22 10:15 hf_model_0001_0.pt
 4999824766 Aug 22 10:15 hf_model_0002_0.pt
 4915937322 Aug 22 10:15 hf_model_0003_0.pt
 1168140692 Aug 22 10:15 hf_model_0004_0.pt
    6857850 Aug 22 10:15 adapter_0.pt
    6861962 Aug 22 10:15 adapter_model.bin
         87 Aug 22 10:15 adapter_config.json
```
With this changes, the output of `tune` is actually 
`adapter_config.yaml` and `adater_model.bin` which is exactly what NIM needs
Execept that I have to map model to CPU before it really works
```python
tensors = torch.load('/adater_model.bin',map_location=torch.device('cpu'))
torch.save(tensors, '/_loras/adapter_model.bin')
```

PS The full code for `kh_PreTrainedTokenizerFast`

```python
from transformers import PreTrainedTokenizerFast
from typing import Dict, List, Optional, Tuple
from torchtune.data import Message, truncate
LLAMA3_SPECIAL_TOKENS = {
    "<|begin_of_text|>": 128000,
    "<|end_of_text|>": 128001,
    "<|start_header_id|>": 128006,
    "<|end_header_id|>": 128007,
    "<|eot_id|>": 128009,
    "<|eom_id|>": 128008,
    "<|python_tag|>": 128255,
}

class kh_PreTrainedTokenizerFast(PreTrainedTokenizerFast):
    def __init__(self, *args, **kwargs):
        special_tokens = kwargs.pop("special_tokens", None)
        self.special_tokens = (
            special_tokens if special_tokens is not None else LLAMA3_SPECIAL_TOKENS
        )

        self._validate_special_tokens()

        # Encode BOS and EOS, define pad ID
        self.bos_id = self.special_tokens["<|begin_of_text|>"]
        self.eos_id = self.special_tokens["<|end_of_text|>"]
        self.pad_id = 10

        # Encode extra special tokens
        self.start_header_id = self.special_tokens["<|start_header_id|>"]
        self.end_header_id = self.special_tokens["<|end_header_id|>"]
        self.eot_id = self.special_tokens["<|eot_id|>"]

        self.eom_id = self.special_tokens["<|eom_id|>"]
        self.python_tag = self.special_tokens["<|python_tag|>"]

        # During generation, stop when either eos_id or eot_id is encountered
        self.stop_tokens = [self.eos_id, self.eot_id]

        if "tokenizer_file" in kwargs.keys():
            super().__init__(*args, **kwargs)
        else:
            tokenizer_file = kwargs.pop("path", None)
            super().__init__(tokenizer_file=tokenizer_file, *args, **kwargs)

    def _validate_special_tokens(
        self,
    ):
        """
        Validate that required special tokens are passed into the tokenizer. The
        following special tokens are required: <|begin_of_text|>, <|end_of_text|>,
        <|start_header_id|>, <|end_header_id|>, <|eot_id|>, <|eom_id|>, <|python_tag|>
        """
        for token in LLAMA3_SPECIAL_TOKENS.keys():
            if token not in self.special_tokens:
                raise ValueError(f"{token} missing from special_tokens")

    def encode(
        self,
        text: str,
        add_bos: bool = True,
        add_eos: bool = True,
    ) -> List[int]:
        if add_bos:
            return super().encode(text)
        else:
            return super().encode(text)[1:]
              
    def tokenize_message(
        self, message: Message, tokenize_header: bool = False
    ) -> List[int]:
        """
        Tokenize a message into a list of token ids.
            Args:
            message (Message): The message to tokenize.
            tokenize_header (bool): Whether to prepend a tokenized header to each message.

        Returns:
            List[int]: The list of token ids.
        """
        if tokenize_header:
            tokenized_header = (
                [self.start_header_id]
                + self.encode(message.role.strip(), add_bos=False, add_eos=False)
                + [self.end_header_id]
                + self.encode("\n\n", add_bos=False, add_eos=False)
            )
        else:
            tokenized_header = []
        tokenized_body = self.encode(
            message.content.strip(), add_bos=False, add_eos=False
        )
        if message.ipython:
            tokenized_body = [self.python_tag] + tokenized_body
        tokenized_message = tokenized_header + tokenized_body
        if message.eot:
            tokenized_message = tokenized_message + [self.eot_id]
        else:
            tokenized_message = tokenized_message + [self.eom_id]
        return tokenized_message

    def tokenize_messages(
        self,
        messages: List[Message],
        max_seq_len: Optional[int] = None,
        tokenize_header: bool = True,
        add_eos: bool = True,
    ) -> Tuple[List[int], List[bool]]:
        """
        Tokenize a list of messages into a list of token ids and masks.

        Args:
            messages (List[Message]): The list of messages to tokenize.
            max_seq_len (Optional[int]): The maximum sequence length.
            tokenize_header (bool): Whether to prepend a tokenized header to each message.

        Returns:
            Tuple[List[int], List[bool]]: The list of token ids and the list of masks.
        """
        tokens = [self.bos_id]
        # bos and eos are always masked
        mask = [True]
        for message in messages:
            tokenized_message = self.tokenize_message(
                message, tokenize_header=tokenize_header
            )
            tokens = tokens + tokenized_message
            mask = mask + ([message.masked] * len(tokenized_message))
            if max_seq_len and len(tokens) >= max_seq_len:
                break
        if add_eos:
            tokens = tokens + [self.eos_id]
            mask = mask + [True]
        if max_seq_len:
            tokens = truncate(tokens, max_seq_len, self.eos_id)
            mask = truncate(mask, max_seq_len, True)
        return tokens, mask
```

