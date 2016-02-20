---
layout: post
title:  "Setup AWS CLI on mac"
date:   2014-05-05 11:45:00
categories: aws cli
---

아래 문서를 기반으로 한다. 

http://docs.aws.amazon.com/AWSEC2/latest/CommandLineReference/set-up-ec2-cli-linux.html

--- 
## 사전 준비사항
### CLI 툴 설치가 필요하다. 
- wget http://s3.amazonaws.com/ec2-downloads/ec2-api-tools.zip
- curl -O http://s3.amazonaws.com/ec2-downloads/ec2-api-tools.zip

### 인증 키 설정
- AWS access key와 secret key 설정을 환경변수에 설정해야 한다. 우선 ~/.bashrc 에 아래 내용을 기록한다. 
```
export AWS_ACCESS_KEY=your-aws-access-key-id
export AWS_SECRET_KEY=your-aws-secret-key
```

- 업데이트 후 아래 명령어로 반영한다. 
```
source ~/.bashrc
```


 
