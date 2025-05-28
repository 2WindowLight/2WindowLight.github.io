---
title:CloudFront vs Global Accelerator 핵심 팁
author: cotes
categories: [AWS SSA 03]
tags: [AWS]

---

### **문제 풀이 키워드**

- **"콘텐츠 캐싱", "정적 파일", "동적 페이지"** → **CloudFront**.
- **"TCP/UDP 성능", "고정 IP", "실시간 장애 조치"** → **Global Accelerator**.
- **"동시에 두 서비스 사용"**:
  - **예: CloudFront로 콘텐츠 전달 + Global Accelerator로 백엔드 API 가속.**

------

### **주요 차이점**

| **구분**        | **CloudFront**             | **Global Accelerator**               |
| :-------------- | :------------------------- | :----------------------------------- |
| **트래픽 계층** | Layer 7 (HTTP/HTTPS)       | Layer 4 (TCP/UDP)                    |
| **캐싱**        | 지원 (엣지 로케이션)       | 미지원                               |
| **고정 IP**     | 미지원 (엣지 IP 동적 변경) | 지원 (Anycast IP)                    |
| **장애 조치**   | TTL 기반 캐시 무효화       | 실시간 Health Check + 자동 장애 조치 |
| **비용 모델**   | 데이터 전송량 + 요청 수    | 고정 IP 요금 + 데이터 전송량         |

------

### **Global Accelerator 종류**

1. **표준 Accelerator**:
   - 일반적인 애플리케이션 가속 (ALB, EC2, 온프레미스 서버 연결).
2. **Custom Routing Accelerator**:
   - 특정 포트/프로토콜에 맞춘 라우팅 (예: 게임 서버의 UDP 3074 포트).

- **시험 팁**:
  - "특정 포트 라우팅 필요" → **Custom Routing Accelerator**.
  - "일반적인 글로벌 로드 밸런싱" → **표준 Accelerator**.

------

### Lambda@Edge

**Lambda@Edge는 AWS 클라우드 컴퓨팅 서비스인 Lambda를 전 세계 컨텐츠 전송 네트워크인 CloudFront의 엣지 위치에서 실행하는 것**

------

### AWS Endpoint

**AWS Endpoint는 VPC 내부에서 외부의 AWS 서비스와 통신하기 위한 진입점(Entry Point)을 의미합니다. VPC Endpoint를 사용하면 인터넷을 거치지 않고 PrivateLink를 통해 안전하게 AWS 서비스에 접속**