---
title: <h0>AWS 보안 그룹과 Network ACL 차이점</h0>
author: cotes 
categories: [cisco virtual cloud,2025-04-11-cisco]
tags: [Network,AWS]






---

# AWS NACL과 보안 그룹의 차이점

---

## 1. 기본 개념

**N ACL(Network Access Control List)**

- **서브넷** 수준에서 작동하는 방화벽
- VPC 내부의 서브넷 입출구에서 트래픽을 필터링
- 규칙 번호에 따라 순차적으로 평가되는 명시적 허용/거부 규칙

**보안 그룹(Security Group)**
- 인스턴스 수준에서 작동하는 가상 방화벽
- EC2 인스턴스와 같은 리소스에 직접 적용
- 암묵적 거래(기본적으로 모든 트래픽 차단)와 명시적 허용 규칙

---

## 2. 적용 범위와 계층 구조

**NACL 적용 범위**
- 서브넷 전체에 적용되는 광범위한 규칙
- VPC 내의 모든 인스턴스에 영향을 미칠 수 있음
- 계층 구조상 보안 그룹보다 상위 레벨에서 작동

**보안 그룹 적용 범위**
- 특정 인스턴스 또는 인스턴스 그룹에 적용
- 더 세분화된 접근 제어 가능
- NACL보다 더 구체적인 수준에서 보안 적용

---

## 3. 규칙 평가 방식

**NACL 규칙 평가**
- 번호가 매겨진 규칙 목록(1-32766)
- 낮은 번호부터 순차적으로 평가
- 첫 번째로 일치하는 규칙이 적용되면 평가 중단
- 기본적으로 마지막에 암시적인 '모든 트래픽 거부' 규칙 존재

**보안 그룹 규칙 평가**
- 상태 저장(stateful) 방식으로 작동
- 모든 규칙이 독립적으로 평가
- 규칙 간 우선순위 없음
- 명시적으로 허용하지 않은 모든 트래픽 자동 차단

---

## 4. 상태 추적 기능

**NACL의 상태 비저장(Stateless) 특성**
- 인바운드와 아웃바운드 규칙을 별도로 구성해야 함
- 요청 트래픽을 허용해도 응답 트래픽은 별도 규칙 필요
- 더 엄격하지만 관리가 복잡할 수 있음

**보안 그룹의 상태 저장(Stateful) 특성**
- 허용된 연결의 응답 트래픽 자동 허용
- 인바운드 규칙만 설정하면 응답 트래픽 자동 처리
- 사용이 간편하지만 덜 엄격할 수 있음

---

## 5. 실제 사용 사례 비교

**NACL의 일반적인 사용 예시**
- 특정 IP 대역에서의 접근 차단
- 서브넷 전체에 대한 DDoS 보호 구성
- 특정 프로토콜(예: RDP)의 광범위한 차단
- 계층화된 서브넷(퍼블릭/프라이빗) 간 트래픽 제어

**보안 그룹의 일반적인 사용 예시**
- 특정 인스턴스에 대한 SSH/RDP 접근 제한
- 웹 서버에 대한 HTTP/HTTPS 트래픽 허용
- 데이터베이스 인스턴스에 대한 특정 포트 접근 제어
- 애플리케이션 계층 간의 통신 제어

---

## 6. 함께 사용할 때의 시너지 효과

NACL과 보안 그룹은 상호 보완적으로 사용될 때 가장 효과적입니다. 일반적인 보안 전략은 다음과 같습니다:

1. NACL을 사용하여 서브넷 수준에서 광범위한 차단 정책 적용
2. 보안 그룹을 사용하여 인스턴스 수준에서 세부적인 허용 정책 구성
3. NACL로 네트워크 계층의 기본적인 보안 프레임워크 구축
4. 보안 그룹으로 애플리케이션별 특수한 요구사항 처리

이러한 다층적 방어 전략은 보안 사고 발생 시 피해 범위를 최소화하고, 보다 견고한 인프라 보안을 구축할 수 있게 합니다.