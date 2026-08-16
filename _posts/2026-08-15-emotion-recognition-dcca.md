---
categories:
  - Papers
  - Cognitive Science
tags:
  - bci
title: >-
  Comparing Recognition Performance and Robustness of Multimodal Deep Learning
  Models for Multimodal Emotion Recognition (2021)
date: '2026-08-15'
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

### DCCA

각 모달리티 별 표현 $$$O_1, O_2$$$를 다음과 같이 정의하겠습니다. 

$$
O_1=f_1(X_1;W_1), O_2=f_2(X_2;W_2)
$$

목표 함수는 다음과 같습니다.
