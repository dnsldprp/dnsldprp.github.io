---
layout: post
title: "GraphQL http request 기본 "
description: "http로 graphql request 하는 방법"
date: 2019-11-07
tags: [graphql]
comments: true
share: true
---

#### GraphQL ?
- 단일 엔드포인트로 백엔드 부담이 줄어듬, serverless와 잘 어울리는듯함
- [공식 가이드](https://graphql-kr.github.io/learn/)

#### Schema 
***
~~~
type User {
	id: ID!
	name: String
}

type Query {
	getUser(id: ID!): User
}

type Mutation {
	createUser(input: CreateUserInput!): User
}

input CreateUserInput {
	name: String
}
~~~

- 뮤테이션은 데이터 조작이 일어날 때 사용, resolver를 등록해서 원하는 행동을 할 수 있음

***

#### GET - ID를 이용한 유저 조회
~~~
endpoint : https://my-url/graphql
param
  query : {getUser(id:"ca02ab35-b8e6-438a-9d7f-456b12ff46d6"){name}}

url : https://my-url/graphql?query={getUser(id:"ca02ab35-b8e6-438a-9d7f-456b12ff46d6"){name}}
~~~

###### graphql query
~~~
{
  getUser(id:"ca02ab35-b8e6-438a-9d7f-456b12ff46d6")
  {
    name
  }
}
~~~

###### response
~~~
{
    "data": {
        "getMyModelType": {
            "name": "Hello, world!"
        }
    }
}
~~~

*** 

#### POST - 유저 생성
~~~
endpoint : https://my-url/graphql
header : {Content-Type:application/json}
body : 
{
	"query":"mutation myMutation ($user:CreateUserInput!) { createUser(input:$user) { id name }}",
	"variables":{
		"user":{
			"name":"dnsldprp"
		}
	}
}
~~~

- body 에 query에는 graphql query string을, variables에는 query variables 입력

###### graphql query
~~~
mutation myMutation($user:CreateUserInput!){
  createUser(input:$user){
    id
    name
  }
}

Query variables
{
  "user": {
    "title": "Hello, world!!!!"
  }
}
~~~

###### response
~~~
{
    "data": {
        "createUser": {
            "id": "7ac55b71-2c17-4a6b-8c2f-18fd5a90e166",
            "title": "Hello, world!!!!"
        }
    }
}
~~~


