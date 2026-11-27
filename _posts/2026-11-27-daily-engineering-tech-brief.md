---
layout: post
read_time: true
show_date: true
title: "Daily Engineering Tech Brief: Nov 27, 2026"
date: 2026-11-27 06:00:00 +0900
description: "양자화학(VQE 계열), 신뢰성해석, 공학용 머신러닝, FE 대리모델 쪽에서 이번 주 눈에 띈 논문과 소식 정리."
img: posts/20260114/quantum-computing.jpg
tags: [daily brief, quantum computing, VQE, reliability analysis, Kriging, surrogate model, FEM, modal analysis]
categories: [daily brief]
author: Hoechang Ryu
toc: yes
---
<style>.header-page h1.page-title{font-size:2.4rem;line-height:1.25}@media only screen and (max-width:480px){.header-page h1.page-title{font-size:1.7rem}}</style>

## 들어가며 {#intro}

요즘 꾸준히 따라가고 있는 분야는 크게 네 가지다. VQE 계열 양자화학 알고리즘(특히 HI-VQE와 gPCE를 엮는 작업), UQ와 신뢰성 기반 설계최적화, 공학용 머신러닝, 그리고 FE 해석과 대리모델이다. 매일 쏟아지는 arXiv 논문과 발표 자료 중에서 내 연구(DD-GPCE, HI-VQE-gPCE, 기계 시스템의 모달·접촉 해석)와 맞닿는 것만 골라 짧게 메모해 두려고 한다.

이번 주는 HI-VQE 자체에 대한 새 논문은 없었다. 대신 부분공간을 잘라낸 뒤 놓친 상관에너지를 섭동으로 되살리는 논문이 하나 나왔는데, HI-VQE-gPCE 후처리를 고민하던 차라 반가웠다. 신뢰성해석 쪽에서는 능동학습 Kriging이 파손모드를 놓쳤을 때 <i>P<sub>f</sub></i>를 얼마나 과소평가하는지 보여주는 논문이 제일 기억에 남는다.

아래 논문은 모두 arXiv 프리프린트라 아직 심사를 거치지 않았다는 점은 감안해야 한다.

## 양자컴퓨팅 · 양자화학 {#quantum}

### SSPT: 잘라낸 부분공간의 상관에너지를 섭동으로 복원 {#sspt}

<small>Yao, Liu, Li · arXiv:2611.22108 · 2026.11.17 공개, 11.23 v2 · <a href="https://doi.org/10.48550/arXiv.2611.22108">DOI</a> · <a href="https://arxiv.org/abs/2611.22108">원문</a></small>

Contextual Subspace VQE(CS-VQE)는 안정자 사영으로 큐비트 수를 줄이는 대신, 남긴 부분공간 바깥 배치와의 해밀토니안 결합을 버린다. 저자들은 사영 과정에서 빠진 Pauli 항을 최적화된 기준상태에 대한 섭동으로 보고, 이 손실을 Stabilizer-Subspace Perturbation Theory(SSPT)로 되살린다. 보정 방식은 Epstein–Nesbet형 2차 보정과, 외부공간 안의 결합까지 유지하는 resolvent 방식 두 가지다. 기준상태가 인수분해된 형태라 행렬요소를 축소된 레지스터의 Pauli 기댓값만으로 구할 수 있고, 전체 파동함수를 다시 만들 필요가 없다는 점이 핵심이다. 저자들은 여러 분자에서 사영 오차가 크게 줄고, 일부는 더 적은 큐비트로 화학적 정밀도에 도달했다고 보고한다.

익숙한 Epstein–Nesbet 형태로 쓰면 대략 이런 모양이다(논문의 정확한 식은 아니다).

<p style="text-align:center">\[ E^{(2)} = \sum_{k} \frac{\lvert \langle k \rvert V \lvert \psi_0 \rangle \rvert^{2}}{E_0 - \langle k \rvert H \lvert k \rangle} \]</p>

