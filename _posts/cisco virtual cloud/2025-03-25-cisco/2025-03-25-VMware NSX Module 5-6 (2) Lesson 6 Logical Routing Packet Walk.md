---
title: <h0>VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk</h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-25-cisco]
tags: [Network, Cloud, NSX]

 



---

## **Multitier Routing: Egress to Physical Network (1단계 – 다단계 라우팅 구조)**

![image-20250325111730675](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk/image-20250325111730675.png)

**1. 트래픽 흐름 개요**

• **출발지 VM (10.1.1.10)** → **목적지 (192.168.10.1)** 로 전송

• 패킷은 **기본 게이트웨이 10.1.1.1 (T1 DR)**로 전달됨

• **T1 DR은 하이퍼바이저 내에 분산 형태로 존재**

→ **SR 없음**, 따라서 **모든 트래픽은 T0 DR로 전달되어야 함**



------



**2. 경로 흐름 단계별 정리**

① **VM**에서 **T1 DR (10.1.1.1)**로 패킷 전송

② T1 DR은 **T0-T1 Transit Subnet (100.64.16.0/31)**을 통해

→ **T0 DR (169.254.0.1)**로 트래픽 전달

③ **T0 DR**은 내부 트랜짓 세그먼트(169.254.0.0/24)를 통해

→ **T0 SR (169.254.0.2)**로 패킷 전송

④ T0 SR은 **업링크 세그먼트 (예: 172.16.215.100/29)**를 통해

→ **물리 라우터로 전송**



------



**3. 구성 요소별 역할**

| **구성 요소**               | **설명**                                |
| --------------------------- | --------------------------------------- |
| **T1 DR**                   | 하이퍼바이저 내 분산 라우터 (DR만 존재) |
| **T0 DR**                   | DR 컴포넌트, T1과 연결된 상위 라우터    |
| **T0 SR**                   | Edge Node에 존재, 실제 외부 통신 수행   |
| **Transit Subnet**          | T0-T1 간 연결: 100.64.16.0/31           |
| **Internal Transit Subnet** | T0 DR ↔ T0 SR 연결: 169.254.0.0/24      |





------



**4. 핵심 요약**

• T1에는 **SR이 존재하지 않기 때문에**, 모든 외부 트래픽은

→ T0 DR → T0 SR → 물리 라우터 순으로 전달됨

• T1 DR → T0 DR은 **T0-T1 Transit Subnet (100.64.16.0/31)** 사용

• T0 DR → T0 SR은 **Internal Transit Subnet (169.254.0.0/24)** 사용

• T0 SR은 **NSX Edge Node**에서 외부로 트래픽을 보냄

------

------

## Multitier Routing: Egress to Physical Network (2)

![image-20250325111912843](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk/image-20250325111912843.png)

게이트웨이(T1 DR)는 라우팅 결정을 내리기 위해 자신의 포워딩 테이블을 확인한다. 

192.168.10.0/24 네트워크에 대한 특정 경로가 존재하지 않기 때문에, 패킷은 기본 게이트웨이인 100.64.16.0으로 전송되며, 

이는 동일한 하이퍼바이저에 있는 Tier-0의 DR 인스턴스이다.

------

------

## Multitier Routing: Egress to Physical Network (3)



![image-20250325112346560](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk/image-20250325112346560.png)

패킷은 T0-T1 전송 서브넷을 통해 동일한 하이퍼바이저에 있는 T0 DR 인스턴스로 전송된다.

------

------

## Multitier Routing: Egress to Physical Network (4)

![image-20250325112642857](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk/image-20250325112642857.png)

게이트웨이(T0 DR)는 라우팅 결정을 내리기 위해 자신의 포워딩 테이블을 확인한다. 

패킷은 기본 게이트웨이인 169.254.0.2로 전송되며,

 이는 엣지 노드에 있는 T0 SR 구성 요소이다.

**패킷은 트랜짓 세그먼트를 통해 기본 게이트웨이인 169.254.0.2로 전송된다. 169.254.0.2는 내부 트랜짓 네트워크에 연결된 Tier-0 SR 구성 요소의 인터페이스이다.**

