---
title: <h0>OSPF Link State Database/h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]









---

## LSA와 링크 상태 데이터베이스



### LSA(Link State Advertisement) - OSPF가 사용하는 라우팅 정보를 LSA 라고 한다.

OSPF는 네이버로부터 수신한 LSA를 모두 링크 상태 데이터베이스(Link State Database)에 저장한다.

그런 다음, 이들을 이용하여 특정 목적지로 가는 최적 경로를 계산하고, 이를 라우팅 테이블에 저장시킨다.

------

## **OSPF LSA 종류 정리 **

| **LSA 타입** | **이름**          | **생성 라우터**          | **내용**                                                     | **확인 명령어**                     | **전송 범위**       |
| ------------ | ----------------- | ------------------------ | ------------------------------------------------------------ | ----------------------------------- | ------------------- |
| **Type 1**   | Router LSA        | 모든 라우터              | 라우터의 인터페이스 및 인접 라우터 정보 포함                 | show ip ospf database router        | 같은 영역 (Area)    |
| **Type 2**   | Network LSA       | DR (Designated Router)   | 해당 네트워크의 DR 및 접속된 모든 라우터 정보 포함           | show ip ospf database network       | 같은 영역 (Area)    |
| **Type 3**   | Summary LSA       | ABR (Area Border Router) | 다른 영역으로 보낼 요약된 네트워크 정보                      | show ip ospf database summary       | AS 내부 (인터 영역) |
| **Type 4**   | ASBR Summary LSA  | ABR                      | ASBR(Autonomous System Boundary Router) 정보를 다른 영역으로 전파 | show ip ospf database asbr-summary  | AS 내부 (인터 영역) |
| **Type 5**   | External LSA      | ASBR                     | 외부(Non-OSPF) 네트워크 경로 정보 포함                       | show ip ospf database external      | 전체 OSPF 도메인    |
| **Type 7**   | NSSA External LSA | NSSA ASBR                | NSSA(Not-So-Stubby Area) 내부에서 외부 경로 정보 전파        | show ip ospf database nssa-external | NSSA 내부           |

✅ **주요 차이점 정리:**

​	•	**Type 1, 2**: **같은 영역 내부에서만 전파**

​	•	**Type 3, 4**: **ABR를 통해 다른 영역으로 요약 정보 전달**

​	•	**Type 5**: **ASBR가 생성하여 모든 OSPF 라우터에 전파 (Stub 영역 제외)**

​	•	**Type 7**: **Stub 대신 NSSA에서만 사용되는 외부 라우트**



 **LSA 확인 시 show ip ospf database 사용!** 

------

###  **📌 OSPF 라우터 역할 정리 표**

| **개념**                                     | **설명**                                                     | **쉽게 말하면**                                        |
| -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| **ABR (Area Border Router)**                 | 두 개 이상의 OSPF 영역(Area 0 포함)을 연결하는 라우터. 각 영역 간 LSA를 교환하고, OSPF 요약(route summarization)을 수행할 수 있음. | **“Area 0과 다른 영역을 연결하는 경계 라우터”**        |
| **ASBR (Autonomous System Boundary Router)** | OSPF 외부의 다른 라우팅 프로토콜(BGP, EIGRP 등)과 연결된 라우터. 외부 경로를 OSPF 내부로 가져오거나 반대로 내보냄. | **“OSPF와 외부 네트워크를 연결하는 라우터”**           |
| **NSSA (Not-So-Stubby Area)**                | 기본적으로 Stub처럼 외부 경로 차단하지만, 내부에 ASBR이 있는 경우 외부 경로를 Type 7 LSA로 받아들일 수 있음. | **“Stub + 필요할 때만 외부 경로 가능”**                |
| **NSSA ASBR**                                | NSSA 내부에서 외부 네트워크(BGP, EIGRP 등)와 연결된 라우터. Type 7 LSA를 생성하여 OSPF 내부로 외부 경로를 가져옴. | **“NSSA 내부에서 외부 경로를 OSPF로 가져오는 라우터”** |
| **ABR (NSSA 관련)**                          | NSSA 내부의 ASBR이 생성한 Type 7 LSA를 일반 OSPF가 이해할 수 있는 Type 5 LSA로 변환하여 OSPF 전체에 전파함. | **“NSSA 외부 경로를 OSPF 전체로 퍼뜨리는 역할”**       |

=