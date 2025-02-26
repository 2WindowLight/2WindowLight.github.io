---
title: <h0>Controllers/h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-26-cisco]
tags: [Network, Cloud]

















---

# 13.5 Controllers



## 13.5.1 SDN Controller and Operations

![image-20250226162336165](/assets/cisco_post_img/2025-02-26-Controllers//image-20250226162336165.png)

**SDN 컨트롤러 및 운영 개요**



SDN 컨트롤러는 중앙 집중식으로 네트워크의 데이터 흐름을 제어하며, 각 라우터 및 스위치의 데이터 플레인과 상호 작용함.

------

**1. SDN 컨트롤러의 역할**

✅ **데이터 흐름 정의 및 관리**

​	•	네트워크를 통과하는 각 데이터 흐름(Flow)은 SDN 컨트롤러의 허가를 받아야 함.

​	•	컨트롤러는 네트워크 정책에 따라 흐름을 검증하고, 허용된 경우 최적의 경로를 계산한 후 해당 경로의 스위치들에 흐름 테이블(Flow Table)을 업데이트함.

✅ **중앙 집중식 네트워크 정책 적용**

​	•	복잡한 네트워크 정책 및 트래픽 엔지니어링 작업을 중앙에서 처리.

​	•	개별 장비(라우터, 스위치)는 컨트롤러의 명령에 따라 패킷을 전달.

✅ **Flow Table 관리**

​	•	컨트롤러는 각 OpenFlow 스위치의 **Flow Table**을 채우고 이를 관리함.

​	•	스위치는 컨트롤러의 명령을 받아 Flow Table을 기반으로 패킷을 처리.

✅ **보안 및 정책 강화**

​	•	컨트롤러는 TLS(Transport Layer Security)를 이용하여 OpenFlow 스위치와의 통신을 보호.

​	•	중앙 집중식 보안 정책을 통해 네트워크 전반의 보안성을 높임.

------

**2. SDN 컨트롤러와 OpenFlow**

​	•	**OpenFlow 프로토콜**을 통해 SDN 컨트롤러와 OpenFlow 호환 스위치 간의 통신이 이루어짐.

​	•	OpenFlow 스위치는 컨트롤러의 지시에 따라 패킷 전달을 수행.

​	•	OpenFlow는 보안 강화를 위해 TLS를 사용하여 컨트롤러와의 통신을 보호함.

------

**3. OpenFlow 스위치의 주요 테이블**

OpenFlow 스위치는 패킷을 관리하기 위해 다음과 같은 테이블을 사용함.

| **테이블 유형**               | **설명**                                                     |
| ----------------------------- | ------------------------------------------------------------ |
| **Flow Table (흐름 테이블)**  | - 들어오는 패킷을 특정 흐름(Flow)과 매칭하여 지정된 작업 수행- 여러 개의 Flow Table이 파이프라인 방식으로 동작할 수 있음 |
| **Group Table (그룹 테이블)** | - Flow Table이 특정 플로우를 Group Table로 보낼 수 있음- 여러 플로우에 대한 다양한 액션을 트리거 가능 |
| **Meter Table (미터 테이블)** | - 특정 흐름에 대한 성능 관련 작업 수행- 트래픽 속도 제한(rate-limiting) 등 QoS 관련 기능을 적용 가능 |

➡ **결과적으로, SDN 컨트롤러는 OpenFlow 프로토콜을 이용해 네트워크 장비를 중앙에서 제어하고, 각 스위치는 Flow Table을 기반으로 패킷을 전달함.**

------

## 13.5.4 Spine-Leaf Topology

![image-20250226162932522](/assets/cisco_post_img/2025-02-26-Controllers//image-20250226162932522.png)

**Spine-Leaf 토폴로지 (Cisco ACI Fabric)**



Cisco ACI(Application Centric Infrastructure) 패브릭은 **Cisco Nexus 9000 시리즈 스위치**와 **APIC(Application Policy Infrastructure Controller)**를 기반으로 한 **이중 계층(2-Tier) Spine-Leaf 토폴로지**로 구성됨.

**1. Spine-Leaf 구조**

✅ **Leaf 스위치**

​	•	Leaf 스위치는 **Spine 스위치에만 연결**됨.

​	•	**다른 Leaf 스위치와는 직접 연결되지 않음**.

​	•	모든 네트워크 장비(APIC, 서버, 방화벽, 로드 밸런서 등)가 Leaf 스위치에 연결됨.

✅ **Spine 스위치**

​	•	Spine 스위치는 **모든 Leaf 스위치에 연결**됨.

​	•	다른 Spine 스위치나 Leaf-Leaf 간 연결은 없음.

​	•	트래픽이 Spine을 거쳐가므로 **모든 장비가 1-hop 거리 내에 위치**.

✅ **특징**

​	•	**확장성 (Scalability)**: 새로운 Leaf 스위치를 추가해도 네트워크 성능이 저하되지 않음.

​	•	**고성능 (High Performance)**: 트래픽이 균등하게 Spine을 통해 전달되므로 병목이 줄어듦.

​	•	**예측 가능한 지연 시간 (Low Latency)**: 모든 장비가 **최대 2-hop(Leaf → Spine → Leaf)** 내에 위치.

------

**2. Cisco APIC (Application Policy Infrastructure Controller)**

Cisco ACI에서 **APIC**은 네트워크 정책을 중앙에서 정의하고, 이를 Leaf 스위치에 적용하는 역할을 함.



✅ **APIC의 역할**

​	•	**데이터 경로 직접 제어 ❌ (SDN과 차이점)**

→ SDN 컨트롤러와 달리, **APIC는 직접 트래픽을 처리하지 않음**.

​	•	**정책 관리 (Policy Enforcement) ✅**

→ 네트워크 정책을 중앙에서 설정하고, Leaf 스위치가 이를 기반으로 트래픽을 처리하도록 설정.

​	•	**네트워크 자동화 (Automation) ✅**

→ 장비 추가, 삭제, 설정 변경 등을 자동으로 처리하여 운영 효율성을 증가시킴.

​	•	**보안 정책 적용 (Security Enforcement) ✅**

→ VLAN, VXLAN, 마이크로 세그멘테이션을 활용한 보안 정책을 설정 가능.

**3. Spine-Leaf 구조와 SDN 비교**

| **항목**          | **Cisco ACI (Spine-Leaf)**              | **SDN (소프트웨어 정의 네트워크)**  |
| ----------------- | --------------------------------------- | ----------------------------------- |
| **컨트롤러 역할** | APIC: 정책 정의 및 프로그래밍           | SDN 컨트롤러: 데이터 경로 직접 제어 |
| **데이터 경로**   | Leaf 스위치가 정책 기반으로 트래픽 전달 | 컨트롤러가 패킷 전달 경로 결정      |
| **구조**          | Spine-Leaf 구조 기반                    | 중앙 컨트롤러 기반                  |
| **확장성**        | 매우 우수 (Leaf 추가만으로 확장 가능)   | 비교적 제한적                       |
| **트래픽 처리**   | 분산 구조 (각 Leaf에서 직접 결정)       | 중앙 집중식 컨트롤러 관리           |

➡ **Cisco ACI의 Spine-Leaf 구조는 SDN과 유사하지만, SDN 컨트롤러처럼 직접 데이터 경로를 조작하지 않고, 정책(Policy) 중심으로 동작한다는 점이 가장 큰 차이점.**