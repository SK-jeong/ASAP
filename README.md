# ASAP: AI State Analysis of Pets🐕
Recognition of Pet Actions Using Keypoint Detection and Skeleton-Based Action Recognition.<br>
(2021.07.05~2021.08.23)

## Overview
어쩌구저쩌구 반려동물에 대한 관심 올라감 어쩌구 동물의 행동 분류 blahblahblah

## Data
AI-Hub에서 제공하는 [반려동물 구분을 위한 동물 영상](https://aihub.or.kr/aidata/34146) 데이터 중 개의 행동영상 및 영상 데이터만을 사용하여 모델 학습을 진행하였습니다.<br>

Keypoint Detection 모델 학습시 영상 데이터(영상의 프레임 별 이미지)를, Action Recognition 모델 학습시 라벨 데이터를 사용하였으며 각 학습 데이터의 형식은 다음과 같습니다.
* **Keypoint Detection** <br>
  | Original | Keypoint | Label |
  | :------: | :------: | :---- |
  | <img width="250" alt="data-img" src="https://user-images.githubusercontent.com/63901494/129929470-e2def238-963b-42c8-98b6-59bb0ad3b4c6.jpg"> | <img width="250" alt="data-keypoint" src="https://user-images.githubusercontent.com/63901494/129929529-235d5f39-1ec5-4452-8299-490c84702ebf.png"> | 0: 코<br>1: 이마 정 중앙<br>2: 입꼬리(입끝)<br>3: 아래 입술 중앙<br>4: 목<br>5: 앞다리 오른쪽 시작<br>6: 앞다리 왼쪽 시작<br>7: 앞다리 오른쪽 발목<br>8: 앞다리 왼쪽 발목<br>9: 오른쪽 대퇴골<br>10: 왼쪽 대퇴골<br>11: 뒷다리 오른쪽 발목<br>12: 뒷다리 왼쪽 말목<br>13: 꼬리 시작<br>14: 꼬리 끝 |

  키포인트 감지 모델 학습을 위해서는 키포인트 좌표 데이터가 필요합니다. 다음과 같은 csv 파일 형식으로 이미지의 경로와 키포인트 정보를 구성해 주세요. <br>
  <img width="800" alt="data-csv" src="https://user-images.githubusercontent.com/63901494/129934622-ec6e8130-50de-4893-92d6-7d040220cac9.png">

* **Action Recognition** <br>
  행동 분류 모델의 학습을 위해서는 키포인트 좌표값과 각 포인트에 대한 confidence score, 영상의 라벨인 action class 정보가 필요합니다. 다음과 같은 형식의 json 파일을 각 영상에 대해 구성해 주세요.
  ```
  {
    "data":[
      {"frame_index":1, "skeleton":[
        {"pose":[x.xxx, x.xxx, x.xxx, ...],   // keypoint
         "score":[x.xxx, x.xxx, x.xxx, ...]   // confidence score
        }]
      },
      {"frame_index":2, "skeleton":[
        {"pose":[x.xxx, x.xxx, x.xxx, ...],
         "score":[x.xxx, x.xxx, x.xxx, ...]
        }]
      },
      ...,]
      "label": "걷거나 뜀",
      "label_index": 5
    ...
  }
  ```
  영상의 각 프레임별 스켈레톤 정보로부터 '앉기', '두 앞발을 들어 올림', '앞발 하나를 들어 올림', '몸을 턴다', '엎드리기', '걷거나 뜀', '꼬리를 위로 올리고 흔듦', '빙글빙글 돈다', '마운팅', '꼬리가 아래로 향함' 과 같은 행동 분류를 진행하였습니다. 

## Models
* [Keypoint Detection] **Keypoint RCNN** - [Mask-RCNN, 2017 (Kaiming He, Georgia Gkioxari, Piotr Dollár, Ross Girshick)](https://arxiv.org/pdf/1703.06870v3.pdf)
* [Keypoint Detection] **HR-Net** - [Deep High-Resolution Representation Learning for Visual Recognition, 2019 (Jingdong Wang, Ke Sun, Tianheng Cheng, Borui Jiang, Chaorui Deng, Yang Zhao, Dong Liu, Yadong Mu, Mingkui Tan, Xinggang Wang, Wenyu Liu, Bin Xiao)](https://arxiv.org/pdf/1908.07919.pdf)
* [Action Recognition] **ST-GCN** - [Spatial Temporal Graph Convolutional Networks for Skeleton-Based Action Recognition, 2018 (Sijie Yan, Yuanjun Xiong, Dahua Lin)](https://arxiv.org/pdf/1801.07455.pdf)

해당 모델들은 모두 Pytorch 프레임워크를 사용하여 학습하였습니다. 

<!--
**Flowchart** <br>
<img width="500" alt="tech stack" src="https://user-images.githubusercontent.com/63901494/129585982-4705c85e-c81b-4b97-87ef-20a37119d999.png"> 
-->

## Result
Streamlit 라이브러리를 통해 서비스 프로토타입을 제작하였습니다. <br>

<img width="600" alt="demo-streamlit" src="https://user-images.githubusercontent.com/63901494/130187131-cff4b3e4-0ac0-4475-a6ec-8433e875bbc0.gif">

## Usage
학습시 모델의 용량 문제로 인해 GPU 환경에서 실행하기를 권장합니다. <br>
Google Colab을 활용할 시 [런타임] - [런타임 유형 변경] - [GPU]로 설정을 바꿔주세요.
### 1. Installation
```
git clone https://github.com/Taehee-K/ASAP.git
cd ASAP
```
### 2. Train
[Keypoint RCNN](./KeypointRCNN)
```
cd KeypointRCNN
pip install -r requirements.txt
python train.py
```
[HR-Net]()
```
cd HR-Net
pip install -r requirements.txt
python train.py
```
[ST-GCN](./ST-GCN)
```
cd ST-GCN
pip install -r requirements.txt
python torchlight/setup.py install
python main.py recognition -c config/st_gcn/kinetics-skeleton/train.yaml --device 0
```
### 3. Inference
```
cd demo
pip install -r requirements.txt
python torchlight/setup.py install
```
Terminal
```
python test_final.py recognition -c work_dir/stgcn_demo.yaml
```
Streamlit
```
streamlit run app.py
```

## Reference
* [pytorch/vision](https://github.com/pytorch/vision)
* [yysijie/st-gcn](https://github.com/yysijie/st-gcn)

## Contributors
<table>
  <tr>
    <td align="center"><a href="https://github.com/Taehee-K"><img src="https://user-images.githubusercontent.com/63901494/129619988-1a959834-313c-443c-84c2-4fc2db8ef8f6.jpg" width="100" height="100"><br /><sub><b>김태희</b></sub></td>
    <td align="center"><a href="https://github.com/wonjae-yang"><img src="https://user-images.githubusercontent.com/63901494/129583717-42d19759-7586-4de0-aea9-5e935295f4dd.png" width="100" height="100"><br /><sub><b>양원재</b></sub></td>
    <td align="center"><a href="https://github.com/SK-jeong"><img src="https://user-images.githubusercontent.com/63901494/129582209-1d1d194e-cf3e-48d6-b097-35a7b855a683.jpg" width="100" height="100"><br /><sub><b>정성경</b></sub></td>
    <td align="center"><a href="https://github.com/miso-choi"><img src="https://user-images.githubusercontent.com/63901494/129582741-870a71c4-6a3a-4c99-9e86-e1cd0290070a.png" width="100" height="100"><br /><sub><b>최미소</b></sub></td>
    <td align="center"><a href="https://github.com/HwangChaewon"><img src="https://user-images.githubusercontent.com/63901494/129582279-430df734-43ae-451a-991d-7bb62a170eb0.png" width="100" height="100"><br /><sub><b>황채원</b></sub></td>
    <td align="center"><a href="https://github.com/HyeJung-Hwang"><img src="https://user-images.githubusercontent.com/63901494/129582233-ccf2137f-89db-4559-9bc3-be23a133e2a3.png" width="100" height="100"><br /><sub><b>황혜정</b></sub></td>
  </tr>
</table>