------

------

## Multitier Routing: Egress to Physical Network (5)

![image-20250325112714834](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk/image-20250325112714834.png)

하이퍼바이저에서 엣지 노드로 패킷을 전송하기 위해, 패킷은 Geneve 헤더로 캡슐화된다.

**소스 호스트(TEP 172.16.215.67)는 패킷을 엣지 노드(TEP 172.16.215.124)로 보내기 위해 Geneve 헤더로 캡슐화하며, 원본 패킷은 그대로 유지된다.**

------

------

## Multitier Routing: Egress to Physical Network (6)

![image-20250325113215900](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325113215900.png)

**캡슐화된 패킷은 오버레이 터널을 통해 엣지 노드로 전송된다.**

------

------

## Multitier Routing: Egress to Physical Network (7)

![image-20250325114624729](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325114624729.png)

**엣지 노드는 패킷의 캡슐화를 해제한 후, 이를 자신의 T0 SR 인스턴스로 전송한다.**

------

------

## Multitier Routing: Egress to Physical Network (8)

![image-20250325114709119](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325114709119.png)

게이트웨이(T0 SR)의 라우팅 테이블에는 업링크 세그먼트를 통해 192.168.10.0/24 네트워크로 가는 경로가 표시되어 있다.

**목적지 네트워크 192.168.10.0/24에 도달하기 위해 다음 홉 172.16.215.98이 사용된다.**

------

------

## Multitier Routing: Egress to Physical Network (9)

![image-20250325114805470](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325114805470.png)

**엣지 노드는 패킷을 상위 물리적 게이트웨이로 전송하고, 해당 게이트웨이는 패킷을 목적지인 192.168.10.1로 라우팅한다.**

------

------

## Multitier Routing: Ingress from Physical Network (10)

![image-20250325114905444](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325114905444.png)

**응답 패킷의 경우, 소스 VM인 192.168.10.1은 패킷을 기본 게이트웨이로 전송하고, 해당 게이트웨이는 패킷을 엣지 노드로 라우팅한다.**

------

------

## Multitier Routing: Ingress from Physical Network (11)

![image-20250325114958824](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325114958824.png)

------

 **시나리오 개요**

• **목적지 VM:** 10.1.1.10

• **출발지:** 외부 물리 네트워크 (예: 192.168.10.1)

• **라우팅 대상:** T0 SR → T0 DR → T1 DR → VM

------

**구성 흐름 설명**



① **T0 SR**가 물리 네트워크에서 패킷을 수신함

→ T0 SR은 같은 Edge Node에 있는 **T0 DR**과 **T1 DR**과 라우팅 테이블을 **공유**함



② 따라서 별도의 **Internal Transit Subnet**을 거치지 않고

→ **T1 DR**로 바로 전달됨 (SR과 DR이 **Edge Node 내에 함께 존재**하기 때문)



③ **T1 DR**은 해당 세그먼트의 게이트웨이 역할을 하므로

→ VM (10.1.1.10)으로 트래픽을 최종 전송함

------

**핵심 구성 요소별 연결 요약**

| **컴포넌트**                | **역할**                                       |
| --------------------------- | ---------------------------------------------- |
| **T0 SR**                   | 외부 네트워크에서 인그레스 트래픽 수신         |
| **T0 DR**                   | 경로 공유, 별도 트랜짓 없이 SR과 연결          |
| **T1 DR**                   | 내부 세그먼트 게이트웨이, 최종 전달            |
| **Internal Transit Subnet** | DR(SR 제외)만 있는 하이퍼바이저에서만 사용됨   |
| **Edge Node 내부**          | SR, DR, T1 DR이 모두 존재하므로 직접 전달 가능 |

------

**요약 포인트**

• **T0 SR ↔ T0 DR ↔ T1 DR** 모두 동일한 **Edge Node**에 존재

• **라우팅 테이블 공유**로 인해 성능 최적화됨

