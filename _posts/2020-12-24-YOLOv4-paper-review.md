---
title: "[논문 리뷰] YOLOv4"
date: 2020-12-24 00:25:00 -0400
categories: yolo yolov4 object-detection spp-net panet Mish-Activation
---

**# YOLOv4**
- - -
YOLO는 You Only Look Once의 약자로 이미지를 한 번 보는 것만으로도 Object의 종류와 위치를 추측하는 딥러닝 기반의 물체 인식 알고리즘입니다.
이미지를 한 번만 본다는게 어떤 뜻일까요?

**- R-CNN**
![rcnn_network](https://user-images.githubusercontent.com/39725476/103012866-8b96a180-457f-11eb-8522-a2541d10df1f.png)
대표적인 Object Detection 방법인 R-CNN의 학습 방법은 다음과 같습니다.
1. 이미지에 대한 후보 영역(region proposal) 약 2000개를 생성합니다.
2. 이 후보 영역을 wrap/crop 방법을 통해 고정된 크기로 맞춘 후 CNN을 통과시킵니다.

**- YOLO**
![yolo_system](https://user-images.githubusercontent.com/39725476/103013339-52126600-4580-11eb-8d35-f99497b3b5f4.png)
하지만 YOLO는 region proposal과 같이 이미지를 나누지 않고 CNN에 단 한 번 통과시킵니다.
이를 두고 이미지를 한 번만 본다고 표현합니다. 또 한 번 보기 때문에 속도가 빠릅니다.

실제로 MS-COCO 데이터 기준 FPS를 보면 그 차이가 더 확실하게 보여집니다.

| FPS | Fast R-CNN | Faster R-CNN | YOLOv1 | YOLOv5 |
| :0.5: | :5: | :45: | :140: |

그럼 본론으로 들어가서 Object Detection 중 최고의 속도를 보이는 YOLOv4 논문 리뷰를 시작하겠습니다.
- - -
**1. Introduction**
Object Detection 분야의 연구는 R-CNN을 시작으로 많은 연구가 진행되고 있습니다. 그리고 대표적인 구조로 논문에서는 아래의 구조를 제시하고 있습니다.
![object-detector-architecture](https://user-images.githubusercontent.com/39725476/103016548-8ccacd00-4585-11eb-8f9d-809a1574c5e4.png)
구조는 크게 3가지로 구분됩니다. 
1. Backbone
2. Neck
3, Head(Dense / Sparse Prediction)

- Backbone은 input image를 feature map으로 변형하는 부분입니다.
- Neck은 Backbone과 Head를 연결하는 부분입니다. Feature map을 정제(Refinement), 재구성(Reconfiguration)한다고 생각하면 될 것 같습니다.
- Head는 Backbone에서 추출한 feature map의 location 작업이 이뤄지는 곳으로 Predict classes와 bounding boxes 작업이 이뤄집니다. Head는 크게 두가지로 구분되는데 Dense Prediction과 Sparse Prediction입니다. 
Dense Prediction에서는 Predict와 Bounding boxes가 함께 작업이 되고, Sparse Prediction에서는 Predict와 Bounding Boxes가 구분되어 작업됩니다. 이때 Dense Prediction이 Head일 경우 Architecture 전체를 One-Stage Detector, Sparse Prediction이 Head일 경우 Two-Stage Detector가 됩니다.

- - -
