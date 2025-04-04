---
title: <h0>VMware NSX Module 5 NSX Logical Routing</h0>
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

![image-20250319163431865](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5 NSX Logical Routing//image-20250319163431865.png)

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

1. **NSX의 분산 라우팅(Distributed Routing)**
   * **라우팅 기능이 물리적 하드웨어에서 분리**되어 가상 환경에서 실행됨.
   * **각 ESXi 호스트의 전송 노드(Transport Node)에서 로컬 라우팅을 수행**하여 불필요한 트래픽 이동을 최소화함.
   * 기본적인 **패킷 전달(Forwarding) 결정이 로컬에서 실행되므로 성능이 향상**됨.

2. **NSX Edge 노드에서 수행하는 L3 기능**
   * **NSX Edge 노드는 NAT, VPN 등 Layer 3 서비스 제공**.
   * Edge 노드를 통해 **외부 네트워크와 연결하는 North-South 트래픽을 처리**.
   * 다수의 **게이트웨이 인스턴스를 설치하여 멀티 테넌시 및 네트워크 분리 지원**.



------



3. **NSX 라우팅 흐름**

   1. **North-South Routing (북-남 라우팅)**
      * **테넌트(Tenant) 네트워크에서 외부(public) 네트워크로의 연결을 지원**.
      * **외부 인터넷 또는 외부 네트워크와의 통신**이 필요한 트래픽이 해당됨.
      * **Edge 게이트웨이(Edge Node)를 통해 외부 네트워크와 연동됨**.

   2. **East-West Routing (동-서 라우팅)**
      * **동일한 테넌트(Tenant) 내에서 논리적 네트워크 간 트래픽을 라우팅**.
      * **같은 관리 도메인 내에서 세그먼트(Segment) 간 이동하는 트래픽**.
      * **NSX의 분산 라우팅(Distributed Routing) 기능을 사용하여 각 ESXi 호스트에서 직접 처리**.



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

## **NSX Tier-0 및 Tier-1 게이트웨이 개요**



NSX에서는 **Tier-0 게이트웨이**와 **Tier-1 게이트웨이**를 사용하여 논리 라우팅을 제공하며, 각각의 역할과 기능이 다름.



------



1. **Tier-0 Gateway (T0)**

   * **주요 역할**: **North-South 트래픽 처리**
     * 물리 네트워크(외부)와 논리 네트워크(가상 네트워크) 간의 라우팅을 담당.
     * 외부 물리적 게이트웨이(라우터)와 연결됨.

   * **기능**
     * **정적 라우팅 및 동적 라우팅(BGP, OSPF, IS-IS) 지원**
     * **Equal-Cost Multi-Path (ECMP) 라우팅 지원** → 성능 및 가용성 향상.
     * **NSX Edge 노드 필요** → 외부 네트워크와의 연결을 위해 필수.

   * **운영 및 관리**
     * NSX 인프라 관리자(Provider)가 설정 및 관리.
     * 모든 하이퍼바이저에서 Tier-0 DR(Distributed Router) 실행.
     * North-South 트래픽을 처리하기 위해 NSX Edge 노드에서 SR(Service Router) 실행.



------



2. **Tier-1 Gateway (T1)**

   * **주요 역할**: **East-West 트래픽 처리**
     * 동일한 테넌트(tenant) 내의 **세그먼트 간 트래픽을 라우팅**.
     * 외부 네트워크 연결 필요 시 **Tier-0 게이트웨이와 연결됨**.

   * **기능**
     * **동적 라우팅 미지원 (정적 라우팅만 사용)**
     * **ECMP 미지원** - 3.0 이상 지원
     * NSX Edge 노드 없이도 동작 가능 **(단, 중앙 집중식 서비스 사용 시 NSX Edge 필요)**.

   * **운영 및 관리**
     * **테넌트(Tenant) 단위에서 개별적으로 관리**.
     * 필요에 따라 **NAT, 방화벽(FW), VPN 등의 서비스 적용 가능**.



------



3. **NSX 논리 라우팅 모델**

   * **Tier-0 Gateway**
     * 외부 물리 네트워크와 직접 연결됨.
     * Tier-1 게이트웨이에서 수신한 트래픽을 외부 네트워크로 전달.

   * **Tier-1 Gateway**
     * 내부 세그먼트 간 트래픽(East-West) 라우팅 수행.
     * Tier-0 게이트웨이와 연결될 경우 **North-South 트래픽 전송 가능**.



------



**4. 비교 요약**

| **구분**                       | **Tier-0 Gateway**                         | **Tier-1 Gateway**          |
| ------------------------------ | ------------------------------------------ | --------------------------- |
| **관리 주체**                  | 인프라 관리자(Provider)                    | 테넌트(Tenant)              |
| **라우팅 방식**                | 동적 라우팅(BGP, OSPF) 및 정적 라우팅 지원 | 정적 라우팅만 지원          |
| **ECMP 지원**                  | 지원                                       | 미지원                      |
| **트래픽 방향**                | North-South 라우팅(외부 연결)              | East-West 라우팅(내부 연결) |
| **NSX Edge cluster 필요 여부** | 필수                                       | 서비스 사용 시 필요         |





------



5. **결론**
   * **Tier-0 Gateway**는 **North-South 트래픽을 처리하며, 외부 네트워크와의 연결을 담당**.
   * **Tier-1 Gateway**는 **East-West 트래픽을 관리하며, 필요 시 Tier-0을 통해 외부 연결 제공**.
   * **NAT, VPN, 방화벽과 같은 서비스는 Edge 노드에서 실행됨**.
   * **이러한 구조를 통해 멀티 테넌트 환경과 확장성을 고려한 고가용성 네트워크 아키텍처를 구축할 수 있음**.





------

------

## **Gateway Components: Distributed Router(DR) & Service Router(SR)** [중요]

 ![image-20250319163502520](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5 NSX Logical Routing//image-20250319163502520.png)

------



**1. NSX 게이트웨이의 주요 구성 요소**



NSX에서 논리적 라우팅을 수행하는 **게이트웨이(Gateway)** 는 **분산 라우터(Distributed Router, DR)** 와 **서비스 라우터(Service Router, SR)** 두 가지 주요 구성 요소로 나뉨.

​	•	**DR(Distributed Router, 분산 라우터)**: **East-West(동-서) 트래픽을 처리**

​	•	**SR(Service Router, 서비스 라우터)**: **North-South(북-남) 트래픽 및 중앙 집중식 서비스 제공**



------



2. **Distributed Router(DR)**

   1. **DR의 특징**
      * **기본적인 패킷 포워딩 기능을 제공**.
      * **모든 전송 노드(ESXi 및 Edge 노드)에 걸쳐 실행됨**.
      * **ESXi 하이퍼바이저의 커널 모듈(Kernel Module)로 실행**되며, 호스트 단에서 라우팅을 수행.
      * **워크로드의 퍼스트 홉 라우팅(First-hop routing) 기능 제공**.
      * **East-West 트래픽을 최적화하여 물리적 네트워크 부하 감소**.
      * **호스트에 분산된 라우팅 제공**

   2. DR의 주요 역할
      * **세그먼트 간 트래픽을 라우팅**하여 동일한 테넌트 내에서 통신을 최적화.
      * **모든 전송 노드(Transport Node)에서 실행되므로 성능이 향상됨**.
      * **각 호스트에서 라우팅을 처리하여, 패킷이 외부 네트워크를 거치지 않도록 함**.



------



3. **Service Router(SR) = edge**

   1. **SR의 특징**
      * **North-South 트래픽(외부 네트워크와의 연결) 처리**.
      * **NAT(Network Address Translation), VPN, 방화벽 등의 중앙 집중식 서비스 제공**.
      * **외부 네트워크 연결을 위한 업링크(Uplink) 필요 [vlan]**.
      * **Edge Transport Node에 배포되어 실행**됨.

   2. **SR의 주요 역할**
      * **인터넷 또는 외부 네트워크와 연결되는 트래픽을 처리**.
      * **NAT 및 방화벽 정책 적용**을 통해 보안 및 네트워크 격리 유지.
      * **BGP 또는 OSPF와 같은 동적 라우팅 프로토콜을 실행하여 경로 학습**.



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

## **싱글 티어(Single-Tier) 토폴로지 개요**

![image-20250319175943224](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5 NSX Logical Routing//image-20250319175943224.png)





싱글 티어 토폴로지는 **NSX 환경에서 간단한 네트워크 계층 구조를 제공**하며, **Tier-0 게이트웨이만 포함**하고 **Tier-1 게이트웨이를 사용하지 않음**.



------



**1. 싱글 티어 토폴로지의 특징**

| **특징**                     | **설명**                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| **Tier-0 게이트웨이만 존재** | 네트워크 트래픽을 직접 Tier-0에서 처리                       |
| **세그먼트 직접 연결**       | 모든 세그먼트(Segment A, Segment B 등)는 **Tier-0 게이트웨이에 직접 연결** |
| **Tier-1 게이트웨이 미사용** | **라우팅 계층을 단순화**하며, **East-West 트래픽 처리를 위한 추가 계층 불필요** |
| **서비스 프로바이더 관리**   | **업스트림(Upstream) 네트워크 연결을 서비스 제공자가 담당**  |
| **테넌트(Tenant) 관리**      | **세그먼트 연결 및 내부 트래픽(남쪽 방향, Southbound)을 테넌트가 관리** |





------



**2. 싱글 티어 토폴로지의 동작 방식**

​	•	**모든 세그먼트(예: Segment A, Segment B)** 는 **Tier-0 게이트웨이와 직접 연결됨**.

​	•	**North-South 트래픽** 은 **Tier-0을 통해 외부 네트워크로 전송됨**.

​	•	**East-West 트래픽** 은 **같은 Tier-0 게이트웨이를 통해 라우팅됨**.



------



**3. 싱글 티어 토폴로지의 장점**



**단순한 구조** → 추가적인 Tier-1 구성 없이 빠르게 배포 가능

**라우팅 성능 향상** → 트래픽이 추가적인 계층을 거치지 않고 빠르게 전달됨

**관리 용이** → 단일 Tier-0 게이트웨이만 운영하면 되므로 **운영 부담이 감소**



------



**4. 싱글 티어 토폴로지의 단점**



**유연성 부족** → 멀티 테넌시 환경에서는 적합하지 않음

**보안 및 서비스 기능 제한** → **NAT, 방화벽 등의 고급 기능을 사용하려면 Tier-1 게이트웨이가 필요**

**확장성 제한** → 네트워크 규모가 커질 경우 **멀티 티어(Tier-0 + Tier-1) 토폴로지가 더 적합**



------



**5. 싱글 티어 토폴로지를 선택하는 경우**



✔ **소규모 환경** → 트래픽이 많지 않은 단순한 네트워크 구성

✔ **빠른 배포 필요** → 복잡한 라우팅 계층 없이 간단한 연결 제공

✔ **단일 테넌트 환경** → 다중 테넌트 격리가 필요하지 않은 경우



→ **결론**: 싱글 티어 토폴로지는 **간단한 네트워크 환경에서 빠른 배포 및 성능 최적화가 필요할 때 적합**. 그러나 **멀티 테넌시 및 보안 기능을 강화하려면 멀티 티어(Tier-1 포함) 구성이 필요할 수 있음**.

------

------

## **NSX 멀티티어 토폴로지 (Multitier Topology) 개요**

![image-20250319172500911](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5 NSX Logical Routing//image-20250319172500911.png)

NSX에서는 **멀티티어 라우팅 구조**를 통해 논리 네트워크를 계층적으로 분리하고 관리할 수 있음. 이를 통해 **테넌트(tenant)와 인프라(provider) 간의 네트워크 구성을 유연하게 관리**할 수 있음.



------



1. **멀티티어 토폴로지 개념**

   * **Tier-0 게이트웨이(T0 Gateway)**:
     * **North-South 트래픽**을 관리하며, 외부 물리 네트워크와 연결됨.
     * **Provider(인프라 관리자)**가 소유 및 관리.

   * **Tier-1 게이트웨이(T1 Gateway)**:
     * 내부 **East-West 트래픽**을 관리하며, 내부 네트워크(세그먼트) 간 라우팅을 제공.
     * **Tenant(테넌트)**가 소유 및 관리.
     * 필요 시 Tier-0 게이트웨이와 연결하여 외부 네트워크와의 연결 제공.

   * **Segments (논리 네트워크)**
     * 각 Tier-1 게이트웨이에 연결됨.
     * 같은 Tier-1 게이트웨이에 속한 세그먼트 간 트래픽은 **East-West 트래픽**으로 간주됨.
     * 서로 다른 Tier-1 게이트웨이에 속한 세그먼트 간 트래픽은 **Tier-0을 통해 라우팅됨**.



------



**2. 멀티티어 토폴로지의 특징**

​	•	**이중 계층 라우팅 구조** → Provider와 Tenant 간의 네트워크 관리 분리 가능.

​	•	**확장성과 보안성 향상** → 테넌트별 라우팅 정책 적용 가능.

​	•	**동적 경로 설정 지원** → BGP, OSPF 등을 통한 네트워크 최적화 가능.



------



**3. 멀티티어 토폴로지 구조**

| **계층**                | **역할 및 기능**                               | **관리 주체**            |
| ----------------------- | ---------------------------------------------- | ------------------------ |
| **Tier-0 Gateway (T0)** | 외부 물리 네트워크와 연결 (North-South 라우팅) | Provider (인프라 관리자) |
| **Tier-1 Gateway (T1)** | 내부 네트워크 간 연결 (East-West 라우팅)       | Tenant (테넌트)          |
| **Segments**            | VM 및 워크로드가 연결되는 네트워크             | Tenant (테넌트)          |





------



4. **멀티티어 토폴로지 vs 단일 계층 토폴로지**

   * **멀티티어 토폴로지**
     * **Provider와 Tenant 간 네트워크 분리**가 필요한 환경에 적합.
     * 멀티 테넌트 환경을 위한 **네트워크 격리 제공**.
     * T0을 통해 **동적 라우팅 및 고가용성(HA) 제공**.

   * **단일 계층 토폴로지**
     * Provider와 Tenant 분리가 필요 없을 경우 사용.
     * **간단한 네트워크 구조**를 원할 때 적합.
     * 모든 네트워크 세그먼트가 **Tier-0과 직접 연결**됨.

------



**5. 결론**

​	•	**멀티티어 라우팅**은 **대규모 클라우드 환경에서 테넌트별 네트워크 분리를 위한 최적의 구성**.

​	•	**Tier-1 게이트웨이는 테넌트가 관리**, **Tier-0 게이트웨이는 인프라 관리자가 운영**.

​	•	**확장성과 보안성을 고려한 네트워크 설계 시 멀티티어 토폴로지를 고려**하는 것이 유리함.

------

------

## **NSX Edge 노드 및 Edge 클러스터 개요**

![image-20250319172634855](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5 NSX Logical Routing//image-20250319172634855.png)



NSX에서 **Edge 노드(Edge Node)** 및 **Edge 클러스터(Edge Cluster)**는 네트워크의 **외부 연결 및 게이트웨이 서비스**를 제공하는 중요한 요소임.

**참고) edge 는 t0 에 최소 한 개는 있어야한다.**

------



**1. Edge 노드(Edge Node) 개념**

​	•	NSX에서 **외부 네트워크와 내부 네트워크를 연결**하는 역할을 함.

​	•	**Tier-0 게이트웨이를 호스팅**하여 물리 네트워크와의 연결을 제공.

​	•	**중앙 집중식(stateful) 네트워크 서비스**를 실행 (예: NAT, VPN, 방화벽).

​	•	**동적 라우팅 프로토콜(BGP, OSPF 등)** 을 실행하여 외부 네트워크와 연동.



**Edge 노드의 주요 기능**

| **기능**                    | **설명**                                              |
| --------------------------- | ----------------------------------------------------- |
| **외부 네트워크 연결**      | Tier-0 게이트웨이를 통해 외부 네트워크와 통신         |
| **중앙 집중식 서비스 제공** | NAT, VPN, 방화벽, 로드 밸런싱 수행                    |
| **동적 라우팅 지원**        | BGP, OSPF를 사용하여 물리 네트워크와 동적 라우팅 수행 |





------



**2. Edge 클러스터(Edge Cluster) 개념**

​	•	**Edge 노드들의 그룹**으로 **확장성 및 고가용성(HA)** 을 제공.

​	•	**NSX 게이트웨이의 동적 라우팅과 서비스 실행을 위한 컴퓨팅 리소스를 제공**.

​	•	**최대 10개의 Edge 노드**를 포함할 수 있음.

​	•	**하나의 Edge 노드에 하나의 Tier-0 게이트웨이만 배포 가능**.



**Edge 클러스터의 주요 특징**

| **기능**             | **설명**                                                     |
| -------------------- | ------------------------------------------------------------ |
| **고가용성(HA)**     | 여러 개의 Edge 노드가 클러스터를 형성하여 **서비스 이중화 및 장애 조치(Failover) 가능** |
| **확장성**           | Edge 노드를 추가하여 **트래픽 처리량 증가 가능**             |
| **다이나믹 라우팅**  | Edge 노드가 동적 라우팅을 통해 외부 네트워크와 연동          |
| **NSX Gateway 지원** | Tier-0 및 Tier-1 게이트웨이를 실행하는 환경 제공             |





------



3. **Edge 노드 및 Edge 클러스터 구성 방식**
   1. **Edge 노드는 반드시 Edge 클러스터에 속해야 함**.
   2. **하나의 Edge 클러스터에는 최대 10개의 Edge 노드 포함 가능**.
   3. **각 Edge 노드는 하나의 Tier-0 게이트웨이만 실행 가능**.
   4. **클러스터 내 Edge 노드 간의 Active-Active 또는 Active-Standby 구성 가능**.



------



**4. Edge 노드와 NSX 게이트웨이 관계**

​	•	**Tier-0 게이트웨이**는 **Edge 노드에서 실행되며, 외부 네트워크와의 연결을 담당**.

​	•	**Tier-1 게이트웨이**는 **기본적으로 Edge 노드를 필요로 하지 않지만, 특정 서비스(NAT, VPN 등)를 실행할 경우 Edge 노드가 필요함**.



------



**5. 결론**

​	•	**NSX Edge 노드는 외부 네트워크와 NSX 네트워크 간의 라우팅 및 서비스를 제공하는 필수 요소**.

​	•	**Edge 클러스터는 다수의 Edge 노드를 묶어 확장성과 고가용성을 제공**.

​	•	**효율적인 NSX 네트워크 설계를 위해 Edge 노드와 Edge 클러스터의 역할을 이해하는 것이 중요함**.



------

------

## **게이트웨이 구성 요소: 분산 라우터(DR) 및 서비스 라우터(SR) (2)**

![image-20250319175608079](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5 NSX Logical Routing//image-20250319175608079.png)





NSX에서 **게이트웨이(Gateway)** 는 **Tier-0 또는 Tier-1** 로 구성되며, 각각 다른 네트워크 역할을 수행함.



------



**1. Tier-0 및 Tier-1 게이트웨이의 역할**

| **게이트웨이 유형**   | **기능**                                          |
| --------------------- | ------------------------------------------------- |
| **Tier-0 게이트웨이** | **North-South 트래픽 처리** 및 물리 네트워크 연결 |
| **Tier-1 게이트웨이** | **East-West 트래픽 처리** 및 내부 라우팅          |

​	•	**단일 계층(Single-Tier) 토폴로지**:

​	•	**Tier-0 게이트웨이**가 **North-South 및 East-West** 트래픽을 모두 처리함.

​	•	**이중 계층(Multi-Tier) 토폴로지**:

​	•	**Tier-1 게이트웨이**가 내부 라우팅을 담당하고, **Tier-0 게이트웨이**가 외부 네트워크로 연결됨.



------



**2. DR(Distributed Router) 및 SR(Service Router) 구성**

| **라우터 유형**            | **기능**                                   | **위치**                          |
| -------------------------- | ------------------------------------------ | --------------------------------- |
| **DR(Distributed Router)** | **패킷 포워딩을 분산하여 처리**            | 모든 **ESXi 호스트 및 Edge 노드** |
| **SR(Service Router)**     | **NAT, VPN, 방화벽 등의 중앙 서비스 제공** | **Edge 노드에서만 실행**          |

* **DR(분산 라우터)**
  * 모든 **NSX 호스트(ESXi 포함)** 에 분산되어 동작.
  * **라우팅 및 패킷 전달을 분산하여 네트워크 부하를 감소**.
  * **게이트웨이가 생성될 때 자동으로 생성됨**.

* **SR(서비스 라우터)**
  * **NAT, VPN, 방화벽과 같은 중앙 집중식 서비스 제공**.
  * **Uplink 연결을 위해 필요함**.
  * **Edge 노드에서만 실행**되며, **게이트웨이가 Edge 클러스터와 연결될 때 자동 생성됨**.



------



3. **DR과 SR의 동작 방식**

   1. **Tier-0 게이트웨이**
      * 기본적으로 **DR이 포함**되어 내부 라우팅을 처리.
      * **외부 네트워크 연결이 필요할 경우** SR이 자동 생성됨.
      * **BGP 및 OSPF 같은 동적 라우팅 프로토콜을 지원**.

   2. **Tier-1 게이트웨이**
      * 기본적으로 **DR이 포함**되어 내부 세그먼트 간 라우팅을 처리.
      * **NAT 또는 방화벽 같은 서비스가 필요할 경우** SR이 자동 생성됨.
      * **Edge 클러스터 없이도 DR이 동작하지만, 중앙 집중식 서비스가 필요할 경우 Edge가 필요함**.



------



**4. DR과 SR의 관계**

​	•	**DR은 모든 NSX 호스트에서 실행**되어 **분산 포워딩 수행**.

​	•	**SR은 Edge 노드에서만 실행**되며, **서비스(NAT, VPN 등) 및 Uplink 처리**.

​	•	**두 라우터는 NSX에서 자동으로 TransitLink를 생성하여 연결됨**.



------



**5. 결론**

​	•	**Tier-0 게이트웨이 → North-South 트래픽 처리 및 동적 라우팅 지원**.

​	•	**Tier-1 게이트웨이 → East-West 트래픽 처리 및 내부 라우팅 담당**.

​	•	**DR → 모든 호스트에서 분산 포워딩 수행 (항상 존재함)**.

​	•	**SR → Edge 노드에서 실행되며, 서비스(NAT, VPN 등) 및 Uplink 처리**.



→ **NSX 네트워크 설계를 할 때, DR과 SR의 역할을 정확히 이해하는 것이 중요함**.

------

------

## **NSX 논리 라우팅: DR(Distributed Router) 및 SR(Service Router) 실현 방식**

![image-20250319170729584](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5 NSX Logical Routing//image-20250319170729584.png)





**NSX의 논리 라우팅 아키텍처에서는 DR(Distributed Router)과 SR(Service Router)이 서로 다른 방식으로 배포되며, 각자의 역할을 수행함.**



------



**1. DR(Distributed Router)과 SR(Service Router)의 차이점**

| **구분**        | **DR (Distributed Router)**                      | **SR (Service Router)**                                    |
| --------------- | ------------------------------------------------ | ---------------------------------------------------------- |
| **주요 역할**   | 동-서(East-West) 트래픽 처리                     | 남-북(North-South) 트래픽 처리                             |
| **배포 위치**   | 모든 **호스트 및 Edge Transport Node**에 배포됨  | **Edge Transport Node**에만 배포됨                         |
| **라우팅 방식** | **호스트 커널에서 실행** → 분산된 L3 라우팅 제공 | **Edge Transport Node에서 실행** → 중앙 집중식 서비스 처리 |
| **트래픽 흐름** | **로컬에서 직접 패킷 포워딩 수행**               | **NAT, VPN, 방화벽, DHCP 등의 서비스 처리**                |
| **라우팅 기능** | 기본적인 라우팅 및 L3 스위칭 기능 제공           | SNAT, DNAT, BGP, OSPF 등 외부 연결 처리                    |





------



2. **DR과 SR의 실현 방식**

   * **Distributed Router(DR) 인스턴스**
     * 호스트 및 Edge Transport Node에 분산 배치됨.
     * 각 호스트에서 **VM 간 트래픽을 직접 라우팅**하여 성능을 최적화함.
     * 라우팅 테이블을 기반으로 호스트 내부에서 직접 L3 패킷을 전달.

   * **Service Router(SR) 인스턴스**
     * **Edge Transport Node에만 배포됨.**
     * NAT, VPN, 방화벽(FW)과 같은 **중앙 집중식 네트워크 서비스** 처리.
     * 외부 네트워크(인터넷)와의 **North-South 트래픽을 처리**.



------



**3. DR과 SR 간 연결**

​	•	**TransitLink 포트**

​	•	SR과 DR 간의 연결은 **TransitLink 인터페이스**를 통해 자동으로 설정됨.

​	•	이 포트를 통해 DR이 처리할 수 없는 **North-South 트래픽이 SR로 전달됨.**

​	•	기본적으로 **169.254.0.0/24 서브넷**이 할당됨.



------



4. **아키텍처 개요**

   1. **Compute Cluster 내 DR 배포**
      * 모든 호스트(ESXi, Edge Transport Node)에서 DR이 활성화됨.
      * VM 간 L3 통신을 최적화하여 분산 라우팅 수행.

   2. **Management Cluster 내 SR 배포**
      * SR은 **Edge Transport Node**에서만 실행됨.
      * BGP, OSPF, NAT, VPN과 같은 **중앙 집중식 네트워크 서비스**를 제공.

   3. **DR ↔ SR 연결**
      * **TransitLink를 통해 자동으로 연결됨**.
      * 내부 DR이 처리할 수 없는 외부 트래픽은 SR을 통해 라우팅됨.



------



**5. 결론**

​	•	**DR(Distributed Router)**: 모든 호스트에서 실행되며 **East-West 트래픽을 로컬에서 처리**.

​	•	**SR(Service Router)**: Edge Transport Node에서 실행되며 **North-South 트래픽 및 중앙 집중식 네트워크 서비스를 제공**.

​	•	**TransitLink를 통한 자동 연결**을 통해 DR과 SR 간 원활한 통신이 가능.



이러한 아키텍처를 통해 **NSX 환경에서 고성능 분산 라우팅과 중앙 집중식 네트워크 서비스를 동시에 제공할 수 있음.**

------

------

## **NSX Gateway 인터페이스 개요 [중요]**



![image-20250319163519878](/assets/cisco_post_img/2025-03-19-VMware NSX Module 5 NSX Logical Routing//image-20250319163519878.png)



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
