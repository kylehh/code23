---
title: Tokenizers in LLM
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

Tokenizer is a basic concept in NLP, and basically it generates tokens from a sentence. A token is a bit "less" than a word, so the common ratio between tokens and words are 1.2, which means **#_tokens = 1.2 x #_works**

While working with Pinecone Canopy, it requries initialization of a tokenizer before calling an LLM. In order to work with Llama and AE (Anyscale Endpoint), I have to add a LLama tokenizer class. and here are couple of interests points I discovered.  
## 1. Findings
1. Tokenizer normally comes with the LLM. If you download Llama from HF, the Llama tokenzier will be downloaded automatically with it. The tricky thing is that HF requires a permission to download Llama models ( Anyone can apply for the permission, but I still haven't got it granded yet. ).   
  
So we can't directly link to HF for the Llama, otherwise users will be blocked  
2. Since AE hosts Llama model, and can we provide the tokenizer for users to download? It's a simple JSON file, and kind of public. But it requires legal clearance ,and you konw how much trouble it is to deal with the legal deparment.   
## 2. Get pre-trained tokenizers
1. HF should also realize this issue, so they provide a Llama tokenizer and named it as **internal** one. Huh, this is the one we eventually used.  
```
from transformers import LlamaTokenizerFast as HfTokenizer
self._encoder = HfTokenizer.from_pretrained(
            "hf-internal-testing/llama-tokenizer", 
            token=hf_token, legacy=True, add_bos_token=False
        )
```
So the `LlamaTokenizerFast` can take the pretrained tokenzer and staring encoding. `add_bos_token` should be set to `False`, otherwise there is always a BOS token added and would failed the unit test.  
## 3. Code examples
1. Here are couple of detailed concepts in tokenizer, like encode/decode:
  code here are actually digital numbers represent works. Use the `encode` methods would get the digtal tokens. This is the core of tokenizing.  
```
self._encoder.encode("Hello World!")
# return [12345,54321,123321]
```  

5. Now finally, tokenzier/detokenizer
This step is simply a map transfer, converting digital tokens to string tokens, it's actually **decode**. So if we combine **encode** and **decode**, you will get your tokenizer. Let's look at an example of `tiktoken`, which is the basic class for all HF tokenizers classes
```
import tiktoken
self._encoder = tiktoken.encoding_for_model(model_name)
return [self._encoder.decode([encoded_token])
                for encoded_token in self._encode(text)]
```
Or, you can directly call the `tokenzier` method for `LlamaTokenizerFast`.  
```
self._encoder.tokenize("Hello World!")
# return ['▁Hello', '▁World', '!']
```  
Detokenizer actually is a bit tricky. And you can see that it's not just concatentate the tokens together. Lucky there is a method to call directly. 
```
self._encoder.convert_tokens_to_string(['▁Hello', '▁World', '!'])
# return "Hello World!"
``` 
But for `tiktoken`, the concatenation works
```
def detokenize(self, tokens: List[str]) -> str:
    if not isinstance(tokens, List):
        raise TypeError(f"detokenize expect List[str], got f{type(tokens)}")
    return "".join(tokens)
```

I still don't understand why we need to specify a tokenizer before using LLM instead of call it implicity in the backend. 