여기서 <i>V</i>는 사영으로 제거된 항이다. HI-VQE는 selected-CI처럼 중요한 배치로 핵심공간을 만들고 고전적으로 대각화한다. SCI에서는 핵심공간 밖의 결합을 PT2로 보정하는 것이 거의 표준이니, HI-VQE-gPCE에서 부분공간 대각화 뒤에 붙일 후처리로 비슷한 보정을 시험해 볼 만하다. 결국 따져봐야 할 것은 측정 비용이다. SSPT가 요구하는 Pauli 기댓값 개수와 HI-VQE의 샘플링 비용을 같은 분자에서 비교해 보고 싶다.

앞으로는 벤치마크 분자와 기저, 분모가 작아질 때 resolvent 방식이 얼마나 안정적인지, 측정 비용이 어떻게 스케일링되는지를 보려 한다.

<small>중요도 중간 · 연구 관련성 높음 (부분공간 선택 이후 고전 후처리와 직접 연결)</small>

### 제1양자화 화학 시뮬레이션: sum-of-squares 표현 {#first-quantised}

<small>Ivanov, Sünderhauf, Kunjummen, Blunt, Izsák · arXiv:2611.24885 · 2026.11.18 공개 · <a href="https://doi.org/10.48550/arXiv.2611.24885">DOI</a> · <a href="https://arxiv.org/abs/2611.24885">원문</a></small>

일반 오비탈 기저에서 쓸 수 있는 제1양자화 위상추정 알고리즘이다. 비볼록 sum-of-squares 해밀토니안 표현과 spectral-gap amplification을 쓰고, 큐비트 수가 오비탈 수에 대해 로그로만 늘어난다. 저자들의 자원 추정에 따르면, 전자 4–24개에 오비탈이 전자 수의 5–14배인 분자 6종에서 논리큐비트가 수백 개 수준일 때 Toffoli 게이트가 제2양자화 방식보다 최대 16배 적다. 이 우위는 약 1,400 큐비트까지 이어지고, 최소 큐비트 수도 1.4–2배 낮다. 하드웨어 실험이 아니라 논리 수준의 추정치다.

결함허용 양자컴퓨터를 전제로 한 방법이라 지금 하는 VQE 작업과는 거리가 있다. 다만 하이브리드 부분공간 방법이 장기적으로 어떤 기준선과 비교될지 감을 잡는 데는 도움이 된다.

<small>중요도 중간 · 연구 관련성 낮음</small>

### IBM · ORNL · AMD 양자오류정정 공동설계 플랫폼 {#qec-platform}

<small>보도자료 · 2026.11.05 발표 · <a href="https://hpcwire.com/off-the-wire/ornl-ibm-and-amd-launch-new-co-design-platform-for-quantum-error-correction">HPCwire 게재본</a></small>

IBM Quantum Nighthawk(120 큐비트)를 ORNL의 Umbra(AMD Instinct MI355X GPU, EPYC CPU)와 묶은 QEC 연구 플랫폼이다. 연접 부호, 시공간 부호, 알고리즘 인지형 디코더를 다룰 계획이라고 한다. 보도자료에 나온 70 논리큐비트·438 연산은 IBM이 기존에 보고한 실험치이고, 이 플랫폼에서 나온 결과는 아니다. 100 논리큐비트로 100,000 연산을 돌리는 것은 2028년까지의 DOE 목표치다.

인프라 소식이라 당장 연구에 쓸 내용은 없다. 디코더 지연시간 같은 실제 수치가 나오면 다시 볼 생각이다.

<small>중요도 중간 · 연구 관련성 낮음</small>

## UQ · 신뢰성 · 설계최적화 {#uq}

### 능동학습 Kriging이 파손모드를 놓칠 때 {#ak-inner-optimizer}

