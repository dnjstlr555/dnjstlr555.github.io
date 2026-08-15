---
categories:
  - Papers
  - Robotics
tags:
  - policy-learning
date: '2026-07-16'
title: >-
  A Comprehensive Survey of Cross-Domain Policy Transfer for Embodied Agents
  (2024)
---

[원본 보기](https://arxiv.org/abs/2402.04580)

한줄 요약: 구체적인 로봇간 Domain gap을 정의하고 이를 구체적으로 해결하는 방안 정리

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-36.png)

### Abstract

robotics의 AI 기술은 많은 데이터를 필요로 한다. 하지만 보통 비용 및 시간 문제로 특정 lab 환경에서의 데이터만 데이터가 수집되는 경우가 많다. 따라서 사용하고자 하는 target domain에 대한 cross-domain policy transfer 방안이 필요하다.

본 논문은 도메인 격차가 발생하는 구간을 카테고리화 한 다음 추상화 하여 각 문제에 맞는 기술과 insight들을 정리하였다. 최종적으로 장기적으로 개선해야 할 문제점들을 제시하였다.

### Problem Definition

환경을 env, 몸체를 emb로 정의하고 몸체를 움직이는 역학은 env와 emb 모두 영향을 받는다. = T(env, emb) 이 세가지 (env, emb, T(env,emb))를 도메인 omega라고 하자

이때 모델이 구성되는 Markov Decision Process (S,A,Time,R,gamma)는 도메인 omega에 의존적이다.

우리의 문제는 MDP omega\_source에서 학습된 여러개의 policy 를 explicit/implicit하게 전달하는것이 목표이다. (J(pi\_src) << J(h(pi\_src)) ≤ J(pi\_tgt\*)), h는 transfer 함수

→ 데이터가 몸과 환경에 영향을 받는 MDP를 통해 수집됐다고 가정했을때, 보지 않은 우리의 (target) 환경에 policy를 적응시키는 함수 h를 찾을 수 있을까?

### Appearance gaps

appearance gaps : source domain과 target domain간 observation의 조건이 다른 상태 (색깔, 배경 물체, 렌더링 방법, 해상도등)

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image.png)
__

시뮬레이션 → Real world image 변환 (cyclegan) \[2020\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-1.png)
__

시뮬레이션/현실 segmentation으로 통합 \[2022\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-3.png)

적대적 loss를 도입해 domain 차이 줄임 \[2019\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-4.png)

domain randomization 적용 \[2022\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-5.png)

simulation policy → real world policy imitation learning으로 적응 \[2022\]

### Viewpoint gaps

viewpoint or perception gaps : appearance의 하위로, 카메라 포지션이 다른 상태

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-6.png)

아예 다른 시점의 전문가가 행한 action을 배우는 기술 \[2016\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-7.png)

Target 도메인의 시점으로 source 비디오를 생성해 모방하는 기술 \[2018\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-8.png)

Simulation 환경 자체를 다양한 object와 시점으로 구성 \[2018\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-9.png)

시간에 따른 feature 차이 극대화 + viewpoint를 통합하는 contrastive loss 구성 \[2017\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-10.png)

아예 viewpoint도 다르고 model도 다르면? meta-learning을 통한 few-shot learning 구성 \[2018\]

### Dynamic gaps

dynamic gaps : 아까 정의한 T(env, emb)에 대해서, env나 emb중 하나 이상이 바껴 역학이 바뀌고, 상호작용 결과가 달라지는 병목점 (모터 뻑뻑함, 무게, 조작 데드존, 마찰..)

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-11.png)

아주 다양하게 랜덤화한 시뮬레이션 환경에서 데이터를 수집해 통합된 policy 학습 \[2020\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-12.png)

물리 파라미터를 reference 환경과 구분 못하는 쪽으로 수정하는 gradient로 수정 \[2020\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-13.png)

실제 세계의 로봇 행동 동작 trajectory image와 시뮬레이션간의 간격을 줄이는 파라미터 업데이트 \[2021\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-14.png)

f(a)=시뮬레이션에서 a를 했을때 현실은 어디로 갈지 예측 s’, f-1(s,s’) 시뮬레이터에서 s’에 도달하려면 어떤 행동을 수행해야 할지 예측 a’ \[2017\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-15.png)

