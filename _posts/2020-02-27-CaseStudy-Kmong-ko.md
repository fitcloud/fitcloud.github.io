---
layout: post
title: AWS CaseStudy-크몽
date: 2020-02-27
---

![kmong](../images/CaseStudy/2020-02-27-Kmong/kmong-logo.png) https://www.kmong.com

#### Customer : 크몽 ( Kmong )


### Start/End Date
- 2019/03/04 ~ 2019/06/03 (3month)

### Problem statement/definition
- 고객은 초기 부터 AWS Cloud에서 서비스 개발 및 운영을 진행, 전형적인 Monolitic한 서비스 구현을 시작으로 초기에는 서비스 규모와 팀원이 작아 큰 이슈가 없었음.
- 현재는 100명 가까이 인력 늘어 났으며, 서비스 역시 예전에 비해 비약적으로 발전을 한 상태로 서비스 업데이트를 위해 배포시 약 2~3시간 가량 소요되며, 그동안 관련 개발자들 역시 배포완료까지 대기하며 다른 업무를 하지 못하는 문제 점이 있었음.
- 주 단위 또는 2주 단위 배포로 Application의 배포의 자유도가 떨어지고 업데이트가 빠르게 일어나지 못함
- 이런 문제점들을 개기로 MSA 및 DevOps로의 접근 필요해짐



### What you proposed
#### 제안 Architecture
 - 2tier 웹서비스 구조
 - 각 Tier별 HA를 위한 이중화 및 Multi-Az 구성
 - AutoScaling을 이용한 유연한 구성
 - Serverless Fargate Service 를 활용한 서버관리 최소화
 - CI/CD를 위한 AWS Code Series를 활용한 배포 효율성 증대
 - CloudWatch Logs를 통한 Log Integration
 - ElasticSearch 서비스를 이용한 Log 통합 모니터링 서비스 진행


 ![](../images/CaseStudy/2020-02-27-Kmong/architecture-1.png)

 ![](../images/CaseStudy/2020-02-27-Kmong/architecture-2.png)


#### Agile 개발 방법론
 - 프로젝트를 통해서 Kmong의 Engineer들이 향후에도 Agile 개발 방법론을 활용할 수 있게 프로젝트를 Agile 개발방법론과 같이 진행 함.


### How AWS services were used as part of the solution
#### 사용된 AWS Service
+ **API Gateway**
  - Billing API 호출부에 대한 Gateway 역할
+ **Code Series**
  - CodeCommit : 개발 Code의 통합을 위한 Git Repository로 활용
  - CodeBuild : source repository의 code compile/test 후 ECR에 Push 용도
  - CodeDeply : 최종 Application의 배포를 위해 활용하며, CodeDeploy를 이용하여 Blue/Green    배포를 적용함.
  - CodePipeLine : Code Commit 부터 Deploy까지 전반적인 배포 워크플로우를 관장하는 역할이며 Dev/Stage/Product 까지 자동 배포될 수 있게 구성
+ **Cloud Formation**
  - 인프라의 변경 사항 및 추가 사항 등 자동화된 Provisioning을 위해 활용
+ **ECS (Fargate/ECR)**
  - Fargate : Server관리의 최소화를 위해 활용 (대부분 EC2에서 Fargate로 전환)
  - ECR : Container image Repository로 활용
+ **CloudWatch & ElasticSearch**
  - Cloudwatch : 각종 서비스 Alarm 및 CloudWatch Logs로 Log 통합 및 모니터링 DashBoard 활용
  - ElasticSearch : Log 통합 모니터링 Tool로 활용
+ **IAM, Etc… (ELB,RDS 등)**
  - IAM : 개발자 및 운영자가 AWS에 Access 할 수있는 용도로 활용
  - IAM 계정 활용 원칙 요약
     * IAM 계정은 1인 1계정 발급하며, 계정에 대해서는 MFA, 주기적인 암호교체, No Credential key 를 원칙으로 생성
     * 기본적으로 User 계정은 최소 권한(아무 권한이 없는 계정)을 원칙으로 발급
     * Service 별 Admin/Developer/Operator로 IAM Group을 생성하여 각 Group군에 필요한 Service 권한을 매칭 하고 User에 Group을 할당 함.
     * 각 Group의 추가 권한은 필요 시 Admin에 요청하여 할당

### Third party applications or solutions used
 - None

### Outcomes
- Account 분리를 통한 팀별 AWS Resource 사용량에 대한 가시성 확보 및 Resource 분리로 인한 편의성 증대
- Serverless Fargate 도입으로 서버 관리에 대한 불편함을 해소
- Application의 품질 및 성능 향상
- CI/CD 구축으로 인한 배포 편의성 증대
- 개발,테스트 또는 운영에 대한 지출 감소
- 개발 인력들의 효율적인 업무능력 향상
- Service 분리로 인한 Application 배포 소요 시간 단축
- Application Release 배포 기간 단축

<br>

```
※ 배포 소요 시간 단축
  ■ 기존 2~3 hr >> 10~20Min 단축 (80%이상 개선)

※ 배포 주기 단축
  ■ 1~2주 단위 정기 배포 >> 일단위 배포 (필요할 때)

※ Blue/Gleen 배포 ZeroDownTime 구현 Business 손실 축소
```
