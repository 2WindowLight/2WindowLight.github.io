---
title:Amazon Cognito
author: cotes   
categories: [AWS SSA 03,솔루션 설계자 관점의 서버리스]
tags: [AWS]









---

### **API Gateway 생성 및 통합**

- **목적**: API Gateway는 애플리케이션이 백엔드 서비스의 데이터, 비즈니스 로직 또는 기능에 접근할 수 있도록 하는 **'정문'** 역할을 수행합니다

------

### **보안 구성 (선택 사항)**

- **옵션**:
  - **API 키**: 사용량 제한 및 추적.
  - **Cognito 사용자 풀**: 인증된 사용자만 접근 허용.
  - **IAM 권한**: API Gateway 실행을 위한 리소스 정책.
  - **HTTPS**: 사용자 지정 도메인 + ACM 인증서 적용.

------

### **클라이언트 호출**

- **엔드포인트 사용**:
  클라이언트는 API Gateway의 URL로 HTTP 요청 전송 (예: `POST https://api-id.execute-api.region.amazonaws.com/prod/invoke`).
- **응답**: 비동기 호출 시 Lambda는 `202 Accepted`를 즉시 반환.

------

- **서버리스 아키텍처**:
  API Gateway + Lambda = 인프라 관리 불필요.
- **보안**:
  IAM, Cognito, API 키 등을 활용해 엔드포인트 보호.
- **배포 유형**:
  - **엣지 최적화**: 글로벌 클라이언트 (CloudFront 활용).
  - **리전**: 동일 리전 클라이언트.
  - **프라이빗**: VPC 내부 접근.