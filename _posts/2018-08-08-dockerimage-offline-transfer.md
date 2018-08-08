---
layout: post
title: "도커 이미지 오프라인 전송"
description: "외부 인터넷이 안되는 환경에서 도커 이미지 옮기는법"
date: 2018-08-08
tags: [docker]
comments: true
share: true
---

## 오프라인 환경이라 도커 이미지를 가져오기 힘들떄
## 이미지를 압축 파일 형태로 저장하고 전송 후 불러올 수 있다.

***

### 순서

1. 도커 이미지 확인
~~~
docker images
~~~

2. 압축 파일로 이미지 저장
~~~
docker save [IMAGE ID] | gzip > [파일명].tar.gz
~~~

3. 오프라인 환경으로 파일 전송 후 이미지 불러오기
~~~
zcat [파일명].tar.gz | docker load
~~~


