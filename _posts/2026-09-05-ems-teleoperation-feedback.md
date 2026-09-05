---
categories:
  - Papers
  - Robotics
tags:
  - teleoperation
title: >-
  [논문 리뷰] TelePulse: Enhancing the Teleoperation Experience through
  Biomechanical Simulation-Based Electrical Muscle Stimulation in Virtual
  Reality (2025)
date: '2026-09-05'
math: true
---

## Introduction

Teleoperation에서의 햅틱 피드백은 조작과 실제 수행간의 gap을 극복하는데 있어서 중요한 역할을 합니다. → 이때 햅틱 피드백은 대중에 잘 알려져 있는 것처럼 단순히 진동 피드백만을 포함하지 않고, 더 다양한 범위를 포함하여 haptic feedback이라 합니다. 기존의 방법론들은 다음과 같습니다.

- 부착된 기계 장치

  - 일대일 대응의 똑같은 로봇 장비나 small scale의 장비로 teleoperation 및 피드백 수행
- 웨어러블 햅틱 장치

  - 진동등으로 알려줌
- 외골격 기반 웨어러블 장치

  - 외골격에 힘을 가해서 알려줌

하지만 이러한 장비들은 섬세하지 않거나 (웨어러블 햅틱) 가지고 다니기 불편하거나 (부착된 기계 장치) 여러 요소가 작용해 비용 및 무게 측면에 불리한 점 (외골격 기반)의 단점들이 존재합니다.

따라서 저자들은 이러한 단점을 제안한 TelePulse를 제안합니다. 근육에 전기 자극을 주는 방식(EMS)으로 force feedback을 제공하고, 입을 수 있으며 가볍습니다. 특히 EMS의 구체적인 파라미터는 참여자의 체중등의 물리적 조건에 맞게 설정해야 하기 때문에, 이를 해결하기 위해 재활 분야에서 발전해온 생체역학 시뮬레이션을 적용하였습니다.

해당 연구는 생체 역학 시뮬레이터를 통해 어느정도 힘의 크기와 방향을 전달해야 할지를 계산하는 PhySim과 힘 정보가 주어지면 어느정도 전기 자극을 줘야 할지를 계산하는 EleStim 두가지로 나눠져 있습니다.

EMS의 대한 평가는 EMG를 통해 실제로 측정된 근육 활성도를 바탕으로 둘이 유사한지를 확인하였고, 실제로 정밀한 teleoperation을 요구하는 두가지 task (sanding, drilling)를 통해 실제 작업 성능이 향상됐는지를 평가하였습니다. baseline으로는 기존에 수립된 웨어러블 haptic 기반 시스템과 PhySim을 적용하지 않은 EMS feedback에 대해서 평가했습니다.

### Research Questions

- TelePulse가 생체역학적 시뮬레이션과 EMS를 통해실제 근육 활성화를 얼마나 정확하게 묘사할 수 있을까?
- 유저 경험과 테스크 수행 성능이 향상될 수 있을까?
- 어떤 한계점이 있고 이걸 어떻게 개선할 수 있을까?

## Background

### Teleoperation Haptic System

Haptic system은 로봇을 실시간으로 제어하는 teleoperation 과정에서 발생하는 힘을 사용자에게 전달하는 시스템을 얘기합니다. Introduction에서 언급한것처럼 두가지의 흐름이 있는데, 먼저 첫번째는 Grounded mechanical devices이고, 두번째는 Body-attached devices 입니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image.png)

(Phantom Omni)

Grounded mechanical devices는 책상등에 단단히 고정하여 로봇이 느끼는 힘을 실제로 유저에게 전달할 수 있는 능력이 있습니다. 예시 사진처럼 누르는 힘의 저항을 설정하여 로봇의 현재 작업 환경을 표현하는등의 일을 합니다. 하지만 단점으로는 단단한 지면에 고정되어 있어야 하기 때문에 이동성이 제한되고 조작 범위에도 한계가 있는 경우가 많습니다. 예시 장비로는 Novint Falcon, Phantom Premium/Omni등이 있습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-1.png)

(Won et al)

