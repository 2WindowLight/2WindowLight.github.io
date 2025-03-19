---
title: <h0>VMware NSX Module 5 NSX Logical Routing </h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-19-cisco]
tags: [Network, Cloud, NSX]











---



# Module 5: NSX Logical Routing 

------

## Importance

NSX에서 논리적 라우팅(Logical Routing)은 **동서(East-West) 및 남북(North-South) 트래픽을 최적화하고 확장 가능하게 관리하는 방법**을 제공합니다.

효율적이고 안전한 **Layer 3 네트워크 인프라**를 구축하려면 **NSX 논리적 라우팅 아키텍처, 라우팅 구성 요소 및 라우팅 기능**을 이해해야 합니다.

------

## **모듈 강의 개요**

1. **논리적 라우팅 개요**
2. **NSX Edge 및 Edge 클러스터** 
   * edge - 네트워크 서비스 제공(DHCP, NAT, DNS, L4...) , 라우팅

3. **Tier-0 및 Tier-1 게이트웨이 구성**
4. ***정적 및 동적 라우팅 구성***
5. **ECMP(Equal-Cost Multi-Path[이중화]) 및 고가용성(High Availability)**
6. **논리적 라우팅 패킷 흐름(Packet Walk)**
7. **VRF(virtual routing forwarder) Lite**

------

# Lesson 1: Overview of Logical Routing

------



------

## **논리적 라우팅의 사용 사례**



* **단일(Single) 또는 다중 테넌트(Multitenant) 배포 모델 지원**
  * **tenant:** 사용자 구분
    1. **ISP: 고객, 가입자**
    2. **enterprise(회사): 부서, 지사, 그룹...**
* **테넌트 및 네트워크 분리**
* **컨테이너화된 워크로드를 포함하는 클라우드 환경 솔루션 제공**
* **가상 네트워크에서 최적화된 라우팅 경로 제공 및 라우팅 단순화**
* **데이터 센터에서 분산 라우팅(Distributed Routing) 및 중앙 집중식(edge) 서비스 제공**
* **논리적 네트워크를 물리적 환경까지 확장 가능**

------

## **논리적 라우팅의 사전 요구 사항**

논리적 라우팅이 정상적으로 작동하려면 다음 요구 사항을 충족해야 합니다:

​	•	**NSX 관리 클러스터(NSX Management Cluster)** 가 구성되고 사용 가능해야 함

​	•	**전송 영역(Transport Zones) 및 N-VDS/VDS** 가 생성되어 있어야 함

​	•	**하이퍼바이저가 NSX 전송 노드(Transport Nodes)로 준비되고 관리 플레인(Management Plane)에 추가** 되어야 함

​	•	**전송 노드가 적절한 전송 영역에 연결** 되어야 함

​	•	**각 전송 노드에 N-VDS/VDS 인스턴스가 생성** 되어 있어야 함

​	•	**NSX Edge 노드가 배포되고 요구 사항에 맞게 사전 구성** 되어 있어야 함

------

------

## **Logical Routing in NSX(논리적 라우팅 개요)**

