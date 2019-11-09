---
layout: post
title: "AWS Lambda - S3 PreSignedURL 사용해서 파일 업로드"
description: "S3 PreSignedURL with upload file"
date: 2019-11-09
tags: [aws lambda, aws s3, python]
comments: true
share: true
---

***
#### PreSignedURL ?? 
1. 클라이언트 -> 서버로 이미지를 업로드할 수 있는 URL을 요청
2. 서버는 URL 생성 후 클라이언트에 전달
3. 클라이언트에서 이미지 저장소로 바로 업로드
4. 서버쪽에 이미지 왔다갔다하는 부하를 줄일 수 있다.

#### 로컬에서 테스트

- aws cli configure

~~~
# pip install boto3
# pip install awscli
# aws configure
AWS Access Key ID [None]:
AWS Secret Access Key [None]:
Default region name [None]:
Default output format [None]:
~~~
aws IAM console 접속 -> 사용자 생성 -> 권한에 AmazonS3FullAccess 추가후 키 확인해서 넣어주면 됨
Default region은 한국 리전일 경우 ap-northeast-2 

***

- s3 버킷 조회

~~~
import boto3
from botocore.exceptions import ClientError

def getBuckets():
    s3 = boto3.client('s3')
    response = s3.list_buckets()

    print('Existing buckets:')
    for bucket in response['Buckets']:
        print(f'  {bucket["Name"]}')

if __name__ == '__main__':
    getBuckets()
~~~

***

- upload 용 PreSignedURL 생성

~~~
def create_presigned_post():
    
    bucket_name='yourbuckname'
    object_name='overlay.png' # 생성될 object name
    fileds={"acl": "public-read", "Content-Type": 'png'}
    conditions=[
        {"acl":"public-read"},
        {"Content-Type":"png"}
    ]
    expiration=3600
   
    # Generate a presigned S3 POST URL
    s3_client = boto3.client('s3')
    try:
        response = s3_client.generate_presigned_post(bucket_name,
                                                     object_name,
                                                     fileds,
                                                     conditions,
                                                     expiration)
    except ClientError as e:
        logging.error(e)
        return None

    # The response contains the presigned URL and required fields
    print(response)
    return response

if __name__ == '__main__':
    create_presigned_post()
~~~

***

- response 확인

~~~
{
'url': 'https://yourbucket.s3.amazonaws.com/', 
'fields': 
{'key': 'object_name', 
'x-amz-algorithm': 'AWS4-HMAC-SHA256', 
'x-amz-credential': '', 
'x-amz-date': '', 
'policy': '', 
'x-amz-signature': ''
}
}
~~~

url에 아래 field들을 body로 multipart 요청 보내면 된다

***

- postman 예시

![screenshot](/images/postman_local.JPG)


***

#### AWS Lambda (Python3.6) 에서 사용

- Lambda의 IAM 역할에 AmazonS3FullAccess 추가

- Lambda 함수 작성

~~~
import json
import boto3

def lambda_handler(event, context):
    
    bucket_name='yourbuckname'
    object_name=event['object_name'] # parameter
    fileds={"acl": "public-read", "Content-Type": 'png'}
    conditions=[
        {"acl":"public-read"},
        {"Content-Type":"png"}
    ]
    expiration=3600
   
    # Generate a presigned S3 POST URL
    s3_client = boto3.client('s3')
    try:
        response = s3_client.generate_presigned_post(bucket_name,
                                                     object_name,
                                                     fileds,
                                                     conditions,
                                                     expiration)
    except ClientError as e:
        logging.error(e)
        return None

    # The response contains the presigned URL and required fields
    #print(response)
    return response
~~~

***

- 테스트 이벤트 구성 후 테스트했을 때 response

~~~
{
  "url": "https://yourbucket.s3.amazonaws.com/",
  "fields": {
    "acl": "public-read",
    "Content-Type": "png",
    "key": "object_name",
    "x-amz-algorithm": "AWS4-HMAC-SHA256",
    "x-amz-credential": "",
    "x-amz-date": "",
    "x-amz-security-token": "",
    "policy": "",
    "x-amz-signature": ""
  }
}
~~~

로컬에서 했을때와 파라미터는 다르지만 똑같이 field를 body로 mulitpart 전송하면됨 

외부에서 접속가능한 URL은 https://yourbucket.s3.amazonaws.com/object_name