Body-attached devices는 몸에 직접 피드백 장치를 부착하여 이동성이 높은 장점이 있습니다. 또한 피드백의 표현이 조금 더 자유롭습니다. 기존에는 tactile feedback 시스템을 중점으로 발전해왔습니다. tactile feedback은 전기나 진동을 이용해 피부 표면 수준에서 피드백을 주는 시스템입니다. 이런 시스템은 여러 선행 연구에서 좋은 결과를 보였지만 결국 Grounded mechanical devices처럼 직접적인 힘의 크기와 방향을 유저에게 전달할 수 없다는 한계점이 존재합니다.

따라서 이 두 지점 사이의 간극이 존재하는데, 이 간극을 메울 수 있을지 본 연구에서 밝히고자 하였습니다.

### EMS Haptic System

현재 수준은 근육에 전기 자극을 직접적으로 줘서, 무거운 물체나 저항을 줘야 할때의 시나리오를 성공적으로 모방한것으로 알려져 있습니다. 예를 들어 벽이나 무거운 박스등이 있습니다.

하지만 이런 EMS가 teleopration에 적용되는 사례는 없었습니다.

### Biomechanical Simulation

원래는 Rehabilitation 과정의 인체 역학 및 feedback을 제공하기 위해 인간의 몸을 시뮬레이션 하고 이를 활용하는 연구가 활발히 진행되었습니다. 재활 과정에서 시뮬레이션을 사용해 non-invasive하게 인체에 가해지는 세부 힘을 측정하고 재활 목표에 적합할 수 있도록 피드백을 제공해 정확도나 성능을 높이는 등의 선행 연구가 있었습니다.

그리고 이 논문처럼 haptic feedback에 사용할 목적으로 시뮬레이션을 하반신에 사용한 사례가 있었습니다. OpenSim을 사용했고, 축구공을 차거나 물에 저항을 받는 등의 상황을 OpenSim으로 계산한 다음 EMS를 통해 저항값을 주었습니다 (ErgoPulse - 동일 랩실의 다른 논문). 본 논문은 이를 Teleoperation에 적용했다는 차이점이 있습니다.

## Implementation

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-2.png)

전반적인 아키텍처는 다음과 같습니다. 먼저 VR 환경에서 컨트롤러를 조작해 Manipulator의 위치를 변경합니다. 이제 manipulator의 F-T Sensor 정보와 VR HMD에서 나온 body motion 정보를 결합하여 PhySim 시스템에 입력합니다. 해당 시스템은 Model 1과 Model 2로 나눠져 있는데 Model 1은 VR HMD의 body motion 정보만을 바탕으로 몸의 관절 토크를 생성하고, Model 2는 이 상황에서 F-T Sensor에 의한 힘이 가해졌을때 어떻게 변화할지를 예측합니다. 이 두가지 힘 예측 결과를 빼서 어느정도 힘을 어느 관절에 적용해야 할지에 대한 가이드 값을 계산합니다.

실제로 적용할때 1) manipulator 관절의 위치와 사람 관절의 위치에 대한 차이, 2) 작업에 따른 힘의 크기에 대한 차이들로 인해 발생하는 문제점들이 존재합니다. 본 논문에서는 각도 변환과 사람 개개인에 맞는 힘의 크기 정렬 세션을 통해 이를 해결하였다고 합니다.

그리고 EleStim은 이러한 정보를 바탕으로 전기 자극을 제공합니다. 이때 근육 위치 차이등에 따른 전극 위치의 조절이 필요하고, 개인별로 다른 환경을 사용하기 때문에 개인별 맞춤 세션이 필요합니다. 개인 맞춤 세션은 torque-intensity 관계를 포착하여 이를 활용하는것으로 진행하였다고 합니다.

### Personalization Process of EMS

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-3.png)

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-4.png)

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-5.png)

Torque-EMS간의 관계를 찾기 위해 사진과 같은 장비를 만들었습니다. 예를 들어 이두근과 삼두근에 전기 자극을 주고 이로 인해 Load Cell에 생기는 부하를 바탕으로 둘간의 관계를 찾고, 다음과 같은 식을 fitting합니다.

  
$T(I) = ae^{bI}(I_{MT}<I<I_{PT})$ 

이때 a와 b는 learnable parameter, I는 EMS intensity이고 e는 자연상수입니다.

