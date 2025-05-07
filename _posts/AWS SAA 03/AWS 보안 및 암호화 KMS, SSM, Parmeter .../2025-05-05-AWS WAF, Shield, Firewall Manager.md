---
title:AWS WAF, Shield, Firewall Manager
author: cotes   
categories: [AWS SSA 03,AWS 보안 및 암호화 KMS SSM Parameter..]
tags: [AWS]







---

#### **AWS WAF (Web Application Firewall)**

- **목적**: **L7 (애플리케이션 계층)**에서 웹 애플리케이션 보호 (SQLi, XSS, 봇 트래픽 차단 등).
- **적용 가능 리소스**:
  - **CloudFront 배포** (글로벌 리소스 → `us-east-1`에서만 설정).
  - **ALB (Application Load Balancer)**, **API Gateway** (리전 리소스).
- **핵심 기능**:
  - **웹 ACL (Web Access Control List)**: 규칙 그룹을 정의하여 트래픽 필터링.
    - **규칙 유형**:
      - **관리형 규칙 그룹**: AWS/파트너사 제공 (예: 봇 제어, 익명 IP 차단).
      - **사용자 정의 규칙**: IP 세트, 문자열 패턴 기반 차단.
    - **용량 (WCU)**: 규칙당 용량 소모 (최대 1,500 WCU/웹 ACL).
  - **우선순위 설정**: 규칙 처리 순서 지정.
  - **기본 작업**: 규칙 불일치 시 허용/차단 선택.

------

#### **AWS Shield**

- **목적**: **DDoS 공격 방어**.

- **계층**:

  | **Shield Standard**            | **Shield Advanced**                          |
  | :----------------------------- | :------------------------------------------- |
  | 무료 (모든 AWS 고객).          | 유료 (월 $3,000 + 리소스별 추가 비용).       |
  | 기본적인 L3/L4 보호.           | 고급 DDoS 보호 + 24/7 전문가 지원.           |
  | CloudFront, Route53 자동 적용. | WAF, EC2, ELB, Global Accelerator 등과 통합. |

- **주요 기능**:

  - 공격 탐지/완화 자동화.
  - 공격 보고서 및 보호 현황 대시보드 제공.

------

#### **AWS Firewall Manager**

- **목적**: **다중 계정/리전**에서 WAF, Shield, 보안 그룹 정책 **중앙 관리**.
- **주요 기능**:
  - 조직 전체에 일관된 보안 정책 적용.
  - **지원 서비스**: WAF, Shield Advanced, Network Firewall, 보안 그룹.
  - **정책 유형**:
    - WAF 규칙 (예: 모든 ALB에 XSS 차단 규칙 적용).
    - Shield Advanced 보호 활성화.
    - 보안 그룹의 허용 포트 제한.
- **비용**: 정책당 월 $100 (리소스 비용 별도).

------

#### **통합 시나리오 및 시험 핵심 포인트**

1. **WAF + CloudFront**:
   - 글로벌 웹 애플리케이션에 대한 공격 차단 (예: SQL Injection).
2. **WAF + ALB**:
   - 리전 기반 애플리케이션 보호 (예: API Gateway에 연결된 서버리스 앱).
3. **Shield Advanced + WAF**:
   - DDoS 공격 대응 + 애플리케이션 레이어 보호 강화.
4. **Firewall Manager**:
   - 대규모 조직에서 모든 계정의 WAF/Shield 정책 일괄 관리.

------

### **체크리스트**

1. **WAF**: L7 보호 (웹 ACL, 관리형 규칙, 용량 제한).
2. **Shield**: DDoS 방어 (Standard 무료, Advanced 유료).
3. **Firewall Manager**: 중앙 관리 (WAF/Shield 정책 일괄 적용).
4. **통합**: WAF는 CloudFront/ALB/API Gateway와, Shield는 리소스 보호와 연동.