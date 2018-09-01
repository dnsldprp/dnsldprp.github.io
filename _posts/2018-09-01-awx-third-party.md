---
layout: post
title: "Ansible AWX에서 third-party credential storage 사용하는법"
description: "Vault를 활용해 암호를 저장하고 playbook 실행"
date: 2018-08-08
tags: [vault, ansible, awx]
comments: true
share: true
---
### ansible lookup plugin 활용해 동시에 여러 서버의 암호 가져와 실행시킬 수 있다

***

#### vault 서버 구성해 서버별 ID, password 저장
> docker 사용시 awx_web, awx_task container 내부에 hvac 패키지 설치 해야 함

#### ansible awx inventory탭의 extra variables에 다음과 같이 플러그인 사용

~~~
{% raw %}
ansible_user : {{ lookup('hashi_vault', secret=secret/{{ inventory_hostname }}:username)}}
ansible_ssh_pass : {{ lookup('hashi_vault', secret=secret/{{ inventory_hostname }}:password)}} 
{% endraw %}
~~~

> **ansible_hostname** : ansible fact 수집할 때 서버의 호스트네임 읽어온 값

> **inventory_hostname** : inventory에 써있는 호스트 네임 값 ( container의 /etc/hosts 에 host 등록하면 IP주소가 아닌 hostname으로 사용가능)

#### ansible awx credential 탭에 custom credential 생성

Input : 

~~~
fields:
  - type: string
    id: vault_server
    label: URL
  - type: string
    id: vault_token
    label: Token
    secret: true
required:
  - vault_server
  - vault_token 
~~~

Injector :
 
~~~
{% raw %}
env:
  VAULT_ADDR: '{{ vault_server }}'
  VAULT_TOKEN: '{{ vault_token }}'
{% endraw %}
~~~
 
#### 마지막으로, template 탭에서 inventory, credential 적용 후 스크립트 수행


