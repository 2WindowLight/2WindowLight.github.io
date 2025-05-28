---
title:DevNet API
author: cotes 
categories: [cisco virtual cloud,2025-05-23-cisco]
tags: [Network,DevNet]










---

## 4.1.1 API란 무엇인가?

#### **API의 개념**  
- API(**Application Programming Interface**)는 **한 소프트웨어가 다른 소프트웨어와 통신할 수 있게 하는 인터페이스**이다.  
- **전기 콘센트**에 비유할 수 있다. 만약 콘센트가 없다면, 노트북에 전원을 공급하기 위해 벽을 뜯고, 전선을 연결하고, 복잡한 배선을 이해해야 할 것이다.  
- API는 **프로그래머가 기존 애플리케이션의 기능을 활용하거나 새로운 애플리케이션을 구축할 수 있는 방법을 정의**한다.  

---

#### **API의 작동 방식**  
1. **통신 방식**  
   - 일반적인 **웹 기반 상호작용** 또는 **통신 프로토콜**을 사용할 수 있다.  
   - 때로는 **자체적인 표준**을 사용하기도 한다.  

2. **예시: 맛집 추천 앱**  
   - 앱 개발자가 직접 지도 기능을 만들 필요 없이, **제3자 API(예: Google Maps API)를 통합**하여 맛집 위치를 표시할 수 있다.  
   - API 제공자는 **어떻게, 어떤 조건으로 인터페이스에 접근할 수 있는지 규정**한다.  

---

#### **API의 보안과 제한**  
- API는 **어떤 데이터, 서비스, 기능을 외부에 공개할지 결정**한다.  
  - **API로 공개되지 않은 기능은 절대 접근할 수 없다** (보안이 올바르게 설정되었다는 가정 하에).  
- API를 제공함으로써, 애플리케이션은 **안전하게 필요한 기능만 노출**할 수 있다.  

> **핵심 요약**: API는 소프트웨어 간의 효율적이고 안전한 통신을 가능하게 하는 **"규약"**이며, 개발 시간을 단축하고 기능 재사용을 용이하게 한다.  

------

## 4.2.1 API 디자인 스타일의 유형

#### **1. 동기식(Synchronous) vs 비동기식(Asynchronous) API**  
- API는 **동기식(Synchronous)** 또는 **비동기식(Asynchronous)** 방식으로 제공된다.  
- 클라이언트(API를 사용하는 애플리케이션)는 **API의 설계 방식에 따라 응답을 다르게 처리**해야 한다.  

| **구분**      | **동기식(Synchronous)**                         | **비동기식(Asynchronous)**                                |
| ------------- | ----------------------------------------------- | --------------------------------------------------------- |
| **응답 방식** | **즉시 응답** (요청 후 결과가 나올 때까지 대기) | **지연 응답** (요청 후 완료 신호 또는 콜백으로 결과 수신) |
| **예시**      | REST API (HTTP 요청-응답)                       | 웹훅(Webhook), 메시지 큐(Kafka, RabbitMQ)                 |
| **장점**      | 간단한 구현, 실시간 처리                        | 대용량/장시간 작업에 적합, 리소스 효율성                  |
| **단점**      | 대기 시간 문제 (블로킹)                         | 복잡한 구현, 상태 추적 필요                               |

- **주의**: 하나의 제품이 **동기식과 비동기식 API를 혼용**할 수 있지만, **설계 논리는 일관성 있게 유지**하는 것이 좋다.  

---

#### **2. NORTHBOUND vs SOUTHBOUND API**  