실제로 사용시에는 해당 식을 바탕으로 Torque가 주어지면 EMS를 얼마나 발생시킬지에 계산하는데 사용됩니다.

### Coordinate Transformation for Simulation

실제로 나타내고자 하는 힘은 manipulator의 end effector에 가해지는 힘으로 힘과 방향을 가지고 있고, 이걸 인체에 표현하는게 목적입니다. 하지만 end effector에서 얻는 힘의 크기와 방향은 manipulator 좌표계에 종속되어 있는게 문제입니다. 또 사람의 손 각도 또한 수시로 바뀌기 때문에 사람 손에 대한 좌표계에 종속되어 있다고 봐도 될것 같습니다.

두가지 좌표에 gap이 존재하기 때문에 저자는 먼저 실제 세계 좌표계를 기준으로 로봇 model end effector 각도와 사람 손 각도를 표현하고, 이 두가지에 대해서 다음을 계산합니다.

$ q_{m\rightarrow u}=q_{m\rightarrow w}\otimes q_{u\rightarrow w}^* $

이때 m은 robot 각도, w는 세계 기준 각도, u는 사용자의 손 끝 각도를 의미하고, 이렇게 계산하여 로봇의 기준에서 상대 회전 각을 구할 수 있습니다.

해당 정보를 통해 다음을 계산하여 손에 가할 force와 torque를 계산할 수 있습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-7.png)

### EMS Hardware

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-6.png)

기존의 수립된 EMS 장치를 이용하되 이 장비들의 전기 부하를 조정하는 아두이노 보드를 추가하여 새로운 시스템을 구축하였습니다. 결과적으로 45ms정도의 반응 속도 (feedback에까지 걸리는 시간)를 구축하였습니다. → motion capture 15ms, physical simulation 10ms, wifi 5ms, muscle contraction latency 15ms.

## Experiments

### EMS Intensity ↔ EMG Data in Force-Directed Tasks

계속 논문을 읽으면서 어떻게 EMG와 EMS 데이터를 비교했을까 생각했는데, Implementation을 읽으니 구체적으로 이해할 수 있었습니다. PhySim을 통해 Joint에 힘을 가했을때 각 관절/근육별로 가해지는 부하를 모델링 할 수 있고, Torque-EMS Intensity에 대해 fitting 해둔 정보가 존재하기 때문에 바로 Torque에 따른 EMS를 적용할 수 있습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-8.png)

저자는 이러한 EMS의 출력 결과가 실제 상황에서의 EMG 부하의 패턴과 일치하는지를 보고자 하였습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-9.png)

먼저 비교를 위해 로봇에 핸들을 장착한 뒤 Gforce PRO EMG Sensor를 삼두/이두근쪽과 전완근쪽 각각 2개를 설치하고 로봇을 앞 뒤 양 옆으로 민 다음 민 상태를 유지하였습니다. 구체적으로 6개 방향에 대해 3번의 trial을 거쳤고 총 인원은 8명이었습니다. 이렇게 얻은 데이터에 대해서 PhySim을 통해 게산된 EMS 신호 (TelePulse)와 단순히 가해지는 힘을 일정 비율로 변환하는 NonBioSim의 baseline을 비교하였습니다. 그리고 EMG 신호는 lowpass filter를 적용한 다음 linear envenlop를 적용하여 힘의 작용 trend를 비교하였습니다. (선행 연구에서 사용되는 메소드라고 합니다.)

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-10.png)

단순히 비교했을때 NonBioSim은 그냥 단순 자극으로 계산되는 반면 TelePulse는 실제 EMG 패턴과 위치 및 패턴, 타이밍 등에서 상당히 일치하는 모습을 확인할 수 있었습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-11.png)

Channel별 신호와 EMG 신호간의 직접적인 Error율 비교에서도 두 방법론간 통계적으로 유의미한 차이가 있었습니다. 특히 force(왼쪽)와 direction(오른쪽)의 모든 항목에서 TelePulse가 공통되게 낮은 에러율을 보였습니다.

