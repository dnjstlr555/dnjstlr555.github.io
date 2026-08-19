---
categories:
  - Papers
  - Cognitive Science
tags:
  - bci
title: >-
  Comparing Recognition Performance and Robustness of Multimodal Deep Learning
  Models for Multimodal Emotion Recognition (2021)
date: '2026-08-19'
math: true
---

## Introduction

감정 예측 시스템은 BCI, 질병 예측, 감정 통제등에 활용될 수 있어 중요합니다.  그 중에서도 EEG는 뇌의 직접적인 활동에 대한 정보가 있기 때문에 감정 예측 모델에 활용되고 있습니다.

특히 감정의 복잡성 때문에 멀티 모달리티 전략도 좋은 선택지가 됐습니다. 단순히 추출된 특징을 concat해 모델을 학습하는 사례들도 있지만 딥러닝 기술의 발전으로 최소한으로 가공된 신호를 처리하는 방법론 또한 제안되었습니다.

저자는 딥러닝 기반 멀티 모달 퓨전을 두가지로 분류하는데 하나는 joint representation, 1차적으로 각각의 modality별 layer를 둔다음 표현을 합쳐 분류하는 방식이고, 두번째는 coordinated representation으로 각각의 모달리티별 표현에 제약을 거는 방식입니다.

따라서 해당 논문에서는 deep canonical correlation analysis (DCCA → coordinated representation), bimodal deep autoencoder (BDAE → joint representation) 두가지를 비교합니다. Task는 eye+eeg 데이터의 감정 분류 이고 사용한 데이터셋은 총 5가지입니다.

## Related Work

**전통적인 멀티모달 퓨전 방법론**의 경우 early fusion, late fusion, hybrid fusion이 있습니다. 

early fusion은 특징간의 상관관계를 일찍 확인하고 둘간의 관계성을 활용할 수 있다는 장점이 있지만 차원이 증가하기 때문에 과적합의 위험이 높다는 단점이 있습니다.

late fusion의 경우 저자가 decision-level fusion이라 명하였는데, 앙상블 방법같이 제일 confidence가 높은 예측 결과를 취하거나 (MAX fusion) 평균을 내거나 (SUM fusion) 예측이 잘되는 경우의 수를 따지거나 (Fuzzy integral) 하는등의 방법론을 소개했습니다. 단점은 규칙이 너무 쉬우면 일반화가 어렵다는 단점이 있습니다. 

hybrid는 early와 late을 둘다 도입한 방법론을 의미하고, 일부 연구 사례가 소개되었습니다.

**딥러닝 기반 방법론**의 경우 아까 소개했던 joint와 coordinated 방법론이 있습니다. joint는 일반적인 경우이고, coordinated는 두 representaton이 서로를 참조하게 만드는 방법론입니다.

## Methods

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image.png)

### DCCA

각 모달리티 별 표현 $O_1, O_2$를 다음과 같이 정의하겠습니다. 

$$
O_1=f_1(X_1;W_1), O_2=f_2(X_2;W_2)
$$

목표 함수는 다음과 같습니다.

$$
(W_1^*,W_2^*)=argmax(corr(O_1,O_2))
$$

이때 corr 그리고 미분 식은 다음과 같이 정의됩니다.

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-1.png)

이 목표 함수에 의해 O\_1과 O\_2는 서로 상관계수가 높은 특징점을 형성하게 됩니다. 이때 이 두가지를 가중합하거나 gating network를 구성하여 fusion하게 됩니다.

![가중합 예시 (파라미터 수동 탐색)](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-2.png)
_가중합 예시 (파라미터 수동 탐색)_

![Gating network 예시 (파라미터 샘플 단위 자동 생성)](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-3.png)
_Gating network 예시 (파라미터 샘플 단위 자동 생성)_

그 이외에도 Concat, MAX, Fuzzy, BDAE가 baseline으로써 비교되었습니다.

## Experimental Settings

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-4.png)

5개의 emotion recognition 데이터 셋이 비교 되었습니다.

EEG 특징의 경우 4s window STFT를 통해 differential entropy를 5개의 주파수 대역 (delta, theta, alpha, beta, gamma)에 대해서 추출하였습니다. Eye 특징의 경우 소프트웨어에서 제공하는 동공 직경, 눈 깜빡임 시간등 33개의 추출된 특징을 그대로 사용하였습니다.

## Results

### Seed-V 데이터셋에 대한 결과

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-5.png)
__

Grid search 결과, 가로줄은 출력 차원이고 세로줄은 EEG feature의 가중치 (EEG+EYE = 1)를 의미합니다.

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-6.png)

CCA Loss term 파라미터 결과 0.7이 제일 좋았습니다.

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-7.png)

(a) - EEG, (b) eye, (c) BDAE, (d) DCCA 순서대로 성능을 보고한 그림입니다.

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-8.png)

맨 위가 가공하기 이전 feature, 두번째가 DCCA layer를 지난 feature, 마지막이 최종적으로 결합된 feature로 랜덤한 3개의 참가자에 대해서 시각화를 진행했습니다. 그 결과 DCCA 처리 만으로도 EEG와 Eye의 맥락이 일치하고, 어느정도 분별이 생기는 feature가 형성됨을 확인할 수 있었습니다. 

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-9.png)

특히 한가지 감정에 대해서 봤을때 EEG와 Eye가 더 결합되는 것을 확인하기 쉬웠습니다.

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-10.png)

gating network 기반 방법론을 사용했을때 모델이 도출한 weight를 시각화 한 모습입니다. 아까의 matrix와 마찬가지로 평균적으로 0.7-0.3대의 비율로 분포해 있음을 확인할 수 있었습니다.

### DREAMER 데이터셋에 대한 결과

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-11.png)

arousal, valence, dominance classification을 각각 수행했을때 어느 파라미터가 좋았는지 확인을 했고, 이 각각의 classification에 대해 다른 parameter를 적용하였습니다.

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-12.png)

성능 결과의 경우 다음과 같았습니다.

### 예측 성능 비교

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-13.png)

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-14.png)

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-15.png)

SEED, SEED-IV DEAP 데이터셋에 대한 성능 비교 테이블입니다. 모두 제일 좋은 성능을 달성하였다고 합니다.

### 노이즈 강건 비교

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-16.png)

EEG와 Eye feature에 gaussian noise를 추가한 결과 성능이 점진적으로 감소하였습니다. 0.7 파라미터 모델이 초기 성능은 제일 좋았지만 노이즈에는 쉽게 무너지는 속성이 있었습니다. 0.5 모델의 경우 두 모달리티의 데이터를 골고루 사용하기 때문에 성능 하락 폭이 적다는 생각이 들었습니다. 

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-17.png)

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-18.png)

EEG의 일부를 노이즈로 대체하였을때 방법론에 따라 성능 하락이 얼마나 일어날지를 비교한 테이블입니다. 0.3 모델이 노이즈에 제일 강건하였습니다.

결과적으로 DCCA는 emotion 정보를 살리고 필요없는 부가정보를 지워 correlation을 유지하려고 하기 때문에 negative information을 효과적으로 지울 수 있는 근거가 된다고 설명하고 있습니다.

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-19.png)

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-20.png)

![](/assets/img/posts/2026-08-19-emotion-recognition-dcca/image-21.png)
__

동일한 실험을 다른 데이터셋에서도 진행했을때 강건한 성능을 얻을 수 있었습니다.