![What is the difference between Northbound and Southbound Interfaces?](https://learningnetwork.cisco.com/sfc/servlet.shepherd/version/renditionDownload?rendition=THUMB720BY480&versionId=0683i000001rnMl&operationContext=CHATTER&contentId=05T3i00000ACKHA&page=0)

API는 시스템 간의 통신 방향에 따라 **Northbound(북향)**과 **Southbound(남향)**으로 구분된다.  

| **구분**      | **Northbound API**                             | **Southbound API**                                    |
| ------------- | ---------------------------------------------- | ----------------------------------------------------- |
| **통신 방향** | **상위 시스템 → 하위 시스템** (제어/관리 목적) | **하위 시스템 → 상위 시스템** (데이터 수집/제공 목적) |
| **예시**      | 클라우드 관리 시스템이 가상머신 제어           | IoT 디바이스가 센서 데이터 전송                       |
| **주요 용도** | Orchestration, 자동화                          | 데이터 수집, 모니터링                                 |

> **Northbound** = "위로 올라가는" API (관리자/제어 계층으로 데이터/명령 전달)  
> **Southbound** = "아래로 내려가는" API (하위 리소스/디바이스에서 데이터 수집)  

---

### **결론**  
- **API 설계 시 동기/비동기 방식을 목적에 맞게 선택**해야 한다.  
- **Northbound/Southbound API**는 시스템 아키텍처에서 **계층 간 통신의 역할**을 정의한다.  
- 일관된 설계 원칙이 중요하며, 혼용 시에도 논리적 통일성을 유지해야 한다.  

------

### **4.4.1 REST 웹 서비스 API 핵심 요약**  

#### **1. REST API 기본 개념**  
- **정의**: HTTP 프로토콜을 사용하며 **REST 아키텍처 원칙**을 따르는 프로그래밍 인터페이스.  
- **REST 6대 원칙**:  
  - Client-Server, Stateless, Cache, Uniform Interface, Layered System, Code-On-Demand (선택적).  

#### **2. REST API 요청(Request) 구조**  
- **4가지 주요 구성 요소**:  
  1. **URI (Uniform Resource Identifier)**  
     - **구성**: `Scheme://Authority/Path?Query` (예: `https://api.example.com/v1/users?name=DevNet`).  
     - **역할**: 리소스 위치 및 액션 대상 지정.  
  2. **HTTP Method (Verb)**  
     - **주요 메서드**: GET(조회), POST(생성), PUT(전체 수정), PATCH(부분 수정), DELETE(삭제).  
  3. **Header**  
     - **타입**: Request Header (메타데이터), Entity Header (본문 내용 설명).  
     - **예시**: `Content-Type: application/json`.  
  4. **Body**  
     - **용도**: POST/PUT/PATCH 요청 시 전송할 데이터 (예: JSON, XML).  

#### **3. REST API 응답(Response) 구조**  
- **3가지 주요 구성 요소**:  
  1. **HTTP Status Code**  
     - **카테고리**:  
       - 2xx (성공), 4xx (클라이언트 오류), 5xx (서버 오류).  
     - **대표 코드**: 200 (OK), 201 (Created), 404 (Not Found), 500 (Internal Error).  
  2. **Header**  
     - **예시**: `Content-Type: application/json`, `Content-Encoding: gzip` (데이터 압축 시).  
  3. **Body**  
     - **성공 시**: 요청한 데이터 (예: JSON 배열).  
     - **실패 시**: 오류 상세 정보 (예: `{"error": "Invalid input"}`).  

#### **4. 고급 기능**  
- **페이지네이션(Pagination)**: 대량 데이터를 청크로 분할 (예: `?page=2&limit=10`).  
- **데이터 압축**: `Accept-Encoding: gzip` 헤더로 요청 → 서버가 압축 응답.  

#### **5. 시퀀스 다이어그램과 비동기 처리**  
- **동기 vs 비동기**:  
  - **동기**: 요청 → 즉시 응답 (예: 장비 목록 조회).  
  - **비동기**: 요청 → 작업 ID(taskId) 반환 → 후속 상태 확인 (예: 장비 추가 작업).  
- **시퀀스 다이어그램**:  
  - **용도**: 클라이언트-서버 간 상호작용 순서 시각화.  
  - **예시**:  
    1. **세션 생성** → **장비 목록 조회** (동기).  
    2. **장비 추가** → **taskId 반환** → **상태 폴링** (비동기).  

---

### **요약 표**  
| **구분**        | **주요 내용**                                       |
| --------------- | --------------------------------------------------- |
| **REST 원칙**   | Stateless, Uniform Interface 등 6가지 원칙 준수.    |
| **요청 구성**   | URI + HTTP Method + Header + Body.                  |
| **응답 구성**   | Status Code + Header + Body.                        |
| **상태 코드**   | 2xx (성공), 4xx (클라이언트 오류), 5xx (서버 오류). |
| **비동기 처리** | taskId 반환 후 상태 폴링 (예: HTTP 202 Accepted).   |

> 💡 **핵심**: REST API는 **HTTP 표준**을 기반으로 하며, **URI와 메서드로 리소스 제어**. 비동기 작업 시 **폴링(Polling)** 메커니즘 활용.