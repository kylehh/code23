---
title: Diffusion Quantization
mathjax: true
toc: true
categories:
  - Study
tags:
  - LLM
---

I didn't start the first blog in 2025 till later Jan. I wasn't quite myself for the first couple of weeks of the new year. Still recovering from the UK trip and putting my mind together.

Let's see how we can quantize diffusion models with TensorRT. Not TensorRT-LLM but TRT directly, which is the my first time interact with this library.

## 1 Legacy approach
I was introduced to this [blog](https://developer.nvidia.com/blog/tensorrt-accelerates-stable-diffusion-nearly-2x-faster-with-8-bit-post-training-quantization/) which was published in March 2024, which is getting obsolete for sure in the LLM world. 

[SmoothQuant](https://arxiv.org/pdf/2211.10438.pdf) stands out as a popular PTQ(Post Training Quantization) method to enable 8-bit weight, 8-bit activation (W8A8) quantization for LLMs. Its primary innovation lies in its approach to addressing activation outliers by **transferring the quantization challenge from activations to weights** through a mathematically equivalent transformation. 

NVIDIA TensorRT developed a tuning pipeline to determine the optimal parameter settings for each layer of the model for SmoothQuant.  You can develop your own tuning pipeline depending on the specific characteristics of the feature maps. This capability enables TensorRT quantization to result in superior image quality that preserves rich details from original images,

The method in the blog is using [NV AMMO](https://pypi.org/project/nvidia-ammo/) lib and the code snippet is based on [diffusion demo](https://github.com/NVIDIA/TensorRT/tree/release/10.2/demo/Diffusion) but it's NOT intact in the blog

## 2 New Lib TensorRT-Model-Optimizer

[ModelOpt](https://github.com/NVIDIA/TensorRT-Model-Optimizer) is a standalone project under NV Github repo. and I verified the quantization works for the SDXL example

1. Build the Modelopt container  

  ```sh
  # Clone the ModelOpt repository
  git clone https://github.com/NVIDIA/TensorRT-Model-Optimizer.git
  cd TensorRT-Model-Optimizer

  # Build the docker (will be tagged `modelopt_examples:latest`)
  # You may customize `docker/Dockerfile` to include or exclude certain dependencies you may or may not need.
  ./docker/build.sh

  # Mount model folder, TensorRT code folder
  export IMG_NAME=docker.io/library/modelopt_examples:latest
  # Run the docker image
  docker run --gpus all -it  \
  --shm-size 20g --rm \
  -v /raid/models:/raid/models \
  -v /home/khuang/_git_repos/TensorRT:/TensorRT \
  -e HF_HOME=/raid/models/huggingface \
  $IMG_NAME bash
  ```  

2. 8-bit ONNX Export 
On A100 machines, go to this [folder](https://github.com/NVIDIA/TensorRT-Model-Optimizer/tree/main/diffusers/quantization) and build the INT8 engine for SDXL. It will output torch checkpoint and ONNX dir  

  ```sh
  # The example script 
  #bash build_sdxl_8bit_engine.sh --format int8

  ## Actually commands
  python quantize.py --model sdxl-1.0 \
  --format int8 --batch-size 2 --calib-size 32 --collect-method min-mean \
  --percentile 1.0 --alpha 0.8 --quant-level 3 --n-steps 20 \
  --quantized-torch-ckpt-save-path /raid/models/SDXL-1.0/sdxl-1.0_3_int8.pt \
  --onnx-dir /raid/models/SDXL-1.0/sdxl-1.0_3_int8.onnx
  ```

3. TRT Engine Built
Now we can built the TRT Engine with following script  

```sh
trtexec --builderOptimizationLevel=4 --stronglyTyped --onnx=/raid/models/SDXL-1.0/sdxl-1.0_3_int8.onnx/backbone.onnx \
  --minShapes=sample:2x4x128x128,timestep:1,encoder_hidden_states:2x77x2048,text_embeds:2x1280,time_ids:2x6 \
  --optShapes=sample:16x4x128x128,timestep:1,encoder_hidden_states:16x77x2048,text_embeds:16x1280,time_ids:16x6 \
  --maxShapes=sample:16x4x128x128,timestep:1,encoder_hidden_states:16x77x2048,text_embeds:16x1280,time_ids:16x6 \
  --saveEngine=/raid/models/SDXL-1.0/trtexec_backbone.plan
```

4. Test with Diffusion Pipeline  
Go to TensorRT folder under demo/Diffusion  

```sh
# /TensorRT/demo/Diffusion
python demo_txt2img_xl.py \
"enchanted winter forest, soft diffuse light on a snow-filled day, serene nature scene, the forest is illuminated by the snow" \
--negative-prompt "normal quality, low quality, worst quality, low res, blurry, nsfw, nude" \
--version xl-1.0 --scheduler Euler --denoising-steps 30 --seed 2946901
```
This is will download model to `pytorch_model` folder and generate contents under `onnx`,`engine` and `output` folders. 

5. Engine udpate
The TRT engine built was for UNet. Now replace the `engine/unetxl.trt10.6.0.plan` engine with the one built in step 3.  

```sh
export YOUR_UNETXL=/raid/models/SDXL-1.0/trtexec_backbone
cp -r {YOUR_UNETXL}.plan ./engine/unetxl.trt10.6.0.plan  
```
Now you can rerun the command in step 4 to generate images with INT8 UNet

6. Results Comparision

Output will be saved in `output` folder.  
FP16
![Alt text](/code23/assets/images/2025/25-01-23-Diffusion-Quantize_files/fp16.png)
INT8:
![Alt text](/code23/assets/images/2025/25-01-23-Diffusion-Quantize_files/int8.png)