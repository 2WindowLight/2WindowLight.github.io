---
title:Amazon Inpsector
author: cotes   
categories: [AWS SSA 03,AWS 보안 및 암호화 KMS SSM Parameter..]
tags: [AWS]








---

#### **Amazon Inspector**

- **목적**: EC2 인스턴스의 활성 취약성 스캔을 수행. 소프트웨어 취약성, 의도하지 않은 네트워크 접근성 및 기타 보안 문제를 찾는다.
- **주요 기능**:
  - **EC2 인스턴스**: OS 취약점, 네트워크 접근성 분석.
  - **ECR 컨테이너 이미지**: 알려진 취약점 스캔.
  - **Lambda 함수**: 코드 및 종속성 취약점 검사.
- **작동 방식**:
  - **지속적 모니터링** (EC2) + **이벤트 트리거** (ECR 푸시, Lambda 배포 시).

------

#### **시험 핵심 포인트**

1. **SSM Agent 필수**: EC2 인스턴스 스캔을 위해 SSM Agent 설치 필요.
2. **이벤트 기반 스캔**:
   - **ECR**: 이미지 푸시 → 즉시 스캔.
   - **Lambda**: 함수 배포 → 즉시 스캔.
3. **보안 서비스 통합**:
   - Security Hub (결과 집계) + EventBridge (자동화) 필수 암기.
4. **CVE 기준**: OS/라이브러리 취약점은 공개된 CVE 목록과 비교하여 평가.

------

### **요약 체크리스트**

1. **리소스 지원**: EC2 (SSM 필수), ECR, Lambda.
2. **스캔 방식**: 지속적 (EC2) + 이벤트 트리거 (ECR/Lambda).
3. **통합**: Security Hub (집계), EventBridge (자동화).
4. **CVE 기반**: 알려진 취약점 데이터베이스 활용.