<small>W.-C. Yeh · arXiv:2611.03117 · 2026.11.03 공개 · <a href="https://doi.org/10.48550/arXiv.2611.03117">DOI</a> · <a href="https://arxiv.org/abs/2611.03117">원문</a></small>

AK-MCS 계열에서 다음 샘플을 고를 때 후보풀 대신 연속 최적화기를 쓰는 경우가 많다. 이 논문은 대리모델, 모집단, 종료 규칙을 모두 고정하고, 예산을 맞춘 연속 최적화기 5종(스웜과 L-BFGS-B를 결합한 하이브리드 포함)을 후보풀 방식과 비교했다. 문제는 2–100차원 11종이다. 사전에 세운 가설은 지지되지 않았다. 후보풀 방식과 비교한 44번에서 하이브리드는 한 번도 이기지 못했다(0승 23무 21패).

더 눈에 띄는 결과는 따로 있다. 직렬 시스템 3종에서 multistart L-BFGS-B는 60번 중 59번 파손모드 하나를 끝내 학습하지 못했고, 미학습 모드가 있었던 198번 전부에서 파손확률이 과소추정됐다. 저자는 이 부분을 탐색적 결과라고 표시했다. 오차 기반 종료 기준이 참값의 0.38–0.89배인 추정치를 17번이나 그대로 통과시켰다는 점도 불편하다.

U 학습함수를 떠올리면 왜 그런지 이해가 된다.

<p style="text-align:center">\[ U(\mathbf{x}) = \frac{\lvert \mu_G(\mathbf{x}) \rvert}{\sigma_G(\mathbf{x})} \]</p>

이 함수의 최소점 탐색이 떨어져 있는 파손영역에 한 번도 닿지 못하면, 대리모델은 그 영역을 안전하다고 확신한다. 그러면 <i>P<sub>f</sub></i>가 비보수적으로 나오는데도 대리모델의 오차지표로는 이를 알아챌 방법이 없다. 제약이 여러 개인 RBDO나, 대리모델로 VaR/CVaR 같은 꼬리 지표를 계산할 때도 똑같이 걸리는 문제다. 내가 쓰는 PCE/DD-GPCE 기반 능동학습에도 비슷한 맹점이 있는지 커버리지를 따로 점검해 봐야겠다.

<small>중요도 중간 (단독 저자, 일부 탐색적 결과) · 연구 관련성 높음</small>

DD-GPCE, score-form과 Stein-form 민감도, VaR/CVaR 민감도 쪽에서는 이번 주에 새로 챙길 만한 논문을 찾지 못했다.

## 공학용 머신러닝 {#ml}

### 구조동역학용 물리내장 신경연산자 (Modal Rational) {#modal-rational}

<small>Nasrollahi, Orak · arXiv:2611.07059 · 2026.11.06 공개, 11.09 v2 · <a href="https://doi.org/10.48550/arXiv.2611.07059">DOI</a> · <a href="https://arxiv.org/abs/2611.07059">원문</a></small>

구조 특징에서 응답을 바로 맞추는 대신 모달량(고유진동수, 감쇠 등)을 예측하고, 주파수응답은 해석적으로 다시 조립하는 방식이다(Modal Rational, MR). 여기에 PGMON과 MOSAIC가 진동수, 감쇠, 잔류유연도에 범위를 제한한 보정을 더한다. 80 m 강재 트러스 타워와 테이퍼 쉘 보에 대해 각각 20,000개 설계 집단을 만들었고, 평균 변위이력 오차는 타워 0.73–0.76%, 보 0.029–0.116%였다. 다만 비교 기준이 풀 FE 해가 아니라 대역제한 모달 해다.

결국 이 대리모델은 아래 모달 합 구조를 그대로 유지한다.

<p style="text-align:center">\[ H(\omega) = \sum_{j} \frac{\boldsymbol{\phi}_j \boldsymbol{\phi}_j^{T}}{\omega_j^{2} - \omega^{2} + 2 i \zeta_j \omega_j \omega} \]</p>

