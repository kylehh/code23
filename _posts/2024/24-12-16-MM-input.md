---
title: MultiModal Input in vLLM
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

I started my first vLLM contribution by adding the audio input API following OpenAI's [schema](https://platform.openai.com/docs/guides/audio?audio-generation-quickstart-example=audio-in).

vLLM's multimodal input for OpenAI's Chat-Completion API works for image, audio and video.
The support is not explicitly writing but following format work for all these inputs
```python
# It support directly image/audio/video URL, 
messages=[{
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "What's in this image?"
                },
                {
                    "type": "image_url",
                    "image_url": {
                        "url": image_url
                    },
                },
            ],
        }],
# Or use base64 encoded audio
        {
            "type": "audio_url",
            "audio_url": {
                # Any format supported by librosa is supported
                "url": f"data:audio/ogg;base64,{audio_base64}"
            },
        },

```
## 1 Supported API format
The new format is using `input_audio` type instead of `audio_url`
and it directly takes in base64 encoded audio.  
```python
        {
            "type": "input_audio",
            "input_audio": {
                # Any format supported by librosa is supported
                "data": audio_base64,
                "format": "wav"
            },
        },
```

## 2 Code Changes
Majority of the code changes are in `vllm/entrypoint/chat_utils.py`
1. Add class `ChatCompletionContentPartInputAudioParam`
  This class is derived from `TypedDict` and can be directly imported from [OpenAI](https://github.com/openai/openai-python/blob/main/src/openai/types/chat/chat_completion_content_part_input_audio_param.py) by `from openai.types.chat import ChatCompletionContentPartInputAudioParam`.
2. The code logic is as following  

```python
# Define partial functions, so when you call _AudioParse(part)
# It will call: cast(ChatCompletionContentPartAudioParam, part)
_AudioParser = partial(cast, ChatCompletionContentPartAudioParam)
_InputAudioParser = partial(cast, ChatCompletionContentPartInputAudioParam)

# Define a mapping from part types to their corresponding parsing functions.
MM_PARSER_MAP: Dict[str,
                    Callable[[ChatCompletionContentPartParam],
                             Union[str, Dict[str,str]]]] = {
    "audio_url":
    lambda part: _AudioParser(part).get("audio_url", {}).get("url", ""),
    "input_audio":
    lambda part: _InputAudioParser(part).get("input_audio", {}),
    ...
}

#From parse_chat_messages()
#A loop over _parse_chat_message_content()
#calls _parse_chat_message_content_parts()
#A loop over _parse_chat_message_content_part()
#calls _parse_chat_message_content_mm_part()

content = MM_PARSER_MAP[part_type](part)

if part.get("audio_url") is not None:
    audio_params = cast(CustomChatCompletionContentSimpleAudioParam,
                        part)
    return "audio_url", audio_params.get("audio_url", "")
if part.get("input_audio") is not None:
    input_audio_params = cast(Dict[str, str], part)
    return "input_audio", input_audio_params

```

## 3 Audio Parsing
The multimodal parsing are all defined in `MultiModalContentParser` class. The core functin is `get_and_parse_audio` defined [here](https://github.com/vllm-project/vllm/blob/e8e6b6137c094bba6be3471122308e108fb08fac/vllm/multimodal/utils.py#L260). The new API is leveraging this function so we create a new URL following vLLM convention.  

```python
def parse_audio(self, audio_url: str) -> None:
    audio = get_and_parse_audio(audio_url)

    placeholder = self._tracker.add("audio", audio)
    self._add_placeholder(placeholder)

def parse_input_audio(self, input_audio: Dict[str, str]) -> None:
    input_audio_data = input_audio.get("data","")
    input_audio_format = input_audio.get("format","")
    audio_url = f"data:audio/{input_audio_format};base64,{input_audio_data}"
    audio = get_and_parse_audio(audio_url)

    placeholder = self._tracker.add("audio", audio)
    self._add_placeholder(placeholder)
```

## 4 Future Improvmentment
By defining the `ChatCompletionContentPartInputAudioParam`, I was expected to use this class for type casting, instead of casting to `Dict[str, str]`. But the `mypy` check would fail because ambiguities in the class defination. 

Basically following code can NOT pass `mypy` type check. It complains expect `classUnion` but get `classA`. 
```python
classUnion = Union[classA, classB]
def funcTest() -> classUnion:
  return classA
```

## 5 Local Test
Install vllm locally by running
`VLLM_USE_PRECOMPILED=1 pip install --editable .` unless you need to change the C++/CUDA kernel
