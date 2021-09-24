---
title: Google Cloud Platform(GCP)
date: 2021-09-24 21:09:75
category: AI
thumbnail: { thumbnailSrc }
draft: false
---

&nbsp; &nbsp; 이번 시간에는 GCP에 대해서 알아보도록 하겠습니다😎 **GCP**란 **Google Cloud Platform의 줄임말**로 Google의 데이터 센터 인프라를 기반으로 compute, storage, networking, big data, machine learning 등 다양한 서비스를 제공하는 글로벌 클라우드 입니다. 이번 시간의 목적은 **Deep learning을 목적**으로 **GCP의 기본 개념과 사용법**을 익히는 것 입니다. 새로 알게되는 내용이 있다면 업로드하는 방식으로 계속 포스팅을 이어가겠습니다.

## Category

1. GPUs(all regions)
2. VM Instance
3. What is difference between region and zone?

## GPUs(all regions)

&nbsp; &nbsp; Compute Engine의 **VM instance에서 GPU를 사용**하기 위해서는 프로젝트를 만든 이후, **GPU 할당량을 신청**해 주어야합니다. GPU 할당량을 받는 방법은 아래와 같습니다.

① IAM 및 관리자 → 할당량

<p align="center">
   <img src="assets\2021-09-24\1.png"/>
</p>
②filter를 통해 GPUs(all regions) 검색하기

<p align="center">
   <img src="assets\2021-09-24\2.png"/>
</p>

위의 그림을 보게되면 한도가 1로 되어있는 것을 볼 수 있습니다. 프로젝트를 처음 만들고 저는 할당량을 **0→1로 신청** 했기 때문에 1로 나타나 있는 것인데요! 아마 이 글을 읽으시는 분들은 0일 겁니다.

③ GPUS(all regions)의 한도를 0→1로 바꿔주기

<p align="center">
   <img src="assets\2021-09-24\3.png"/>
</p>

&nbsp; &nbsp; 위의 그림처럼 GPUS(all regions)를 선택하여 할당량 수정을 눌러줍니다.

<p align="center">
   <img src="assets\2021-09-24\4.png"/>
</p>

&nbsp; &nbsp; 위 그림의 오른쪽 처럼 새한도를 0→1로 수정한 후 요청 설명을 작성하시면 됩니다. 저는 대회를 나가는데 GPU가 필요해서 달라고 적었습니다!😉

④ Email 확인

&nbsp; &nbsp; "③ GPUS(all regions)의 한도를 0→1로 바꿔주기"에서 요청 설명까지 해서 다음을 누르면 전화번호와 이메일을 입력하는 칸이 있습니다. 입력한 후 기다리면 Google 쪽에서 이메일이 오게 됩니다.

<p align="center">
   <img src="assets\2021-09-24\5.png"/>
</p>

&nbsp; &nbsp; GPU quota 신청이 accept되면 위와 같은 이메일이 오게 됩니다~!!😊 만약 **GPU(all regions)를 할당 받지 않는다면 나중에 아래와 같은 에러가 발생**합니다.

<p align="center">
   <img src="assets\2021-09-24\6.png"/>
</p>

&nbsp; &nbsp; 위의 순서대로 잘 따라 오셨으면 VM Instance에서 GPU를 사용할 모든 준비가 끝났습니다~!

## VM Instance

<p align="center">
   <img src="assets\2021-09-24\7.png"/>
</p>

&nbsp; &nbsp; **E2 VM**을 선택하게 되면 **GPU를 사용할 수 없습니다**😥 왜냐하면 **GPU를 붙힐 수 있는 CPU 플랫폼이 정해져 있기 때문**인데요! **GPU를 연결하기 위해서는 N1 VM, A2 VM을 사용**해야 합니다.

<p align="center">
   <img src="assets\2021-09-24\8.png"/>
</p>

&nbsp; &nbsp; 위의 그림을 보게되면 **N1 VM으로 바꾸어 GPU를 선택할 수 있게 된 것**을 볼 수 있습니다~😉