![image-20250319162543029](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5: NSX Logical Routing //image-20250319162543029.png)

------



**1. NSX 게이트웨이 역할**



NSX에서 **논리적 라우팅(Logical Routing)** 을 수행하는 **게이트웨이(Gateway)** 는 다음과 같은 기능을 제공함.

​	•	**North-South 및 East-West 라우팅 지원**

​	•	**정적(Static) 및 동적(Dynamic) 라우팅 프로토콜 지원**

​	•	**멀티 테넌시(Multitenancy) 및 네트워크 분리**

​	•	**고가용성(High Availability)**

​	•	**IPv6 및 멀티캐스트(Multicast) 지원**

​	•	**게이트웨이 방화벽, NAT, VPN 등의 중앙 집중식 서비스 제공**



------



**2. NSX Logical Routing 구조**



NSX에서 논리적 라우팅은 **기존 물리 네트워크에서 수행되던 라우팅을 가상 환경으로 재현**하는 방식으로 동작.

​	1.	**NSX의 분산 라우팅(Distributed Routing)**

​	•	**라우팅 기능이 물리적 하드웨어에서 분리**되어 가상 환경에서 실행됨.

​	•	**각 ESXi 호스트의 전송 노드(Transport Node)에서 로컬 라우팅을 수행**하여 불필요한 트래픽 이동을 최소화함.

​	•	기본적인 **패킷 전달(Forwarding) 결정이 로컬에서 실행되므로 성능이 향상**됨.

​	2.	**NSX Edge 노드에서 수행하는 L3 기능**

​	•	**NSX Edge 노드는 NAT, VPN 등 Layer 3 서비스 제공**.

​	•	Edge 노드를 통해 **외부 네트워크와 연결하는 North-South 트래픽을 처리**.

​	•	다수의 **게이트웨이 인스턴스를 설치하여 멀티 테넌시 및 네트워크 분리 지원**.



------



**3. NSX 라우팅 흐름**



**1) North-South Routing (북-남 라우팅)**

​	•	**테넌트(Tenant) 네트워크에서 외부(public) 네트워크로의 연결을 지원**.

​	•	**외부 인터넷 또는 외부 네트워크와의 통신**이 필요한 트래픽이 해당됨.

​	•	**Edge 게이트웨이(Edge Node)를 통해 외부 네트워크와 연동됨**.



**2) East-West Routing (동-서 라우팅)**

​	•	**동일한 테넌트(Tenant) 내에서 논리적 네트워크 간 트래픽을 라우팅**.

​	•	**같은 관리 도메인 내에서 세그먼트(Segment) 간 이동하는 트래픽**.

​	•	**NSX의 분산 라우팅(Distributed Routing) 기능을 사용하여 각 ESXi 호스트에서 직접 처리**.



------



**4. NSX Logical Routing이 제공하는 이점**

| **기존 물리 라우팅**                                         | **NSX Logical Routing 도입 후**                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **라우팅을 위해 물리적 라우터를 거쳐야 함** → 트래픽 지연 발생 | **분산 라우팅을 통해 ESXi 노드에서 직접 처리** → 성능 최적화 |
| **North-South 트래픽이 물리 네트워크로 집중** → 네트워크 병목 현상 발생 | **East-West 라우팅을 통해 내부 트래픽을 최적화**             |
| **물리적 하드웨어에 종속적** → 하드웨어 장애 시 서비스 영향 큼 | **논리적 라우팅이 소프트웨어 기반으로 실행** → 장애 영향 최소화 |
| **VLAN 기반 네트워크 분리 한계(4096개 제한)**                | **Overlay 기반 네트워크로 수백만 개의 VNI 생성 가능**        |





------



**5. 결론**



NSX의 Logical Routing을 사용하면 **기존 물리적 네트워크의 라우팅 한계를 극복하고, Overlay 네트워크를 활용하여 확장성과 보안성을 강화할 수 있음**.

​	•	**North-South 및 East-West 트래픽 최적화**

​	•	**분산 라우팅(Distributed Routing)으로 성능 향상**

​	•	**멀티 테넌시 및 보안 정책 적용 가능**

​	•	**Overlay 네트워크를 활용하여 유연한 네트워크 운영 가능**



즉, **클라우드 환경 및 SDN 기반 네트워크 운영을 위한 필수 요소**로 활용됨.



------

------







------

------

## **Gateway Components: Distributed Router(DR) & Service Router(SR)** [중요]