• **Internal Transit Subnet**은 하이퍼바이저에 DR만 존재할 때만 사용됨

• 최종적으로 트래픽은 **T1 DR → VM**으로 전달됨

------

------

## Multitier Routing: Ingress from Physical Network (12)

![image-20250325115251411](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325115251411.png)

패킷은 T0-T1 전송 서브넷을 통해 엣지 노드에 있는 T1 DR 인스턴스로 전송된다.

------

------

## Multitier Routing: Ingress from Physical Network (13)

![image-20250325115349450](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325115349450.png)

**게이트웨이(T1 DR)는 라우팅 결정을 내리기 위해 자신의 포워딩 테이블을 확인한다.** 

**Segment 1을 통해 10.1.1.0/24 네트워크로 직접 연결된 경로가 존재하며, 패킷은 원격 호스트로 전송된다.**

------

------

## Multitier Routing: Ingress from Physical Network (14)

![image-20250325115618699](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325115618699.png)

엣지 노드에서 하이퍼바이저로 패킷을 전송하기 위해, 패킷은 Geneve 헤더로 캡슐화된다.

**소스 호스트(TEP 172.16.215.124)는 패킷을 원격 호스트(TEP 172.16.215.67)로 보내기 위해 Geneve 헤더로 캡슐화하며, 원본 패킷은 그대로 유지된다.**

------

------

## Multitier Routing: Ingress from Physical Network (15)

![image-20250325115721913](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325115721913.png)

**캡슐화된 패킷은 오버레이 터널을 통해 엣지 노드로 전송된다.**

------

------

## Multitier Routing: Ingress from Physical Network (16)