시뮬레이터의 파라미터를 바꿀 필요 없이, s, a, s’으로 가는 분포를 classification 한 뒤 적대적 학습 \[2020\]

Reward 분포를 조작 \[2022\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-16.png)

online, offline 병행 학습 - 서로의 맥락이 모델에 반영됨 \[2023\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-17.png)

s, a, s’으로 가는 역학이 target domain과 일치하는 데이터만 선택적으로 학습 \[2023\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-18.png)

action이 아니라 state의 trajectory를 모방하게끔 함 \[2020\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-19.png)

소스 정책의 action에 따라 state가 어떻게 변화하는지를 확인한다음 이를 변화시킬 수 있는 현실의 동작 a를 취하는 방식 \[2020\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-20.png)

action의 feasibility score를 정량으로 계산한다음 target policy에 얼마나 학습시킬지 반영 \[2021\]

### Morphology gaps

morphology or modality gaps : 몸체가 달라질때. (관절 타입, 모듈 형태..) (모달리티 관점에서는 sensor, actuation등의 개수가 달라지므로 modality gap이라고도 함)

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-21.png)

관절의 일부가 전반적인 위치를 따라올 수 있도록 하고 dynamics를 학습해서 한번더 최적화 \[2023\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-22.png)

학습 목표에 관절 식을 추가하여 최적의 morphology 및 policy 생성 \[2020\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-23.png)

몸 형태의 정보를 modality처럼 사용해 action sequence를 그에 맞게 생성할 수 있도록 유도 \[2023\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-24.png)

몸의 모듈별 token을 위치, 형태등으로 구성한 다음 각 모듈별 action을 수행하게끔 함 (transformer) \[2022\]

### Multi gap

모든 gap을 아우르는 극복 방안을 제시한 연구들, 특히 기존에는 simulation을 전제로 해서 timing 등을 맞출 수 있었지만 그런게 어려운 상황이라면?

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-25.png)

source에서 target으로 상태를 매핑하는 f와 행동을 매핑하는 g를 가지고 source policy를 변환 \[2020\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-26.png)

아까의 개념에서 확장해서 다음 state의 전이까지 고려하는 policy 출력 \[2020\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-27.png)

도메인 상태를 cyclegan 방식으로 통합한 뒤 현재 상태가 목표의 어느정도 수준에 도달했는지를 판별하는 시공간 진행 상황 판독기를 만들고 behavior cloning 학습에 사용 \[2021\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-28.png)

현재 task progress가 어느정도인지에 대해서 reward를 주는 방식으로 policy를 학습했더니 모방 학습이 가능 \[2021\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-29.png)

두 feature space를 적대적 loss와 Mutual Information loss 등으로 통합 \[2022\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-30.png)

사람의 손 영상에서 high level semantic 정보를 만든다음 이걸 decoding 하는 policy network를 학습 \[2022\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-31.png)

사람과 로봇의 feature space를 정렬하고, 이를 policy 학습에 사용 \[2022\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-32.png)

세가지 로봇을 학습시킬때 하나의 policy network를 사용하고, contrastive loss를 통해 비슷한 feature space를 형성하게끔 함 \[2023\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-33.png)

CLIP과 contrastive learning을 통한 공유되는 embedding space 형성 \[2023\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-34.png)

현재 영상을 기준으로 task의 완성까지 contrastive loss를 통해 거리 형성, 거리를 기준으로 policy 학습 \[2022\]

![](/assets/img/posts/2026-07-16-crossdomain-transfer/image-35.png)

이제까지의 모든 시도 없이 큰 데이터셋을 하나의 policy로 학습 시키기만 했는데 성능이 좋아짐 \[2023\]

### Discussion

**Source Domain Manipulation**

만약 source 환경을 수정할 수 있다면 target 환경과 유사하게 끔 변형하는 기술을 사용할 수 있다. 아예 시뮬레이션을 바꾸거나, 애초에 시뮬레이션 수집부터 다양한 상황을 고려하게끔 데이터를 증강할 수 있다.(Large scale source domain radomization)

또 몸체에 대한 정확한 모델이 존재한다면 더 이점이 있다. 또한 source domain이 좋은 환경이고 target domain의 수집이 비용이 많이 든다면 action등을 calibration 하는것이 더 싸게 먹힌다.

