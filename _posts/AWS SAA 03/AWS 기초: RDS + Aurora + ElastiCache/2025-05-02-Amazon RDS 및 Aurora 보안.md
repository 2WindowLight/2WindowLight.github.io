---
title:Amazon RDS 및 Aurora 보안
author: cotes   
categories: [AWS SSA 03,AWS 기초: RDS + Aurora + ElastiCache]
tags: [AWS]






---

#### **암호화**

#### **시험 핵심 포인트**

1. **암호화 전환**:
   - 기존 DB 암호화 → **스냅샷 생성 → 암호화된 복제본 복원**.
   - Aurora 암호화는 **비활성화 불가**.
2. **IAM 인증**:
   - **"패스워드 없음", "EC2 IAM 역할"** 키워드 → IAM 데이터베이스 인증 선택.
3. **보안 그룹**:
   - **"특정 IP 허용", "VPC 내 프라이빗 서브넷"** → 보안 그룹 구성 필수.
4. **로그 관리**:
   - **장기 보관 필요 → CloudWatch Logs 사용**.



1. **저장 데이터 암호화 (At-Rest Encryption)**
   - **KMS 사용**: AWS Key Management Service (KMS)를 통해 마스터 키로 데이터 암호화.
   - **활성화 시기**: 데이터베이스 생성 시 설정 가능 (기존 암호화되지 않은 DB는 **스냅샷 복원**으로만 암호화 가능).
   - **복제본 영향**:
     - 마스터 DB가 암호화되지 않으면 읽기 전용 복제본도 암호화 불가.
     - Aurora 글로벌 DB의 경우 **모든 리전에서 암호화 필수**.
2. **전송 중 암호화 (In-Transit Encryption)**
   - **TLS/SSL 적용**: 클라이언트 ↔ 데이터베이스 간 통신 암호화.
   - **인증서 관리**: AWS에서 제공하는 루트 인증서 사용 (예: `rds-ca-2019`).