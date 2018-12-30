---
title: "[Machine Learning] 윈도우 10 텐서플로우 (GPU) 설치하기"
strapline: "Tensorflow-GPU installation for Windows 10"
description: "윈도우 10에서 텐서플로우 GPU 설치하기"
header:
 overlay_image: /assets/images/triangular.jpeg
categories:
  - "Machine Learning"
tag:
  - "machine learning"
  - "tensorflow"
toc: true
last_modified_at: 2018-12-30
comments: true
mathjax: true
---

# Tensorflow-GPU installation for Windows 10

최대한 간단하게 정리한 윈도우 10에서 텐서플로우 GPU 설치하기. (2018년 12월 기준)

- GPU가 필요없다면 바로 1번으로...

### 0. GPU support

GPU를 활용하기 위해서는 먼저 다음을 필요로 한다.

- CUDA 지원 GPU 카드
- NVIDIA GPU 드라이버 384.x 이상 (for CUDA 9.0)
- CUDA Toolkit (현재 CUDA 9.0 지원)
- cuDNN SDK (7.2 이상)

GPU와 드라이버는 미리 설치해두고, 다음을 진행한다.

1. 아카이브에서 CUDA toolkit 9.0 다운로드 후 설치 (Base installer, patch 1/2/3/4)
[`https://developer.nvidia.com/cuda-90-download-archive`](https://developer.nvidia.com/cuda-90-download-archive)
<br>
2. 환경 변수에서 CUDA_PATH 확인  
(기본 `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0`)<br><br>  

3. CUDA toolkit 버전에 맞는 cuDNN 라이브러리 다운로드 및 압축풀기 (v7.4.2 for CUDA 9.0)  
[`https://developer.nvidia.com/rdp/cudnn-download`](https://developer.nvidia.com/rdp/cudnn-download)
<br>
4. 압축 해제 3개의 파일을 앞선 CUDA_PATH 경로 내의 동일한 폴더로 이동

### 1. Anaconda를 통한 텐서플로우 설치

1. 최신 버전의 아나콘다 다운로드 후 설치 (Python 3.7 version)
[`https://www.anaconda.com/download/#windows`](https://www.anaconda.com/download/#windows)
<br>
2. Anaconda prompt 실행 후 텐서플로우 구동을 위한 가상 환경 생성 (Python 3.6 버전 지정 필수)  
```
conda create -n 환경이름 pip python=3.6
```

3. 추가적으로 설치하게될 패키지 목록들이 뜨고 `y` 눌러 진행
<br>
4. 생성 완료 후 가상 환경 실행  
```
conda activate 환경이름
```  
가상 환경이 실행되면 `(base) C:\~ `가 `(환경이름) C:\~`로 변경된다.
<br>
5. 다음의 코드를 차례로 실행 (CPU 버전 설치 시 -gpu를 빼고 실행)
```
pip install --upgrade pip
pip install --upgrade tensorflow-gpu
```

6. 설치가 끝나면 verification을 위해 다음의 코드를 실행해본다.  
```
python -c "import tensorflow as tf; tf.enable_eager_execution();  print(tf.reduce_sum(tf.random_normal([1000, 1000])))"
```

7. 설치가 정상적으로 완료되었다면 GPU에 대한 정보가 주르륵 출력되고 최종적으로 다음과 같은 결과가 화면에 나타난다.  
```
tf.Tensor(숫자, shape=(), dtype=float32)
```
8. 가상 환경 종료.  
```
conda deactivate
```
