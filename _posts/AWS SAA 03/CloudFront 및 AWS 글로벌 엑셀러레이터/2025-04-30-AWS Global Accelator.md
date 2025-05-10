---
title:AWS Global Accelerator
author: cotes   
categories: [AWS SSA 03,CloudFront 및 글로벌 엑셀러레이터]
tags: [AWS]





---

## 

### **AWS Global Accelerator 핵심 정리**

------

#### **1. 개요**

- **정의**: 글로벌 애플리케이션의 성능과 가용성을 향상시키는 **라우팅 서비스**.
- TCP/UDP 엔드포인트에 입력할 수 있는 고정 IP를 가져야함

- **목적**:
  - **지연 시간 최소화**: AWS 내부 네트워크를 통해 트래픽 전송.
  - **고정 IP 제공**: 2개의 **애니캐스트** IP 주소를 통해 글로벌 엔드포인트 노출.
  - **자동 장애 조치**: 리전/엔드포인트 장애 시 1분 내 복구.

#### **주요 기능**

- **애니캐스트 IP**:
  - 동일 IP로 전 세계 엣지 로케이션에 트래픽 분산 → 사용자와 가장 가까운 엣지로 라우팅.
- **지능형 라우팅**:
  - 실시간 네트워크 상태를 기반으로 최적 경로 선택.
- **상태 확인**:
  - y66yyy엔드포인트(ALB, NLB, EC2)의 건강 상태 모니터링 → 장애 시 자동 전환.
- **보안**:
  - **AWS Shield Standard** 통합 → DDoS 공격 방어.
  - 고정 IP 2개만 허용 → 화이트리스트 관리 용이.

------

#### **CloudFront vs Global Accelerator**

| **구분**      | **CloudFront**              | **Global Accelerator**                |
| :------------ | :-------------------------- | :------------------------------------ |
| **주요 목적** | 정적/동적 콘텐츠 캐싱 (CDN) | TCP/UDP 트래픽 라우팅 최적화          |
| **프로토콜**  | HTTP/HTTPS/WebSocket        | TCP/UDP                               |
| **캐싱**      | 엣지 로케이션에서 캐시 제공 | 캐싱 없음 (모든 요청 엔드포인트 전달) |
| **고정 IP**   | ❌                           | ✅ (2개의 애니캐스트 IP)               |
| **적합 사례** | 웹 사이트, 미디어 스트리밍  | 게임, VoIP, IoT, 글로벌 API           |

------

#### **시험 핵심 팁**

1. **Global Accelerator 선택 조건**:
   - **"고정 IP 필요", "TCP/UDP", "글로벌 장애 조치"** 키워드 → Global Accelerator.
2. **CloudFront와 차이**:
   - CloudFront는 캐싱, Global Accelerator는 라우팅, 요청을 프록시하고 응답을 위해 항상 애플리케이션 연결.
3. **애니캐스트 IP**:
   - 동일 IP로 최적 경로 라우팅 → 지연 시간 감소.
4. **상태 확인**:
   - 엔드포인트 상태 불량 시 자동으로 정상 노드로 전환.

<img src="../../../assets/cisco_post_img/2025-04-30-AWS Global Accelator//image-20250507165725376.png" alt="image-20250507165725376" style="zoom:50%;" />