---
title: Prompts 
mathjax: true
toc: true
categories:
  - Study 
tags:
  - LLM
---
# Prompt methods
Notes for [this](https://www.promptingguide.ai/techniques/) prompt engineer website
## 1 Automatic Reasoning and Tool-use (ART) 
Key idea: Add code executing results in the chaining prompt
- given a new task, it select demonstrations of multi-step reasoning and tool use from a task library
- at test time, it **pauses** generation whenever external tools are called, and integrate their output before resuming generation
## 2 Automatic Prompt Engineer(APE)
- First, we use an LLM as an inference model to generate instruction candidates based on a small set of demonstrations in the form of input-output pairs.
- Next, we guide the search process by computing a score for each instruction under the LLM we seek to control. 
- Finally, we propose an iterative Monte Carlo search method where LLMs improve the best candidates by proposing semantically similar instruction variants.   

Simply put, APE asks LLMs to generate a set of instruction candidates
based on demonstrations and then asks them to assess which instructions are more promising.  

APE discovers a better zero-shot CoT prompt than the human engineered "Let's think step by step" prompt (Kojima et al., 2022), which is   
```
"Let's work this out in a step by step way to be sure we have the right answer."
```
## 3 Tree of Throught Prompt (ToT)
Use prompt instead of tree searching and backtracking
```
Imagine three different experts are answering this question.
All experts will write down 1 step of their thinking,
then share it with the group.
Then all experts will go on to the next step, etc.
If any expert realises they're wrong at any point then they leave.
The question is...
```
## 4 Active-Prompt
- query the LLM with or without a few CoT examples. k possible answers are generated for a set of training questions. 
- An uncertainty metric is calculated based on the k answers (disagreement used). #different answers/#total answers (so all same answers will get 1/k, all different answers will get 1)
- The most uncertain questions are selected for annotation by humans.
- The new annotated exemplars are then used to infer each question.

## 5 PAL (Program-Aided Language Models) vs CoT
CoT: Use **free-form text** to guide LLM think step by step
PAL: Generate **programs** as the intermediate reasoning steps
     and offloads the solution step to a **programmatic runtime** such as a Python interpreter. 

## 6 Prompt Function
Meta prompt as following
```
Hello, ChatGPT! I hope you are doing well. I am reaching out to you for assistance with a specific function. I understand that you have the capability to process information and perform various tasks based on the instructions provided. In order to help you understand my request more easily, I will be using a template to describe the function, input, and instructions on what to do with the input. Please find the details below:
function_name: [Function Name]
input: [Input]
rule: [Instructions on how to process the input]
I kindly request you to provide the output for this function, based on the details I have provided. Your assistance is greatly appreciated. Thank you!
I will replace the text inside the brackets with the relevant information for the function I want you to perform. This detailed introduction should help you understand my request more efficiently and provide the desired output. The format is function_name(input) If you understand, just answer one word with ok.
```
and specific prompting function could be 
```
function_name: [trans_word]
input: ["text"]
rule: [I want you to act as an English translator, spelling corrector and improver. I will provide you with input forms including "text" in any language and you will detect the language, translate it and answer in the corrected of my text, in English.]
```

# Prompt Samples
Get from Anthropic [cookbook repo](https://github.com/anthropics/anthropic-cookbook)
## 1 Build evaluation
```python
def build_grader_prompt(answer, rubric):
    user_content = f"""You will be provided an answer that an assistant gave to a question, and a rubric that instructs you on what makes the answer correct or incorrect.
    
    Here is the answer that the assistant gave to the question.
    <answer>{answer}</answer>
    
    Here is the rubric on what makes the answer correct or incorrect.
    <rubric>{rubric}</rubric>
    
    An answer is correct if it entirely meets the rubric criteria, and is otherwise incorrect. =
    First, think through whether the answer is correct or incorrect based on the rubric inside <thinking></thinking> tags. Then, output either 'correct' if the answer is correct or 'incorrect' if the answer is incorrect inside <correctness></correctness> tags."""

    messages = [{'role': 'user', 'content': user_content}]
    return messages
```

## 2 Content Moderation by CoT
```python
cot_prompt = '''You are a content moderation expert tasked with categorizing user-generated text based on the following guidelines:

BLOCK CATEGORY:
- Content that is not related to rollercoasters, theme parks, or the amusement industry
- Explicit violence, hate speech, or illegal activities
- Spam, advertisements, or self-promotion

ALLOW CATEGORY:
- Discussions about rollercoaster designs, ride experiences, and park reviews
- Sharing news, rumors, or updates about new rollercoaster projects
- Respectful debates about the best rollercoasters, parks, or ride manufacturers
- Some mild profanity or crude language, as long as it is not directed at individuals

First, inside of <thinking> tags, identify any potentially concerning aspects of the post based on the guidelines below and consider whether those aspects are serious enough to block the post or not. Finally, classify this text as either ALLOW or BLOCK inside <output> tags. Return nothing else.

Given those instructions, here is the post to categorize:

<user_post>{user_post}</user_post>'''
```

## 3. JSON output
```python
client.messages.create(
    model=MODEL_NAME,
    max_tokens=1024,
    messages=[
        {
            "role": "user", 
            "content": "Give me a JSON dict with names of famous athletes & their sports."
        },
        {
            "role": "assistant",
            "content": "Here is the JSON requested:\n{"
        }
    ]
)
```

## 4. SQL Query
```python
prompt = f"""Here is the schema for a database:

{schema}

Given this schema, can you output a SQL query to answer the following question? Only output the SQL query and nothing else.

Question: {query}
"""
```

## 5. Stable Diffusion
```python
image_gen_system_prompt = ("You are Claude, a helpful, honest, harmless AI assistant. "
"One special thing about this conversation is that you have access to an image generation API, "
"so you may create images for the user if they request you do so, or if you have an idea "
"for an image that seems especially pertinent or profound. However, it's also totally fine "
"to just respond to the human normally if that's what seems right! If you do want to generate an image, "
"write '<function_call>create_image(PROMPT)</function_call>', replacing PROMPT with a description of the image you want to create.")

image_gen_system_prompt += """

Here is some guidance for getting the best possible images:

<image_prompting_advice>
Rule 1. Make Your Stable Diffusion Prompts Clear, and Concise
Successful AI art generation in Stable Diffusion relies heavily on clear and precise prompts. It's essential to craft problem statements that are both straightforward and focused.

Clearly written prompts acts like a guide, pointing the AI towards the intended outcome. Specifically, crafting prompts involves choosing words that eliminate ambiguity and concentrate the AI's attention on producing relevant and striking images.
Conciseness in prompt writing is about being brief yet rich in content. This approach not only fits within the technical limits of AI systems but ensures each part of the prompt contributes meaningfully to the final image. Effective prompt creation involves boiling down complex ideas into their essence.
Prompt Example:
"Minimalist landscape, vast desert under a twilight sky."
This prompt exemplifies how a few well-chosen words can paint a vivid picture. The terms 'minimalist' and 'twilight sky' work together to set a specific mood and scene, demonstrating effective prompts creation with brevity.

Another Example:
"Futuristic cityscape, neon lights, and towering skyscrapers."
Here, the use of descriptive but concise language creates a detailed setting without overwhelming the AI. This example showcases the importance of balancing detail with succinctness in prompt structuring methods.

Rule 2. Use Detailed Subjects and Scenes to Make Your Stable Diffusion Prompts More Specific
Moving into detailed subject and scene description, the focus is on precision. Here, the use of text weights in prompts becomes important, allowing for emphasis on certain elements within the scene.

Detailing in a prompt should always serve a clear purpose, such as setting a mood, highlighting an aspect, or defining the setting. The difference between a vague and a detailed prompt can be stark, often leading to a much more impactful AI-generated image. Learning how to add layers of details without overwhelming the AI is crucial.
Scene setting is more than just describing physical attributes; it encompasses emotions and atmosphere as well. The aim is to provide prompts that are rich in context and imagery, resulting in more expressive AI art.
Prompt Example:
"Quiet seaside at dawn, gentle waves, seagulls in the distance."
In this prompt, each element adds a layer of detail, painting a serene picture. The words 'quiet', 'dawn', and 'gentle waves' work cohesively to create an immersive scene, showcasing the power of specific prompts crafting.

Another Example:
"Ancient forest, moss-covered trees, dappled sunlight filtering through leaves."
This prompt is rich in imagery and detail, guiding the AI to generate an image with depth and character. It illustrates how detailed prompts can lead to more nuanced and aesthetically pleasing results.

Rule 3. Contextualizing Your Prompts: Providing Rich Detail Without Confusion
In the intricate world of stable diffusion, the ability to contextualize prompts effectively sets apart the ordinary from the extraordinary. This part of the stable diffusion guide delves into the nuanced approach of incorporating rich details into prompts without leading to confusion, a pivotal aspect of the prompt engineering process.

Contextualizing prompts is akin to painting a picture with words. Each detail added layers depth and texture, making AI-generated images more lifelike and resonant. The art of specific prompts crafting lies in weaving details that are vivid yet coherent.
For example, when describing a scene, instead of merely stating: 
"a forest."
one might say,

"a sunlit forest with towering pines and a carpet of fallen autumn leaves."
Other Prompt Examples:
"Starry night, silhouette of mountains against a galaxy-filled sky."
This prompt offers a clear image while allowing room for the AI’s interpretation, a key aspect of prompt optimization. The mention of 'starry night' and 'galaxy-filled sky' gives just enough context without dictating every aspect of the scene.

Rule 4. Do Not Overload Your Prompt Details
While detail is desirable, overloading prompts with excessive information can lead to ambiguous results. This section of the definitive prompt guide focuses on how to strike the perfect balance.

Descriptive Yet Compact: The challenge lies in being descriptive enough to guide the AI accurately, yet compact enough to avoid overwhelming it. For instance, a prompt like, 'A serene lake, reflecting the fiery hues of sunset, bordered by shadowy hills' paints a vivid picture without unnecessary verbosity.
Precision in language is key in this segment of the stable diffusion styles. It's about choosing the right words that convey the most with the least, a skill that is essential in prompt optimization.
For example, instead of using:
"a light wind that can barely be felt but heard"
You can make it shorter:

whispering breeze
More Prompt Examples:
Sample prompt: "Bustling marketplace at sunset, vibrant stalls, lively crowds."

By using descriptive yet straightforward language, this prompt sets a vivid scene of a marketplace without overcomplicating it. It's an example of how well-structured prompts can lead to dynamic and engaging AI art.
</image_prompting_advice>

If you decide to make a function call:
- the call syntax will not be displayed to the user, but the image you create will be.
- please place the call after your text response (if any)."""
```

## 6 Image operations
Transcribe and understanding. 
```python
message_list = [
    {
        "role": 'user',
        "content": [
            {"type": "image", "source": {"type": "base64", "media_type": "image/png", "data": get_base64_encoded_image("../images/transcribe/school_notes.png")}},
            {"type": "text", "text": "Transcribe this text. Only output the text and nothing else."}
        ]
    }
]
```

For slide interpretation
```python
def build_previous_slides_prompt(previous_slide_narratives):
    prompt = '\n'.join([f"<slide_narration id={index+1}>\n{narrative}\n</slide_narration>" for index, narrative in enumerate(previous_slide_narratives)])
    return prompt

def build_slides_narration_prompt(previous_slide_narratives):
    if len(previous_slide_narratives) == 0:
        prompt = """You are the Twilio CFO, narrating your Q4 2023 earnings presentation.

You are currently on slide 1, shown in the image.
Please narrate this page from Twilio's Q4 2023 Earnings Presentation as if you were the presenter. Do not talk about any things, especially acronyms, if you are not exactly sure you know what they mean. Do not discuss anything not explicitly seen on this slide as there are more slides to narrate later that will likely cover that material.
Do not leave any details un-narrated as some of your viewers are vision-impaired, so if you don't narrate every number they won't know the number.

Put your narration in <narration> tags."""

    else:
        prompt = f"""You are the Twilio CFO, narrating your Q4 2023 earnings presentation. So far, here is your narration from previous slides:
<previous_slide_narrations>
{build_previous_slides_prompt(previous_slide_narratives)}
</previous_slide_narrations>

You are currently on slide {len(previous_slide_narratives)+1}, shown in the image.
Please narrate this page from Twilio's Q4 2023 Earnings Presentation as if you were the presenter, accounting for what you have already said on previous slides. Do not talk about any things, especially acronyms, if you are not exactly sure you know what they mean. Do not discuss anything not explicitly seen on this slide as there are more slides to narrate later that will likely cover that material.
Do not leave any details un-narrated as some of your viewers are vision-impaired, so if you don't narrate every number they won't know the number.

Use excruciating detail.

Put your narration in <narration> tags."""
    
    return prompt
```
Multiple images reading
```python
message_list = [
    {
        "role": 'user',
        "content": [
            {"type": "image", "source": {"type": "base64", "media_type": "image/png", "data": get_base64_encoded_image("../images/best_practices/receipt1.png")}},
            {"type": "image", "source": {"type": "base64", "media_type": "image/png", "data": get_base64_encoded_image("../images/best_practices/receipt2.png")}},
            {"type": "text", "text": "Output the name of the restaurant and the total."}
        ]
    }
]
```