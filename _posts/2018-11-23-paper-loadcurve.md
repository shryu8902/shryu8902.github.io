---
title: "[논문 정리] Load curve data cleansing and imputation"
strapline: "About machine and learning"
description: "스마트 미터 데이터 클린징 방법"
header:
 overlay_image: /assets/images/triangular.jpeg
categories:
  - "paper"
tag:
  - "load data"
  - "data cleansing"
toc: true
last_modified_at: 2018-12-03
comments: true
mathjax: true
---

# Load curve data cleansing and imputation via sparsity and low rank 

2013 / IEEE TSG / Gonzalo Mateos, Georgios B. Giannakis

## 1. Introduction

Load curve data는 스마트 미터에서 주기적으로 수집되는 전력 사용량 데이터를 의미.
또한 정확한 load curve는 스마트 그리드 시스템 운영과 예측 등에서 매우 중요한 역할을 수행.
하지만 다음의 세가지 이유로 인해 부정확한 load curve data가 발생할 수 있음.

- PMU-instrumented buses are few
- SCADA data become available at a considerably smaller time scale than PMU data
- Regional operators are not willing to share all their variables.

이외에도 outlier와 같은 bad data들의 존재 (e.g., meter failures, strikes, unscheduled generator shutdowns, extreme weather condition, malicious cyber attack)는 load curve data 활용에 걸림돌이 됨.

따라서 missing data imputation과 robust load curve estimation이 필수적.
본 논문에서는 principal components pursuit (PCP)를 통해 spatial correlation, outlier sparsity를 고려한 load curve estimation을 수행. 특히 distributed PCP (DPCP)를 제안하여, 각 스마트미터에서 분산하여 estimation 수행.

- 요약하면 인접한 스마트미터끼리 메세지를 주고 받으면서, DPCP를 통해 cleansed load curve의 estimation을 수행.

## 2. Modeling and problem statement
### 2.A. Spatiotemporal load curve data model

- $\mathbf{y}(t):=[y_{1,t},...,y_{N,t}]'$ : $N$개의 네트워크 노드에서 모니터링된 discrete time instance $t\in[1,T]$
- $\mathbf{Y} := [\mathbf{y}(1),...,\mathbf{y}(T)]$ : $N\times T$ matrix. $N$개의 load curve matrix.
- $\Omega\subseteq\{1,...,N\}\times \{1,...,T\}$ : Set of index pair $(n,t)$.
- $\mathcal{P}_\Omega(\ \cdot\ )$ : matrix sampling operator, $\Omega$에 포함되지 않은 index pair $(n,t)$의 값들을 0으로 매핑, 나머지는 그대로 유지.
- 결과적으로 incomplete한 Spatiotemporal load curve data는 다음과 같이 모델링 할 수 있음. $$\mathcal{P}_\Omega(\mathbf{Y})=\mathcal{P}_\Omega(\mathbf{X}+\mathbf{O}+\mathbf{E})$$
- 여기서 $\mathbf{X},\mathbf{O},\mathbf{E}$는 각각 nominal load profile, outlier, error를 의미. 따라서 nominal observation $y_{n,t}=x_{n,t}+e_{n,t}$가 된다. ($x$는 **nominal load profile** 이고 $y$는 **nominal observation** 임에 유의)

위의 모델은 inherently underdetermined인데 observation $\mathbf{Y}$로 부터 unknown $\mathbf{X,O}$를 estimation 해야하기 때문. 이를 극복하기위해 load profile과 outlier가 가지고있는 두가지 속성을 이용함.
1. Low-rank property of $\mathbf{X}$ : 전력 사용 패턴의 주기성과 수용가에 따른 공통된 패턴적 특징으로 인해 $\mathbf{X}$는 linearly dependent하고 따라서 $\mathbf{X}$은 low rank를 갖음.
2. Sparsity of $\mathbf{O}$ : Outlier들은 간헐적으로 발생하기때문에 $\mathbf{O}$는 sparse matrix임.

[7]에서는 low-rank 속성을 바탕으로 data cleansing을 수행하지만 row-wise manner로 동작. 반면에 본 논문에서는 matrix $\mathbf{X}$에 기반하므로 spatial dependency까지 고려한다는 장점이 있음.

### 2.B. Communication network model

- 각 스마트 미터/AMI는 간단한 local computation이 가능하다고 가정.
- 복잡한 커뮤니케이션은 불가능하지만 single-hop communication 가능.
- AMI 네트워크는 undirected graph $G(\mathcal{N},\mathcal{L})$로 정의.
- Node $n\in\mathcal{N}$은 single-hop neigboring peers ($\mathcal{J}_n$)과 통신 가능.
- 각 노드는 적어도 1개 이상의 neighborhood를 가지고 있으며 (no isolation), 비록 single-hop communication만 허용하나 최종적으로 각 노드는 전체 데이터 $\mathbf{Y}$를 확보 할 수 있음.

### 2.C. Load curve cleansing and imputation

Load curve cleansing과 imputation은 목적은 다음과 같음.
1. Outlier 판독과 제거
2. Nominal load matrix $\mathbf{X}$의 missing value 수정
3. Denoising
즉 incomplete, noisy, outlier-contaminated Spatiotemporal load data인 $\mathcal{P}_\Omega(\mathbf{Y})$로부터 nominal load profiles $\mathbf{X}$와 outliers $\mathbf{O}$를 estimation해야함. 결과적으로 본 문제는 estimation-interpolation-detection이 결합된 문제이며, load forecasting과는 다른 문제임에 유의해야함.

## 3. Principal components pursuit

Principal components pursuit (PCP)를 통해 효과적으로 $\mathbf{X,O}$의 estimation이 가능.

![pcpest1](/assets/images/pcpest1.png)

