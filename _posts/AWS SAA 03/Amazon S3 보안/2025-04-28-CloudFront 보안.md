---
title:CloudFront
author: cotes   
categories: [AWS SSA 03,Amazon S3 보안]
tags: [AWS]




---

### 

### Amazon CloudFront 핵심 정리

------

#### **1. 개요**

- **정의**: **글로벌 CDN(Content Delivery Network)** 서비스.
- **목적**: 정적/동적 콘텐츠를 엣지 로케이션에 캐시 → **저지연 전송**.
- **특징**:
  - 전 세계 **216+개의 엣지 로케이션** (지속적 확장).
  - DDoS 공격 방어 (AWS Shield, WAF 통합).
  - S3, ALB, EC2, 사용자 정의 HTTP 백엔드 등 다양한 **원본(Origin)** 지원.

------

####  **보안**

- **DDoS 방어**:
  - **AWS Shield Standard**: 기본 제공.
  - **AWS Shield Advanced**: 고급 보호 (유료).
- **WAF(Web Application Firewall)**: SQLi, XSS 등 공격 차단.
- **암호화**:
  - **HTTPS 지원**: 엣지 ↔ 사용자, 엣지 ↔ 원본 간 암호화.
  - **Field-Level 암호화**: 민감 데이터 부분 암호화.

------

#### **시험 핵심 팁**

1. **CDN 필요 조건**:
   - 글로벌 사용자, 낮은 지연 시간, 캐싱 → **CloudFront** 선택.
2. **OAC**:
   - S3 접근 제어 필수 → OAC 사용 (OAI 대체됨).
3. **캐싱 동작**:
   - 최초 요청: 원본 접근 → 캐시 저장.
   - 재요청: 엣지에서 직접 응답.
4. **DDoS 방어**:
   - Shield + WAF 조합 → 시험 문제 출제 가능성 높음.
5. **CRR과 차이점**:
   - CloudFront: 캐시 기반 정적 콘텐츠.
   - CRR: 리전 간 실시간 복제 (동적 데이터).

------

| **주요 개념**         | **설명**                  |
| :-------------------- | :------------------------ |
| **엣지 로케이션**     | 216+개 글로벌 캐시 지점   |
| **OAC**               | S3 원본 보안 필수 도구    |
| **캐싱 메커니즘**     | TTL 설정 → 캐시 효율 관리 |
| **DDoS 방어**         | Shield + WAF 조합         |
| **CloudFront vs CRR** | 캐싱 vs 실시간 복제       |



### CDN

CDN은 **content delivery network**의 약자로 컨텐츠 전송 네트워크로써 **지리적 물리적**으로 떨어져있는 사용자에게 더 빠르게 컨텐츠를 제공하는 시스템이다.