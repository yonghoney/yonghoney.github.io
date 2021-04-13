---
title: "[딥러닝 환경 세팅] RTX 3080 tensorflow, pytorch 간략 환경 공유 (Windows)"
date: 2021-03-30 09:40:00 -0400
categories: 
- RTX3080
- python
- tensorflow
- pytorch
- gpu
- RTX3090
- RTX3070
- 딥러닝
---

많은 블로그에서 nvidia-driver, cuda, cudnn 설치하는 방법을 공유하고 있기 때문에, nvidia에서 제공하는 페이지만 공유하겠습니다.

[Link: https://docs.nvidia.com/deeplearning/cudnn/support-matrix/index.html](https://docs.nvidia.com/deeplearning/cudnn/support-matrix/index.html)

cudnn 버전에 맞는 cuda, nvidia-driver 버전이 나와있으니 참고하면 좋을 것 같습니다.

# 시스템 환경
os : windows 10 pro  
cpu : AMD Ryzen7 3700X  
memory : Samsung DDR4 16GB X 2 2666 (3200으로 오버클럭)  
gpu : Nvidia GeForce RTX 3080 X 1  
nvidia-driver : 461.92 (03/16/2021)  
cuda : cuda_11.2.2_461.33_win10  
cudnn : cudnn-11.2-windows-x64-v8.1.1.33  
python : 3.8.7 (pycharm)  

### tensorflow 버전
2.4.1 이전 버전의 tensorflow 사용 시 ./cuda/bin/cusolver64_11.dll 파일 이름을 cusolver64_10.dll로 바꿔줘야 tf.test.is_gpu_available()에서 True 값을 반환합니다.  

하지만 tf-nightly 2.5.0.dev20210326 버전, tf-nightly 2.6.0.dev20210328 버전(다른 날짜는 시도 해보지 않음)의 경우 cusolver64_11.dll로 그대로 두고 진행해도 True 값을 반환하는 것을 확인했습니다.  

2.4.1 이전 버전에서 True 값을 반환할 뿐 정상작동 하는지에 대한 유무는 확인하지 않았으니 2.4.1 이전 버전이 필요하신 분은 직접 example code를 돌려 확인해보는 것을 추천드립니다.  

tf-nightly 버전의 경우 정상 작동을 확인했습니다.  


example code를 돌릴 경우 저는 처음에 MNIST 기본 sample code를 돌려 gpu로 돌린 것보다 cpu로 돌린게 속도가 2배 이상이 나오는 것을 보고 이상이 있다고 생각했으나 stackoverflow에 문의한 결과 고수님들께서 주신 답변으로는 연산량이 충분하지 않기 때문에 그렇다, GAN 같은 연산량이 충분한 example code를 돌려봐라 해서 돌려본 결과 gpu가 cpu에 비해 적으면 10배에서 많이 차이날 때는 20배까지 차이 나는 것을 확인했습니다.  


따라서 example code를 돌릴 때 연산량이 충분한 문제로 돌려보는 것을 추천드립니다.


### pytorch 버전
pytorch는 제가 메인으로 다루지 않아 torch.cuda.is_available() 확인만 했습니다.  

최신 버전의 1.8.1로 설치할 경우 False가 나와 검색을 해서 확인한 결과 가상환경 command(또는 각자의 환경)에서 다음과 같이 설치를 해주고  

pip install torch==1.7.1+cu110 torchvision==0.8.2+cu110 torchaudio===0.7.2 -f https://download.pytorch.org/whl/torch_stable.html  

한 단계 다운그레이드 할 경우 torch.cuda.is_available() == True를 확인했습니다.  

[참고 : https://mambo-coding-note.tistory.com/513](https://mambo-coding-note.tistory.com/513)

환경 구축이 잘 되지 않아 몇 주의 시간이 걸렸지만 gpu가 제 역할을 하고 있는 것을 보니 기쁜 것 같네요.