이러한 결과들은 먼저 개인 맞춤형 세션을 통해 세밀한 힘을 제공해 줄 수 있는 점과, 시뮬레이션을 통해 유저의 팔 포즈등에 의해 가해지는 힘 계산을 수행하고 피드백을 제공하는 점때문에 나타났다 보고하고 있습니다. 반면 기존의 방법론을 대변하는 NonBioSim (단순 manipulator 힘을 그대로 선형변환하여 EMS로 전달)은 이러한 부분에서 병목이 존재한다고 볼 수 있습니다.

### Examining TelePulse System’s Effect on Teleoperation Tasks

이제 본격적으로 실제 Task에 대해서 평가하는 파트입니다. 실제로 수행 성능이 얼마나 향상됐는지, 그리고 사용자의 인식 개선을 얼마나 이루었는지에 대해서 평가하였습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-12.png)

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-13.png)

Task 설계를 위해 Teleoperation 과정에서 발생하는 복잡한 힘을 2가지로 카테고리화 하였는데 하나는 연속적인 힘이고 다른 하나는 급격한 힘입니다. 연속적인 힘은 Sanding Task로 큰 나무판자를 연마기를 통해 연마하는 테스크를 수행합니다. 급격한 힘은 Drilling Task로 여러개가 겹쳐 있는 나무판자 여러개를 드릴로 뚫되 정해진 개수만 뚫어야 하는 Task였습니다.

비교 대상은 아예 feedback이 주어지지 않는 Baseline, 몸에 대한 시뮬레이션을 진행하지 않은 NonBioSim, 그리고 본 논문에서 제안하는 TelePulse, 마지막으로 피부 표면에서만 피드백을 주는 ElecTac → TelePulse를 약하게 수행 하는 방법론 총 4가지에 대해서 비교하였습니다.

이러한 4가지 방법론에 대해 HCI에서 사용되는 자가보고 설문을 각 세션별로 수행하도록 하였습니다 (Witmer-Singer presence questionnaire). 항목은 내가 원하는대로 빠르게 움직였는지, 주변 환경에 대한 센서 정보는 적절했는지, 방해하는 요소가 있었는지, 현실적으로 몰입할 수 있었는지 등이 있었습니다. 또한 자유로운 질문도 받았습니다. 참가 인원은 총 8명 이었으며 Latin-square design을 통해 블럭 위치가 데이터에 영향을 주지 않게끔 하였습니다.

**Sanding Task**

먼저 VR 환경으로는 Depth camera + 3d reconstructed operation zone이 사용되었습니다. 특히 실시간으로 로봇의 위치와 변화하는 환경을 관측할 수 있었습니다. 참가자는 VR controller를 통해 maniuplator의 end effector 위치를 조정할 수 있었습니다. 결과적으로 4개의 조건에 대해 각각 2번씩 trial을 수행하면서 한명당 총 8번의 trial을 수행하였고, trial을 시작하기 전 2분의 연습 세션이 있었습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-14.png)

Sanding은 모든 면에 고르게 적용해야 하는데 확실히 TelePulse가 아닌 다른 방법론은 고르지 못하는 모습이 보입니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-15.png)

정량적으로 계산을 했을때에도 통계적으로 유의미하게 다른 방법론과 차이가 나는 모습을 보였습니다. (Bonferroni correction 적용)

결과적으로 나머지 3개 항목들끼리 유의미한 통계적 차이가 없었기 때문에 TelePulse의 PhySim 계산 부분이 큰 지분을 차지했다고 저자는 설명하고 있습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-16.png)

자가 보고 지표에서도 통계적으로 유의미할 정도로 TelePulse가 유리했다고 보고하였습니다.

**Drilling Task**

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-17.png)

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-18.png)

먼저 두개의 판을 준비합니다. (1번과 3번) → 그리고 그 사이에 랜덤한 위치에 2번째 판을 위치시킵니다. (1cm-2cm 랜덤) 그리고 참여자에게 2번째 드릴까지 뚫으라고 지시합니다. 이렇게 한 이유는 여러 trial에서 참가자가 구멍의 위치를 외우게 하지 않고 피드백에 의존하게끔 하기 위함이었습니다. VR 컨트롤러의 트리거를 눌러서 드릴을 작동시킬 수 있었으며, 마찬가지로 2분의 연습 세션 이후 4가지 조건에 대해 2개의 trial을 수행했습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-19.png)

