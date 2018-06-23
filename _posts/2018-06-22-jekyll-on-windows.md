---
title: "[Jekyll] 윈도우에서 지킬 설치 및 블로그 생성하기"
strapline: "Publishing a blog on a local machine via Jekyll on windows"
description: "윈도우에서 지킬 설치 및 블로그 생성"
header:
 overlay_image: /assets/images/triangular.jpeg
categories:
  - "Jekyll"
tag:
  - "지킬"
  - "깃허브"
  - "블로그"
toc: true
last_modified_at: 2018-06-22
comments: true
---

지킬 기반 블로그를 운영하면서 블로그 구성 변경이나 기능 추가를 할 때, 깃허브 상에서 바로 수정하지 않고 로컬머신에서 먼저 적용해보고 미리보기를 수행 할 수 있다. 리눅스나 맥에서는 지킬을 통한 블로그 생성을 상대적으로 쉽게 할 수 있다고 한다. 윈도우 상에서 이를 수행기위해서는 일련의 루비와 지킬 설치 과정을 거쳐야 한다.

## 1. 루비 (Ruby) 설치

[루비 인스톨러 for windows](https://rubyinstaller.org/downloads/)의 다운로드 페이지에서 윈도우용 루비 + 개발자킷(DevKit) 설치 프로그램을 다운로드  후 설치한다. 루비가 먼저 설치되고 (PATH 설정 포함) 이어서 개발자 킷 설치 과정이 이루어진다.

|{% raw %}![alt](/assets/images/Ruby_windows.jpg){% endraw %}|
|*루비 인스톨러 홈페이지에서 Ruby+Devkit 2.4.4-1을 다운로드한다.*|



## 2. 지킬 (Jekyll) 설치하기

루비가 설치가 정상적으로 완료되면, 이제 지킬을 설치해야한다. 먼저 윈도우 검색창에서 Ruby를 검색 후 Start Command Prompt with Ruby를 실행한다.

|{% raw %}![alt](/assets/images/ruby_command.jpg){% endraw %}|
|*윈도우 검색창에서 Ruby 검색 후 루비 콘솔을 실행한다.*|

콘솔창에서 `gem` 명령어를 통해 지킬과 실행에 필요한 패키지들을 설치한다.
```
gem install jekyll
gem install minima
gem install bundler
gem install jekyll-feed
gem install tzinfo-data
```
관련 패키지들이 정상적으로 설치되면, 이제 로컬 머신에서 지킬을 통해 블로그 미리보기를 할 수 있다.

## 3. 로컬에서 블로그 생성하기

먼저 루비 콘솔창에서 이전 포스팅에서 생성한 블로그의 깃허브 저장소와 연동된 폴더로 이동한다. 윈도우 상에서는 인코딩 문제가 있을 수 있는데, 다음의 코드로 블로그 생성이 가능하다.
```
# 블로그 저장 폴더로 이동한다.
# 인코딩 에러 발생시 다음의 코드를 실행한다.
chcp 65001
# 지킬을 실행한다.
jekyll serve
```
코드 실행 결과는 다음과 같다.

|{% raw %}![alt](/assets/images/ruby_command_2.png){% endraw %}|
|*블로그 저장 폴더 내에서 명령어 수행 결과*|

브라우저를 열어 `http://127.0.0.1:4000/`로 접속하면 로컬 상에서 블로그가 구현된 결과를 살펴 볼 수 있다.

## 4. 요약

깃허브에 블로그 수정 내용을 반영하기 전에 로컬 머신 상에서 미리 돌려볼 수 있다. 그리고 정상적으로 동작하는 경우에 git을 통해 변경 내용을 업로드하면 웹상에서도 바로 수정사항을 반영 할 수 있다. 일반적인 과정은 다음으로 요약 할 수 있다.

1. 루비 (Ruby)를 설치한다.
2. 지킬 (Jekyll)을 설치한다.
3. 깃허브 블로그 폴더를 복사.
4. 해당 폴더에서 수정 진행.
5. 해당 폴더에서 지킬을 실행, 로컬호스트로 접속 후 수정 결과 확인.
6. 수정 결과 반영하고 싶다면 Git을 통해 업로드.

설치에 대한 조금 더 자세한 내용은 [지킬 한국어 페이지](https://jekyllrb-ko.github.io/)를 참고한다.