![image-20250319162628092](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5: NSX Logical Routing //image-20250319162628092.png)

------



**1. NSX 게이트웨이의 주요 구성 요소**



NSX에서 논리적 라우팅을 수행하는 **게이트웨이(Gateway)** 는 **분산 라우터(Distributed Router, DR)** 와 **서비스 라우터(Service Router, SR)** 두 가지 주요 구성 요소로 나뉨.

​	•	**DR(Distributed Router, 분산 라우터)**: **East-West(동-서) 트래픽을 처리**

​	•	**SR(Service Router, 서비스 라우터)**: **North-South(북-남) 트래픽 및 중앙 집중식 서비스 제공**



------



**2. Distributed Router(DR)**



**1) DR의 특징**

​	•	**기본적인 패킷 포워딩 기능을 제공**.

​	•	**모든 전송 노드(ESXi 및 Edge 노드)에 걸쳐 실행됨**.

​	•	**ESXi 하이퍼바이저의 커널 모듈(Kernel Module)로 실행**되며, 호스트 단에서 라우팅을 수행.

​	•	**워크로드의 퍼스트 홉 라우팅(First-hop routing) 기능 제공**.

​	•	**East-West 트래픽을 최적화하여 물리적 네트워크 부하 감소**.

​       	호스트에 분산된 라우팅 제공



**2) DR의 주요 역할**

​	•	**세그먼트 간 트래픽을 라우팅**하여 동일한 테넌트 내에서 통신을 최적화.

​	•	**모든 전송 노드(Transport Node)에서 실행되므로 성능이 향상됨**.

​	•	**각 호스트에서 라우팅을 처리하여, 패킷이 외부 네트워크를 거치지 않도록 함**.



------



**3. Service Router(SR)** = edge



**1) SR의 특징**

​	•	**North-South 트래픽(외부 네트워크와의 연결) 처리**.

​	•	**NAT(Network Address Translation), VPN, 방화벽 등의 중앙 집중식 서비스 제공**.

​	•	**외부 네트워크 연결을 위한 업링크(Uplink) 필요 [vlan]**.

​	•	**Edge Transport Node에 배포되어 실행**됨.



**2) SR의 주요 역할**

​	•	**인터넷 또는 외부 네트워크와 연결되는 트래픽을 처리**.

​	•	**NAT 및 방화벽 정책 적용**을 통해 보안 및 네트워크 격리 유지.

​	•	**BGP 또는 OSPF와 같은 동적 라우팅 프로토콜을 실행하여 경로 학습**.



------



**4. DR과 SR의 차이점 비교**

| **구분**        | **Distributed Router (DR)**      | **Service Router (SR)**            |
| --------------- | -------------------------------- | ---------------------------------- |
| **기능**        | East-West 트래픽 처리            | North-South 트래픽 처리            |
| **위치**        | 모든 전송 노드(ESXi & Edge)      | Edge Transport Node                |
| **라우팅 방식** | 분산 라우팅                      | 중앙 집중식 라우팅                 |
| **주요 역할**   | 세그먼트 간 트래픽 최적화        | 외부 네트워크 연결 및 NAT/VPN 처리 |
| **운영 방식**   | 각 ESXi 노드에서 개별적으로 실행 | Edge 노드에서 실행                 |





------



**5. 결론**



NSX에서 **DR(분산 라우터)과 SR(서비스 라우터)를 결합하여 트래픽을 최적화**하고, **클라우드 네트워크 환경에서 보다 유연하고 확장 가능한 네트워크 인프라를 구축**할 수 있음.

​	•	**DR은 각 호스트에서 실행되며 East-West 트래픽을 최적화**하여 물리적 네트워크의 부하를 줄임.

​	•	**SR은 Edge 노드에서 실행되며 North-South 트래픽을 처리하고, 외부 네트워크와의 연결을 담당**.

​	•	**두 가지 라우터 구조를 조합하여 성능과 보안이 강화된 NSX 네트워크를 운영 가능**.

------

------





------

------

## **NSX Gateway 인터페이스 개요 [중요]**

