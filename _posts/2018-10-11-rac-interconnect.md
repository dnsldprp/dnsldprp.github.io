---
layout: post
title: "Oracle 11g RAC 구성 및 Interconnect switch 설정"
description: "Interconnect switch 설정"
date: 2018-10-11
tags: [oracle, switch]
comments: true
share: true
---
### ORACLE 11g RAC 구성

***
![topology](/images/OracleRAC.png)
###### 백업, DR, IPMI 스위치는 제외

***

###### Interconnect 스위치 설정
- 두 스위치간 포트채널 구성 후 동일 VLAN으로 설정
- 서버 연결 포트에 portfast 설정 (설정하지 않으면 failover에 30초정도 딜레이 생길수있다)
- 스위치 로그 관리를 위해 syslog forwarding
- 서버 연결 포트에 점보프레임 설정 (MTU 9000)



