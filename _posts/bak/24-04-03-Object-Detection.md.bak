---
title: Object Detection Summary
mathjax: true
toc: true
categories:
  - Study 
tags:
  - DL
---

I feel I was reading a lot of LLM related topics recently but getting far away from CV. I happened to read this [post](https://blog.csdn.net/v_july_v/article/details/80170182) from `v_JULY_v` and it's a good review for object detection technics, and prepare myself to review on Stable diffusion and vidoe generation.

Here is the overview for OD.
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/overview.png)

## 1. R-CNN
Two approaches for OD. First is regression based. Use regression to generate 4 coordinates (x, y, w, h). But the training is too hard
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/regressionod.png) 

To improve from here, we use windows to iterate the whole image. and we also use **convolution layer to replace FC** to speedup.
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/speedup.png)

**Region Proposal** by Selective Search or EdgeBoxes are proposed by R-CNN. Get ~2000 Regions of Interests (RoI) first, and warp them into same size image (227x227) and then sent to CNN for featrue extraction.
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/rcnn.png) 
Here are the training steps
- Finetune a AlexNet with last layter to number of classes.
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/rcnn1.png)
- For each RoI, run CNN and save feature map to disk
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/rcnn2.png)
- Run SVM for binary classification
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/rcnn3.png)
- Run regression for region adjustment
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/rcnn4.png)

## 2. SPP Net
Kaiming He published Spatial Pyramid Pooling (SPP) paper in 2015.

In R-CNN, region proposals needs to be warp into 227x227 b/c FC layer needs fixed input(so the conv layer before FC needs fixed input, that's the purpose of warping). But **CNN does NOT have this requirement**. So how about we add a special layer to feed fixed size to FC so we don't need to warp the image!. There is the difference between R-CNN and SPP
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/rcnnspp.png)

The key idea is if you make **the pooling window and stride proportional to the input image**, you can always get a **fixed-sized output**.
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/spp.png)

Another improvement is ONLY calculation conv ONCE for the whole image and extract corresponding patch for each RoI.

## 3. Fast R-CNN
Apply SPP into R-CNN.
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/fastrcnn0.png)
- Add RoI pooling layer, which is a simple version of SPP
- Add **Bounding Box Regression** into CNN training to get a multi-task model.
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/fastrcnn.png)
Also, run conv once for the whole picture instead of on each region
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/convonce.png)
## 4. Faster R-CNN
- Use Region Proposal Network (RPN) to replace the selective search
- Use anchor box
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/fasterrcnn.png) 
Notice there are 4 loss functions
  - RPN classification (anchor good/bad)
  - RPN regression (anchor -> proposal)
  - Fast R-CNN classification (over classes)
  - Fast R-CNN regression (proposal -> box)

Here are the summaries of these 4 methods before going to DL based regression approaches

|R-CNN|SPP|Fast R-CNN|Faster R-CNN|
-|-|-|-
|Selective Search||Selective Search|RPN
||RoI Pooling|RoI Pooling|RoI Pooling
|CNN(feature extraction)+SVM(classification)||CNN|CNN|

## 5. YOLO
- Divide image into SxS grid (S=7)
- Predict B bounding boxes (B=2) with 5 values, (x, y, w, h, confidence) and C classes 
- Use NMS(Non-Maximun Suppresion) to get rid of extra windowes
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/yolo.png)
With Region of Proposal, the accuracy suffers

## 6. SSD
Adding back anchor boxes
- Go through certain conv layer to get m x n feature map with p channel
- For each location, get k bounding boxes with different ratio (here are the anchors)
- For each box, compute c class and 4 offsets for (x, y ,w, h)
In total will get (c+4)mnk outputs
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/ssd.png)
Different layers of feature maps also going through 3x3 conv for OD.
So it has 8732 bounding boxes which is way more than 98 from YOLO. (details are [here](https://towardsdatascience.com/review-ssd-single-shot-detector-object-detection-851a94607d11))
![Alt text](/assets/images/2024/24-04-03-Object-Detection_files/moreboxes.png)