## What is difference between region and zone?

&nbsp; &nbsp; GCP의 documentation에 보면 **region**과 **zone**이라는 말을 볼 수 있습니다. 이 둘의 차이점은 무엇일까요? **region은 말 그대로 지역**을 가리킵니다. 리소스를 실행할 수 있는 **지리적 위치**를 나타내는데 이 **region은 많은 zone**을 갖고 있습니다.

&nbsp; &nbsp; 예를 들어, 대한민국의 **서울은 하나의 region**이 되며 이 region을 `asia-northeast3`라고 해봅시다. GCP documentation을 보게되면 서울(`asia-northeast3`) 안에 3가지의 zone이 존재하는데 이 3가지 zone의 이름은 `asia-northeast3-a`, `asia-northeast3-b`, `asia-northeast3-c`가 됩니다.

&nbsp; &nbsp; 즉, **서울 region**(`asia-northeast3`)안에 **GPU가 존재하는 3개의 영역들(zone)이 존재**하며 이 3개의 zone은 위와 같이 `asia-northeast3-a`, `asia-northeast3-b`, `asia-northeast3-c` 라고 불리는 것입니다!😊

&nbsp; &nbsp; 또한, 위의 VM Instance를 설정해줄 때 region을 선택하는 란이 있는데 GPU를 사용하기 위해 사용하는 VM Instance는 지연시간이 상관없기 때문에 싼 곳을 골라주면 됩니다!😎

## Reference

- GCP basic concepts
  - Difference between region and zone: [https://www.waytoliah.com/1437](https://www.waytoliah.com/1437)
- 방화벽
  - jupyter notebook use exclusively the port 8888: [https://stackoverflow.com/questions/51739199/error-unable-to-open-jupyter-notebook-port-8888-is-already-in-use](https://stackoverflow.com/questions/51739199/error-unable-to-open-jupyter-notebook-port-8888-is-already-in-use)
  - what is difference between 0.0.0.0 and 127.0.0.1?\_1: [https://rootable.tistory.com/447](https://rootable.tistory.com/447)
  - what is difference between 0.0.0.0 and 127.0.0.1?\_2: [https://www.howtogeek.com/225487/what-is-the-difference-between-127.0.0.1-and-0.0.0.0/](https://www.howtogeek.com/225487/what-is-the-difference-between-127.0.0.1-and-0.0.0.0/)
  - 방화벽 설정 및 전반적 설명: [https://jeinalog.tistory.com/8](https://jeinalog.tistory.com/8)
- SSH
  - apt-get vs apt 차이: [https://ksbgenius.github.io/linux/2021/01/13/apt-apt-get-difference.html](https://ksbgenius.github.io/linux/2021/01/13/apt-apt-get-difference.html)
- VM Instance
  - GCP에서 GPU 할당받고 우분투 가상환경 실행 및 Tolo v3 실행 blog: [https://turtlefromocean.tistory.com/3](https://turtlefromocean.tistory.com/3)
  - GCP 전반적 설명(2017년): [https://jybaek.gitbook.io/with-gcp/compute/compute_engine](https://jybaek.gitbook.io/with-gcp/compute/compute_engine)
- GPU
  - GPU를 사용할 수 있는 VM(N1 VM, A2 VM): [https://cloud.google.com/compute/docs/machine-types?hl=ko](https://cloud.google.com/compute/docs/machine-types?hl=ko)
  - GPU 사용가능한 region 및 zone(GCP documentation: compute engine의 GPU→GPU 리전 및 영역 가용성): [https://cloud.google.com/compute/docs/gpus/gpu-regions-zones](https://cloud.google.com/compute/docs/gpus/gpu-regions-zones)
- Region & Zone
  - short response time is related to distance from source to destination(Deeplearning과 관련된 VM은 응답 시간과 관련 없기 때문에 싼 region 선택!): [https://kcal2845.tistory.com/49](https://kcal2845.tistory.com/49)
