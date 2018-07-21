---
title: "[기계의 학습법] 학습과 기계 학습의 관계"
strapline: "About machine and learning"
description: "일반적인 학습과 기계 학습 사이의 관계에 대한 이야기"
header:
 overlay_image: /assets/images/triangular.jpeg
categories:
  - "Machine Learning"
tag:
  - "머신러닝"
  - "기계학습"
toc: true
last_modified_at: 2018-07-11
comments: true
mathjax: true
---



 기계 학습은 주어진 상황 $x$에 대한 답을 제공하는 함수 $f(x)$를 인위적으로 설정하지 않고 기계가 스스로 데이터를 통해 학습하도록 하는 프레임워크이다. 여기서 함수는 $w$라는 파라미터를 통해 결정되므로 $f(x)$를 학습한다는 것은 $w$를 학습한다는 것과 동일하다. 그리고 학습한 $w$를 통해 $f(x)$는 정답을 제공해야한다.