![image-20250319162641253](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5: NSX Logical Routing //image-20250319162641253.png)





NSX의 논리적 라우터(LR) 및 게이트웨이에는 **여러 유형의 인터페이스**가 사용되며, 각 인터페이스는 특정 트래픽 흐름을 지원하는 역할을 수행함.



------



**1. Gateway 인터페이스의 유형**

| **인터페이스 유형**        | **설명**                                                     | **주요 역할**                                                |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Uplink Interface**       | Tier-0 Gateway가 물리적 네트워크(외부 장비- ex) 물리 라우터)와 연결 | - 외부 네트워크(BGP, OSPF 피어링) 연결 - IPSec VPN, NAT 등의 트래픽 처리 |
| **Downlink Interface**     | Tier-1 Gateway가 논리적 스위치(Segment)와 연결               | - VM 및 워크로드 네트워크 연결 - 서브넷의 기본 게이트웨이 역할 |
| **RouterLink Port**        | Tier-0 Gateway와 Tier-1 Gateway 간 연결                      | - T0 ↔ T1 라우팅 기능 제공 - 기본적으로 100.64.0.0/16 대역 사용 |
| **Intratier Transit Link** | 동일한 Gateway 내 **DR과 SR을 연결**하는 내부 링크           | - DR(Distributed Router) ↔ SR(Service Router) 간 트래픽 전달 - 169.254.0.0/24 대역 사용 |
| **Service Interface**      | VLAN 기반 서비스 및 파트너 서비스 리디렉션을 위한 특수 인터페이스 | - VLAN 네트워크를 위한 방화벽, NAT, VPN 기능 제공 - 파트너 네트워크 트래픽 리디렉션 |





------



**2. NSX 논리 라우팅 아키텍처에서의 인터페이스 역할**



**1) Uplink Interface**

​	•	**외부 물리적 네트워크와 Tier-0 Gateway를 연결**하는 인터페이스.

​	•	**BGP 및 OSPF 피어링**을 위한 연결 지점.

​	•	**IPSec VPN 트래픽을 처리**하며, 외부 서비스와의 연결을 담당.



**2) Downlink Interface**

​	•	**Tier-1 Gateway와 논리적 스위치(Segment) 연결**.

​	•	**VM 및 워크로드 네트워크가 라우팅을 사용할 수 있도록 기본 게이트웨이 역할 수행**.

​	•	동일한 테넌트 내에서 세그먼트 간 통신을 가능하게 함.



**3) RouterLink Port**

​	•	**Tier-0 Gateway와 Tier-1 Gateway 간 연결을 담당하는 인터페이스**.

​	•	내부 논리적 스위치를 통해 자동 생성됨.

​	•	기본적으로 100.64.0.0/16 대역이 할당됨.



**4) Intratier Transit Link**

​	•	**동일한 논리적 라우터 내에서 DR과 SR 간 트래픽을 전달하는 내부 링크**.

​	•	SR이 필요한 **NAT, 방화벽, VPN과 같은 서비스가 활성화된 경우 자동 생성됨**.

​	•	기본적으로 169.254.0.0/24 서브넷을 사용.



**5) Service Interface**

​	•	**VLAN 기반 네트워크를 위한 특수 인터페이스**.

​	•	**North-South 트래픽 리디렉션 및 보안 정책 적용**.

​	•	**방화벽, NAT, VPN과 같은 서비스 적용 가능**.



------



**3. 결론**

​	•	**Uplink 인터페이스** → 외부 네트워크 연결.

​	•	**Downlink 인터페이스** → 논리적 네트워크(Segment) 연결.

​	•	**RouterLink 포트** → Tier-0 ↔ Tier-1 간 라우팅 제공.

​	•	**Intratier Transit Link** → 동일한 게이트웨이 내 DR ↔ SR 간 연결.

​	•	**Service Interface** → VLAN 서비스 및 보안 정책 적용.



이러한 구조를 통해 **NSX 환경에서 동-서(East-West) 및 남-북(North-South) 트래픽을 효율적으로 처리하고, 다중 테넌트 및 확장성을 지원**할 수 있음.