이번에는 TelePulse 뿐만 아니라 ElecTac도 좋은 성능을 보였습니다.

![](/assets/img/posts/2026-09-05-ems-teleoperation-feedback/image-20.png)

사용자 자가 보고 면에서도 여전히 TelePulse가 우세하였습니다.

**사용자 설문조사**

Task 수행과 관련해서 자유롭게 의견을 남기도록 하였습니다.

- End-effector에 걸리는 힘의 크기와 방향에 대한 정보를 정확히 알 수 있었다고 보고
- 다른 방법들 대비 더 현실적이었다고 보고
- 하지만 너무 정보가 많아서 Task에 집중하기 어려웠던 점도 있었음
- 전기 자극때문에 근육이 피로하거나 저리는 감각이 있었음.

## Discussion

RQ1 → 실제 근육 활성 패턴을 TelePulse가 얼마나 정확하게 재현할 수 있었는가?

성공적이었고 다른 방법론들보다 우세하였습니다. 선행 연구의 경우 이러한 근육의 활성 및 활성 위치에 대한 개인 차이가 큰데, PhySim을 통해 이를 성공적으로 계산할 수 있었다고 보고하고 있습니다.

RQ2 → 이를 통해 teleoperation task 성능과 사용자 경험을 향상시킬 수 있는가?

두가지 Task에 대해서 모두 baseline 대비 성능이 좋았습니다. 또한 유저 경험 또한 향상됐음이 통계적으로 확인되었습니다. 하지만 sanding task의 presence 항목의 경우 electec이랑 비교했을때 유의미한 차이가 나지는 않았습니다. 선행 연구에서는 이렇게 연속적인 힘이 피드백으로써 가해질때 원하지 않는 방향으로 조작이 이루어지기 때문에 유저 경험이 하락하는 현상을 보고하였는데, 그의 일종일 수도 있다 추정하고 있습니다.

RQ3 → 부족한 점을 어떻게 해결할 수 있을까?

- 컴퓨터 계산에 따른 딜레이 발생
- 정보량이 과다하기 때문에 발생하는 집중력 저하
- EMS 자극으로 인해 생기는 저림 현상

이 세가지를 해결하기 위해 추후 연구에서는 더 효율적인 알고리즘을 적용하는게 좋겠다 언급하고 있습니다.

정보량 과다는 선행 연구에 의해서도 여럿 지적되어 왔던 사항으로, task 정보와 관련된 신호만 filtering 하는 방식으로 이를 해결할 수 있을 것 입니다.

마지막으로 EMS 이후에 저림이 발생하는 현상은 흔한 증상으로, EMS 시스템 자체의 한계이기 때문에 더 나은 장비를 사용하거나 이런 현상이 발생하지 않는 자성 근육 자극 시스템(magnetic muscle stimulation)을 사용하는게 좋겠습니다.

## Conclusion

결과적으로 물리 계산 기반 피드백 시스템을 만들고 힘에 의해 영향을 받는 teleoperation task를 만들어 평가해봤습니다. 그 결과 sanding task에서는 22% MAE reduction, drilling task에서는 30% accuracy improvement 를 달성할 수 있엇습니다. user 자가 보고도 향상되었습니다.

추후에는 팔 뿐만 아니라 어깨등 몸 전반적인 부분에 EMS를 부착하는 방안이 있고, 또 calibration session이 길기 때문에 이를 시뮬레이션 하여 계산하는 방안이 있을 것 입니다. 또 실제 vibration motor등과 비교하고 thermal perception등 다양한 피드백 방법론을 적용해 볼 수 있습니다.

뿐만 아니라 단순히 manipulator를 넘어서 robot dog, 잠수함, 수술 로봇등 다양한 곳에 활용될 수 있습니다. 어짜피 요구하는 것은 한 포인트에서의 힘이고 주된 contribution이 이 힘을 사람에게 전달하는데 있어서 그렇다 생각이 들었습니다.

마지막으로 EMS의 최신 연구 여러개와 동일 랩실의 연구 ErgoPulse까지 언급하면서 재밌게 이 논문의 contribution의 level을 설명하였습니다.