설계가 바뀌어 공진점이 이동해도 고유쌍을 통해 따라가니, 응답곡선을 통째로 학습하는 것보다 물리적으로 납득이 간다. 저자들도 위상(topology)이 고정된 경우만 다뤘고 일반화나 계산 절감은 아직 보여주지 못했다고 쓰고 있다. 고유진동수에 대한 PCE/GP 대리모델과 비교해 보거나, 모드 순서가 바뀌는 경우에 어떻게 되는지 보고 싶다.

<small>중요도 낮음–중간 · 연구 관련성 중간–높음 (모달·고유치 대리모델)</small>

## 공학 시뮬레이션 · CAE {#cae}

### FE 응력 대리모델은 에너지 노름으로 학습하자 {#energy-norm}

<small>Cao, Song · arXiv:2611.14574 · 2026.11.11 공개 · <a href="https://doi.org/10.48550/arXiv.2611.14574">DOI</a> · <a href="https://arxiv.org/abs/2611.14574">원문</a></small>

구멍 뚫린 판과 공동이 있는 고체의 선형 탄성 문제에서, FE 대리모델의 손실을 변위 L2가 아니라 에너지 노름으로 바꾸기만 했는데 결과가 크게 달라졌다. 3D 트랜스포머의 중앙값 에너지노름 오차는 7.5배, von Mises 오차는 6.1배 줄었고, 2D에서도 von Mises 오차가 절반 이하로 떨어졌다. 강성행렬을 쓸 수 있으면 Ritz 사영이 최대응력을 뺀 모든 지표에서 라벨 없는 모델보다 나았고, 훨씬 조밀한 메쉬로는 어떤 네트워크도 잘 전이되지 않았다.

식으로 보면 당연한 결과이기도 하다. <i>K</i><b>u</b> = <b>f</b>, 포텐셜 에너지를 Π(<b>v</b>) = ½ <b>v</b><sup>T</sup><i>K</i><b>v</b> − <b>f</b><sup>T</sup><b>v</b>라 두면

<p style="text-align:center">\[ \Pi(\hat{\mathbf{u}}) - \Pi(\mathbf{u}) = \tfrac{1}{2} (\hat{\mathbf{u}} - \mathbf{u})^{T} K (\hat{\mathbf{u}} - \mathbf{u}) = \tfrac{1}{2} \sum_{i} \lambda_i c_i^{2} \]</p>

이고, 여기서 <i>c<sub>i</sub></i>는 오차를 고유모드로 전개한 계수다. 변위 L2 손실은 Σ<i>c<sub>i</sub></i><sup>2</sup>라서, 변형에너지를 대부분 담고 있는 강성 모드(큰 λ<sub>i</sub>)를 상대적으로 가볍게 본다. 응력 정확도가 곧 파손확률의 정확도로 이어지니, 대리모델 기반 신뢰성해석을 할 때 손실을 무엇으로 잡을지는 꼭 짚고 넘어가야 할 문제다. 접촉 문제는 변분부등식이라 그대로 확장되지는 않을 것 같은데, 이 부분은 직접 시험해 봐야 알 것 같다.

<small>중요도 중간 · 연구 관련성 높음 (FE 이산화와 대리모델 오차)</small>

CAE 소프트웨어 쪽은 최근 2주간 굵직한 발표가 없었다. Simcenter 2026 여름 릴리스(7월)와 Ansys 2026 R1(3월) 이후로는 조용한 편이다.

## 다음 주에 볼 것 {#next}

<ul>
<li>SSPT 본문의 측정 비용 분석. HI-VQE 샘플링 비용과 같은 분자로 비교할 수 있을지 확인</li>
<li>Kriging 미학습 파손모드 문제를 PCE 기반 능동학습에서도 재현할 수 있는지</li>
<li>에너지 노름 손실이 비선형·접촉 문제까지 이어지는지에 대한 후속 연구</li>
</ul>