![image-20250325115840940](../../../assets/cisco_post_img/2025-03-25-VMware NSX Module 5-6 (2) Lesson 6 Logical Routing Packet Walk//image-20250325115840940.png)

**수신 호스트는 패킷의 캡슐화를 해제하고, 이를 목적지인 VM 10.1.1.10으로 라우팅한다.**



------

------



## **위 라우팅 흐름 요약 (Ingress + Egress)**

------

### **1. VM → 물리 네트워크로 패킷 전송 (Egress Path)**



**[1] VM에서 패킷 생성**

​	•	Source IP: 10.1.1.10 (VM)

​	•	Destination IP: 192.168.10.1 (외부 VM)

​	•	패킷은 기본 게이트웨이 10.1.1.1로 전달됨



**[2] Tier-1 DR 처리**

​	•	기본 게이트웨이 10.1.1.1은 Tier-1 DR에 있음

​	•	Tier-1 DR은 라우팅 테이블을 확인 후 Tier-0 DR로 전달



**[3] T1 DR → T0 DR 전송**

​	•	T0-T1 Transit Subnet (100.64.16.0/31)을 통해 패킷 전달

​	•	T0 DR에 도달한 패킷은 Tier-0 라우팅 테이블 확인



**[4] T0 DR → T0 SR 전달**

​	•	라우팅 테이블에 목적지 네트워크 192.168.10.0/24가 존재

​	•	T0 DR은 Internal Transit Subnet (169.254.0.0/25)을 통해 T0 SR로 패킷 전달



**[5] Overlay encapsulation**

​	•	T0 DR에서 Geneve Encapsulation 수행

​	•	Source TEP: 172.16.215.67

​	•	Destination TEP: 172.16.215.124



**[6] Overlay 네트워크 통해 Edge Node로 전달**

​	•	패킷은 Overlay 네트워크를 통해 Edge Node의 T0 SR에 도달



**[7] T0 SR 처리 및 Northbound 전송**

​	•	Encapsulation 제거 후 실제 Payload (Src: 10.1.1.10, Dst: 192.168.10.1)를 꺼냄

​	•	Uplink 인터페이스를 통해 외부 물리 라우터로 전송됨



------



### **2. 물리 네트워크 → VM으로 패킷 수신 (Ingress Path)**



**[8] 외부 라우터 → T0 SR로 패킷 전달**

​	•	외부 라우터는 목적지 10.1.1.10을 향해 T0 SR로 패킷 전달



**[9] T0 SR → T0 DR 전달**

​	•	같은 Edge Node 내의 SR과 DR은 라우팅 테이블 공유

​	•	Internal Transit Subnet 필요 없음

​	•	T0 SR은 T0 DR로 직접 전달



**[10] T0 DR → T1 DR 전달**

​	•	T0-T1 Transit Subnet (100.64.16.0/31) 사용



**[11] T1 DR → Compute Node의 T1 DR 전달**

​	•	Geneve encapsulation 수행

​	•	Source TEP: 172.16.215.124, Destination TEP: 172.16.215.67



**[12] Overlay 네트워크 통해 Compute Node 전달**

​	•	Encapsulation된 패킷은 Compute Node의 T1 DR로 전달됨



**[13] T1 DR → VM 전달**

​	•	T1 DR에서 최종 목적지 VM 10.1.1.10로 패킷 전달



------



**전체 요약 흐름**

​	1.	VM에서 패킷 생성 → T1 DR 처리

​	2.	T1 DR → T0 DR → T0 SR 경로로 전달

​	3.	T0 SR → 물리 라우터로 패킷 전송

​	4.	외부에서 응답 수신 → T0 SR 수신

​	5.	T0 SR → T0 DR → T1 DR 경유

​	6.	Overlay 통해 Compute Node의 VM까지 전달



------



이 구조는 **NSX-T 멀티티어 라우팅의 기본 흐름**이며, Overlay 네트워크(Geneve)와 Tier 구조(T0/T1 DR & SR)의 상호작용을 기반으로 고가용성과 확장성을 동시에 제공함. 필요 시 ECMP, BFD, OSPF/BGP 등 다양한 고급 기능과 연동 가능함.

------

| **단계** | **방향** | **위치 / 구성요소**     | **동작 내용**                                                |
| -------- | -------- | ----------------------- | ------------------------------------------------------------ |
| 1        | Egress   | VM (10.1.1.10)          | 패킷 생성, 기본 게이트웨이 10.1.1.1로 전달                   |
| 2        | Egress   | Tier-1 DR (Compute)     | 패킷 수신 후 Tier-0 DR로 라우팅                              |
| 3        | Egress   | T1 → T0 Transit Subnet  | 100.64.16.0/31을 통해 T0 DR로 패킷 전달                      |
| 4        | Egress   | Tier-0 DR (Compute)     | Tier-0 SR로 전달 (라우팅 테이블 참조: 기본 게이트웨이로 설정됨) |
| 5        | Egress   | Internal Transit Subnet | 169.254.0.0/25 통해 T0 SR로 전달                             |
| 6        | Egress   | Overlay Encapsulation   | Source TEP → Dest TEP (Geneve Encapsulation) 수행            |
| 7        | Egress   | Overlay Network         | Encapsulated packet 전송 → Edge Node의 T0 SR 도착            |
| 8        | Egress   | Tier-0 SR (Edge)        | Decapsulation 후 외부 물리 라우터로 전송                     |

| **단계** | **방향** | **위치 / 구성요소**   | **동작 내용**                                  |
| -------- | -------- | --------------------- | ---------------------------------------------- |
| 9        | Ingress  | 물리 라우터           | NSX Edge의 T0 SR로 패킷 전달                   |
| 10       | Ingress  | Tier-0 SR (Edge)      | Routing Table 참조 → T0 DR로 직접 전달         |
| 11       | Ingress  | T0 DR (Edge)          | T1 DR로 전달 (T0-T1 Transit Subnet 경유)       |
| 12       | Ingress  | Overlay Encapsulation | TEP Encapsulation (Edge → Compute) 수행        |
| 13       | Ingress  | Overlay Network       | Encapsulated packet 전달 → Compute Node 도착   |
| 14       | Ingress  | Tier-1 DR (Compute)   | Decapsulation 후 VM 10.1.1.10로 패킷 최종 전달 |