결론적으로 source domain을 바꾸는 것은 편차가 크고 환경 모델에 대한 사전 지식이 필요하며 많은 computation을 필요로 한다.

**Learn Domain Corrections**

Image-to-Image를 사용한 source→target 변환이나 view point 변환등을 사용할 수 있다. 아니면 state나 action trajectory를 source와 target이 일치하게끔 하는 policy/역학을 배울 수 도 있다.

여기에 여러가지 모듈을 부착하여 정확도를 높일 수 도 있고 reward weighting을 할 수 있다.

**Identify Domain Invariant Distributions**

state trajectory를 source와 target간 일치 시키려고 하는 imitation learning이 많이 시도 되고 있다.

하지만 이런 방법은 시야의 차이를 보완해주지 않기 때문에 제한적으로 동작하며 task에 대한 insight가 없어 긴 task에는 취약하다.

**Learn Domain Invariant Features**

딥러닝을 통해 두 feature간의 차이를 직접적으로 줄이는 학습을 수행한다. 그러나 이러한 방식은 보통 obs에만 적용되는 경우가 많고, 보통 표현력 자체가 전반적으로 하락하는 현상이 발생하기도 한다.

또한 시야에만 학습이 의존적이기 때문에 MDP 동작과 결부짓기 위해 시간 순서 맞추기를 추가적으로 학습에 반영하는 사례도 있다. language modality를 결합하는 vla 또한 이 분야중 하나이다.

**Build Hierarchical Control Paradigms**

목표를 중심으로 계층적으로 control을 구성하는 것 또한 좋은데, 고수준의 스킬을 로봇이 습득하게 한다. few shot 메타 러닝을 수행하는것이 이 분야의 하나이다.

## Challenges

### Different Sensor and Actuator Modalities

최근에 이 문제는 다른 모달리티의 다양한 로봇을 하나의 policy로 학습하면서 (Octo, RT-X) 해결되었다. 하지만 여전이 무엇을, 어떻게 이러한 데이터에서 학습할 수 있었는지는 블랙박스인 상태이다.

특히 수집된 데이터는 전부 expert 시현이기 때문에 저수준의 장비및 환경에서도 필요한 데이터를 수집할 수 있는 능력이 중요할 것 같다.

### Multi-Source Data Sharing

기존의 방법론들은 source 1개, target 1개 데이터셋 이렇게만 가정을 해왔다. 하지만 데이터의 부족 문제가 있기 때문에 여러개를 지원해야 하며, 이를 위해 통합된 특징 체계가 필요할 것이다.

통합된 특징 체계를 통해 너무 심한 outlier를 제거하는 등의 디테일도 필요할 것 같다.

### Continual Target Fine-Tuning

online 학습을 보통 가정하지 않고 방법론들이 제안되었는데, 보통 모터의 뻑뻑함이나 센서의 품질등은 시간이 지나면서 달라질 수 있다. 따라서 online 학습 또한 지원을 해야 한다.

이를 위해 continual fine tuning 기술을 사용할 수 있다. 이렇게 하면 cross-modality도 달성 가능할 것이다. (왜?)

### Generalization and Adaptation Trade-Off

아무리 기존의 방법론들이 제안 되었더라도 여전히 모든 domain에 강건하거나 한가지에 adapt 되는것 사이에는 trade off 관계가 존재한다. 최근에는 이러한 문제 해결을 위해 VLA가 강력한 대안으로 대두되고 있다.

하지만 기존 vllm의 표현을 잘 정렬하는 것 또한 아직 갈길이 멀다. vllm의 기존 표현과 로봇 observation을 잘 녹이는 기술이 필요하다.

### Off-Domain Policy Evaluation

target domain에서 데이터를 수집하는게 비싼 경우가 많다. 따라서 source domain에서 많은 학습을 진행 한 후 target domain에 적용하는 경우 domain gap 때문에 안정적으로 잘 작동하지 않을 수 있다.

하지만 off domain policy에 대한 연구는 많지 않다. 따라서 open-x dataset등을 통해 이러한 환경에서의 원할한 작동을 연구하는 것 또한 중요할 수 있다.