- ${\|\mathbf{O}\|}_1 := \sum_{n,t} |o\_{n,t}|$,
- ${\|\mathbf{X}\|}_* := \sum_i\sigma_i(\mathbf{X})$, $\sigma_i()$는 $i$-th singular value를 의미.

------------------------------------------------------------

수식에 대한 intuitive 한 해석은 아래와 같음.
1. reconstruction error 최소화
2. low rank $\mathbf{X}$에 대한 incentive
3. sparse $\mathbf{O}$에 대한 incentive

------------------------------------------------------------

(P1)은 batch estimation을 가능하게 하고 (set $\Omega$에 대해 정의되어 있기때문) 나아가 분산 최적화를 수행하는 기반이 됨.
커뮤니케이션 오버헤드, 개인 보안 이슈, 커뮤니케이션 에러, robustness 등의 측면에서 중앙 최적화로 (P1)을 푸는 것보다 분산으로 해결하는 것이 도움이 됨.
따라서 본 논문에서는 분산 버전의 PCP (DPCP)를 제안함.

## 4. Distributed cleansing and imputation

DPCP 알고리즘은 (P1) 문제를 스마트 미터 네트워크를 통해 계산.
각 노드 (스마트미터)는 매 iteration $k=1,2,3,...$을 통해 인접한 스마트미터와 커뮤니케이션하면서 간단한 local optimization task를 푼다.
최종 목표는 분산최적화를 통해 얻은 local estimation $\mathbf{x}_n[k]$와 $\mathbf{o}_n[k]$가 $k\rightarrow\infty$ 상황에서 중앙최적화로 얻은 $\mathbf{\hat{X},\hat{O}}$의 $n$-th row와 동일해야함.

각 노드의 제한적인 메모리와 계산 복잡도를 고려하기 위해 $rank(\mathbf{\hat{X}})$의 upper bound $\rho$를 설정 (PCA에서 복원에 사용할 maximum number of principal components와 유사)
$rank(\mathbf{\hat{X}})\leq\rho$이므로 (P1)을 통해 $\mathbf{X}=\mathbf{P,Q}'$로 factorize 가능. $\mathbf{P,Q}$는 각각 $N\times\rho,T\times\rho$ matrix임 (Recall PCA).
$\mathbf{P,Q}'$를 통해 (P1)은 아래와 같은 최적화 문제로 변환 가능
($\mathbf{p}_n$은 $\mathbf{P}$의 $n$-th row vector.)

![pcpest2](/assets/images/pcpest2.png)

최적화 문제 (P2)로 reformulation하게되면 estimation 해야하는 variable의 수가 $2NT$ (P1)에서 $\rho(N+T)+NT$로 감소함. (P2)에 남아있는 $NT$ term (dominant)은 $\mathbf{O}$에 의한 것인데, sparse matrix이기때문에 $N,T$의 크기가 크더라도 효과적으로 다룰 수 있음.
(P2)로 변환된 문제는 여전히 분산최적화에 적합하지않은데 그 이유로

- c1) non-seperable nuclear norm in (P2), i.e.,$\mathbf{P}$
- c2) global variable $\mathbf{Q}$ coupling the per-node summands

### 4.A. A seperable low-rank regularization

c1)을 해결하기위해 nuclear norm ${\|\mathbf{X}\|}_*$의 alternative form을 사용.
![nucnorm](/assets/images/nucnorm.png)

따라서 (P2)는 다음의 (P3)로 변환되고, $\mathbf{p}_n, \mathbf{q}_n$으로 분리해서 풀더라도 ${rank}(\mathbf{\hat{X}})\leq\rho$일 때 (P1) 최적화 결과와 동일하다.

![pcpest3](/assets/images/pcpest3.png)

### 4.B. Local variables and consensus constraints

(P3)로 변환하더라도 여전히 global varialbe $\mathbf{Q}$가 남아있으므로, 이를 해결하기 위해 $\mathbf{Q}$의 local estimation인 $\{\mathbf{Q}_n\}^N_n=1$을 사용함.

![pcpest4](/assets/images/pcpest4.png)

(Recall that $\mathcal{J}_n$ is neigborhood of smart meter $n$)
DPCP 알고리즘을 최종적으로 유도하기위해 $\mathbf{Q}$에 대한 제약조건을 reparametrize함.

![pcp](/assets/images/pcp.png)

### 4.C. The D-PCP algorithm

제약 조건이 달려있는 (P4) 문제를 Lagrange multiplier $\bar{\mathbf{M}}^m_n,\tilde{\mathbf{M}}^m_n$을 통해 quadratically augmented Lagrangian function을 얻을 수 있음.

![pcp3](/assets/images/pcp3.png)

ADMM으로 (P4)에 대한 분산최적화, 매 iteration마다 다음의 단계들을 수행.

![pcp4](/assets/images/pcp4.png)

## 요약
_______________________________________________

위의 스텝들은 intuitive하게 보면 순차적으로 커뮤니케이션을 통한 네트워크 전체 노드에서 $\mathbf{Q}$의 수렴과 각 노드에서 local optimization을 수행하는 것임.
논문에서 전체 알고리즘을 다시 정리했는데, 이 과정에서 $\mathbf{F}$들이 사라지고 따라서 이웃 노드들의 $\mathbf{Q}$에 대한 estimation 정보를 유지할 필요없음.

최종적으로 알고리즘이 수렴하면,  분산 최적 결과는 모든 노드에서 동일하고, 또한 
$\| \mathcal{P}_\Omega(\mathbf{Y}-$ $\bar{\mathbf{P}}\bar{\mathbf{Q}}\_{n'}-\bar{\mathbf{O}}\| < \lambda\_*$ 이면 central optimization 결과와 distributed optimization 결과가 동일함.
