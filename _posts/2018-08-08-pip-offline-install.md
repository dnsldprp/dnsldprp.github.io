---
layout: post
title: "pip 패키지 다운 받아서 설치하는법"
description: "인터넷이 안되는 환경에서 pip 패키지설치"
date: 2018-08-08
tags: [pip, python]
comments: true
share: true
---
## pip 패키지 오프라인으로 인스톨 하는법

**인터넷이 안되는 환경에서 파이썬 패키지를 활용해야 할때**

***

## 순서
- 온라인 환경에서 OS 버전, 파이썬 버전 고려해서 패키지 다운로드
~~~ 
pip install some-packages 
~~~

- 설치된 패키지 목록 저장
~~~
pip freeze > packages.txt
~~~

> 필요없는 패키지들은 삭제 하면 됨

- 패키지 파일 다운로드
~~~ 
pip download -r ./packages.txt
~~~

- 파일 압축
~~~ 
tar -zcvf packages.tar.gz ./*
~~~

- 설치할 곳으로 압축파일 옮긴 후 압축 풀기
~~~ 
tar -zxvf package.tar.gz
~~~

- 설치
~~~
pip install --no-index --find-links="./" -r ./packages.txt
~~~

> 패키지들 설치하는 과정에서 알 수 없는 에러 발생하면 에러 패키지를 제외하고 하나씩 설치
~~~
pip install --no-index --find-links="./" --no-dependencies requests-2.19.1-py2.py3-none-any.whl
~~~

 
