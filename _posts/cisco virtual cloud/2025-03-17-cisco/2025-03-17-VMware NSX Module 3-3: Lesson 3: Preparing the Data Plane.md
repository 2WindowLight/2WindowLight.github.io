---
title: <h0>VMware NSX Module 3-3 Lesson 3 Preparing the Data Plane</h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-17-cisco]
tags: [Network, Cloud, NSX]







---



## **NSX 네트워크 인프라 개요**



NSX 네트워크는 **트랜스포트 존(Transport Zone), 트랜스포트 노드(Transport Node), N-VDS(Virtual Distributed Switch), VDS(vSphere Distributed Switch)** 등의 구성 요소로 이루어진다.

​	•	**트랜스포트 존**: NSX 논리 네트워크의 범위를 정의하며, 오버레이(Overlay)와 VLAN 트랜스포트 존으로 구분된다.

​	•	**트랜스포트 노드**: 데이터 플레인 역할을 하며, ESXi 호스트, 베어메탈 서버, NSX Edge가 포함된다.

​	•	**N-VDS vs. VDS**: N-VDS는 NSX에서 관리하는 가상 스위치이며, ESXi에서는 VDS(vSphere Distributed Switch) 사용이 권장된다.

​	•	**IP 풀(IP Pools)**: 트랜스포트 노드 간 GENEVE 터널 통신을 위한 TEP IP 주소를 할당한다.

​	•	**업링크 프로파일(Uplink Profile)**: MTU, VLAN, 로드 밸런싱 정책을 설정하여 물리적 네트워크와 NSX 간 연결을 최적화한다.



NSX 환경에서 ESXi 호스트를 트랜스포트 노드로 구성하여, 가상화된 네트워크를 운영할 수 있다.

------

------

## NSX 데이터 플레인 구성 요소 및 기능

![image-20250317144610223](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250317144610223.png)

**NSX 데이터 플레인(Data Plane)은 다양한 엔드포인트(Endpoints)로 구성되며, NSX의 핵심적인 네트워크 트래픽 전달 역할을 수행**한다.

**주 목적은 네트워크와 보안을 가상화**



------



1. **NSX 데이터 플레인의 주요 기능**
   * **패킷을 상태(State) 없이 전달하며, 캡슐화(encapsulation) 및 디캡슐화(decapsulation) 수행**.
   * **컨트롤 플레인(Control Plane)에서 받은 테이블 정보를 기반으로 패킷을 포워딩**.
   * **확장 가능한 분산 포워딩(Scale-out Distributed Forwarding) 모델을 사용하여 트래픽을 전달**.
   * **논리적 스위칭(Logical Switching), 분산 및 중앙 집중식 라우팅(Distributed & Centralized Routing), 방화벽 필터링(Firewall Filtering) 수행**.



------



2. **NSX 데이터 플레인 구성 요소**

   1. **ESXi 호스트(ESXi Host) + VDS(Virtual Distributed Switch)**
      * **VM(가상 머신) 및 컨테이너(Container)의 네트워크 트래픽을 처리**.
      * **vSphere 환경에서 NSX와 통합된 분산 스위치(VDS)를 활용**하여 패킷을 전달.

   2. **베어메탈 서버(Bare-Metal Server) + NSX**
      * **하이퍼바이저 없이 직접 실행되는 물리적 서버의 네트워크 트래픽을 처리**.
      * **Linux 및 Windows 환경에서 실행되며, 컨테이너 기반 워크로드도 지원**.

   3. **NSX Edge**
      * **게이트웨이 및 네트워크 서비스 제공**(예: NAT, 방화벽, VPN, 로드 밸런싱).
      * **데이터 센터 내부 또는 클라우드 간의 트래픽을 연결**하는 역할 수행.
      * **VM 또는 베어메탈 서버 형태로 배포 가능**.



------



3. **데이터 플레인의 작동 방식**
   * **ESXi 호스트와 베어메탈 서버는 NSX 데이터 플레인의 일부로서 트래픽을 포워딩**.
   * **NSX Edge는 물리적 네트워크와 NSX 논리 네트워크를 연결하는 역할을 수행**.
   * **컨트롤 플레인에서 설정한 정책을 기반으로 스위칭, 라우팅, 방화벽 규칙을 적용**.

------

**NSX 데이터 플레인의 주요 기능**

* **패킷 포워딩 및 캡슐화**
  * VDS 또는 NSX 스위치(N-VDS)가 패킷을 전송 및 수신하며, 오버레이 네트워크에서는 Geneve 캡슐화를 수행한다.

* **라우팅 및 NAT**
  * NSX Edge는 가상 환경과 외부 네트워크 간의 트래픽을 라우팅하고, NAT(Network Address Translation) 기능을 수행할 수 있다.

* **방화벽 및 보안 정책**
  * NSX는 분산 방화벽(DFW)을 사용하여 워크로드 간의 트래픽을 보호하고, 보안 정책을 적용할 수 있다.

* **로드 밸런싱**
  * NSX Edge는 애플리케이션 트래픽을 여러 서버에 분산하는 L4/L7 로드 밸런서를 제공한다.

------



4. **결론**
   * **NSX 데이터 플레인은 다양한 엔드포인트를 지원하며, 분산 방식의 고성능 네트워크 처리를 수행**.
   * **논리적 네트워크와 물리적 네트워크를 연결하는 핵심 요소로 NSX Edge를 활용**.
   * **NSX의 컨트롤 플레인과 협력하여 트래픽을 효과적으로 전달하고, 보안 정책을 적용할 수 있음**.
   * 이들(VDS, NSX, NSX Edge)을 통해 NSX는 **오버레이 네트워크를 형성하고, 네트워크 가상화, 보안, 트래픽 최적화 기능을 수행**할 수 있다.

------

------

## **트랜스포트 노드 개요**



트랜스포트 노드는 **NSX 네트워크 및 보안 기능을 수행하는 핵심 요소**로, 데이터 플레인 트래픽을 포워딩하는 역할을 한다.



**트랜스포트 노드의 역할**

​	•	오버레이(Overlay) 또는 VLAN 기반 네트워크 트래픽을 처리

​	•	가상 머신(VM), 컨테이너, 베어메탈 서버에서 발생하는 데이터 플레인 트래픽을 전달

------

**비교 정리**

| **구분**               | **VLAN (전통적인 L2 방식)**             | **Overlay (L2 over L3) 방식**              |
| ---------------------- | --------------------------------------- | ------------------------------------------ |
| **통신 범위**          | 동일한 L2 도메인(스위치 내)에서만 가능  | L3 네트워크 기반으로 L2 네트워크 확장 가능 |
| **외부 네트워크 통신** | L3 라우터가 필요하며 네트워크 구성 복잡 | Overlay 터널을 통해 L2 네트워크처럼 동작   |
| **확장성**             | VLAN ID 4096개 제한                     | 논리적으로 무제한 네트워크 생성 가능       |
| **멀티 데이터센터**    | VLAN 확장이 어려움                      | Overlay로 데이터센터 간 네트워크 연결 가능 |

즉, **VLAN은 물리적 네트워크(L2)에 종속적이라 동일 네트워크 내에서만 통신 가능하지만, Overlay는 L3 네트워크를 기반으로 하기 때문에 데이터센터 간, 외부 네트워크까지 확장이 가능**합니다.



✔ **Overlay를 사용하면 L3 네트워크를 활용해 L2 네트워크를 자유롭게 확장할 수 있기 때문에 외부 통신이 비교적 자유롭다.**

✔ **멀티 데이터센터 환경에서도 VLAN처럼 네트워크를 나누면서도 통신이 가능하다.**

✔ **Overlay 방식은 확장성과 유연성이 뛰어나기 때문에 대규모 네트워크에 적합하다.**

------

**지원되는 트랜스포트 노드 유형**

1. **ESXi 하이퍼바이저**
   * NSX와 통합되어 가상 네트워크 기능 수행

2. **베어메탈 서버**
   * 지원되는 운영체제:
   * **Linux 계열**: RHEL, CentOS, Ubuntu, SLES, Oracle Linux
   * **Windows**

3. **NSX Edge**
   * NSX의 게이트웨이 및 서비스 기능 수행 (NAT, 방화벽, VPN, 로드 밸런싱 등)



트랜스포트 노드는 NSX 환경에서 데이터 플레인의 핵심 역할을 담당하며, 가상 및 물리 네트워크 트래픽을 효율적으로 처리할 수 있도록 구성된다.

------

------

## **트랜스포트 노드 구성 요소 및 아키텍처**

![image-20250317145627569](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250317145627569.png)

트랜스포트 노드는 **NSX 네트워크를 구성하는 핵심 요소**이며, **데이터 플레인 트래픽을 처리하는 역할**을 한다.



1. **가상 분산 스위치 (Virtual Distributed Switch, VDS) 또는 N-VDS**

   * **VDS (vSphere Distributed Switch)**
     * ESXi 호스트에서 사용되는 **유일한 가상 스위치**
     * vSphere 환경에서 네트워크 트래픽을 관리하는 핵심 요소

   * **N-VDS (NSX Virtual Distributed Switch)**
     * **NSX Edge 노드 및 베어메탈 워크로드에서 지원 - 하이퍼 바이저가 없기때문에 N-VDS 를 사용**
     * NSX가 관리하는 가상 스위치
     * **NSX 4.0.0.1부터 ESXi 호스트에서는 지원되지 않음**
     * 기존 환경(브라운필드)에서는 **업그레이드 전 ESXi를 VDS로 마이그레이션 필요**



2. **NSX-Proxy**
   * **모든 트랜스포트 노드에서 실행되는 에이전트**
   * 중앙 제어 플레인(CCP)으로부터 **네트워크 및 보안 정책을 수신 및 적용**
   * 트랜스포트 노드와 NSX 관리 노드 간의 **구성 및 제어 메시지를 전달**
   * LCP 기반



**트랜스포트 노드의 역할**

* 가상 및 물리 네트워크 트래픽을 관리
* NSX 네트워크의 데이터 플레인 트래픽을 최적화하여 처리
* VDS 또는 N-VDS를 사용하여 가상 네트워크 연결 제공



최신 버전의 NSX에서는 **ESXi 호스트는 VDS만 지원**하며, **N-VDS는 Edge 노드 및 베어메탈에서만 사용 가능**하므로 업그레이드 시 주의해야 한다.

------

------

## **트랜스포트 노드의 물리적 연결 방식**

![image-20250317145811740](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250317145811740.png)

트랜스포트 노드(Transport Node)의 물리적 연결 방식에는 **전용 물리 NIC 사용**과 **공유 물리 NIC 사용** 두 가지 옵션이 있다.



1. **전용 물리 NIC 사용 (Dedicated Physical NICs)**
   * **관리(MGT) 트래픽과 전송(Transport) 트래픽을 분리**하여 각각 전용 NIC를 할당
   * **NSX 관리 트래픽**과 **NSX 오버레이/VLAN 트래픽** 간의 간섭을 최소화
   * **대규모 환경에서 성능과 안정성을 극대화**하는 방식



2. **공유 물리 NIC 사용 (Shared Physical NICs)**
   * **하나의 물리 NIC에서 관리 및 전송 트래픽을 처리**
   * **NIC 수가 제한된 환경**에서 유용
   * 트래픽 충돌이 발생할 수 있으며, **QoS(품질 보장) 설정이 필요**할 수 있음



**트랜스포트 노드의 네트워크 연결 구성**

* **NSX 관리 클러스터**는 관리 네트워크를 통해 트랜스포트 노드와 연결됨
* **전송 네트워크(Transport Network)**는 트랜스포트 노드 간 오버레이 또는 VLAN 트래픽을 처리
* **스위치(Leaf/Spine)와 연결**하여 트래픽을 효율적으로 라우팅



대규모 데이터센터나 멀티테넌트 환경에서는 **전용 NIC 사용**이 권장되며, **리소스가 제한된 환경에서는 공유 NIC 사용**이 고려될 수 있다.

------

------

## **IP 주소 풀(IP Address Pools) 개요**

![image-20250317152419679](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250317152419679.png)

NSX에서 **각 트랜스포트 노드(Transport Node)는 TEP(Tunnel Endpoint)를 포함**하며, 이는 **오버레이 네트워크 통신을 위한 터널링 기능**을 제공한다. TEP는 **IP 주소를 필요로 하며, 해당 주소를 할당하기 위해 IP 주소 풀(IP Address Pool)을 생성**할 수 있다.

TEP는 오버레이 네트워크에 포함시키는 인터페이스

**IP 주소 풀의 역할**

* **TEP에 IP 주소를 동적으로 할당**하여 오버레이 네트워크를 구성
* **여러 개의 IP 풀을 생성 가능**하며, 특정 트랜스포트 노드에 서로 다른 풀을 적용 가능
* **각 트랜스포트 노드의 TEP는 하나 이상의 IP를 가질 수 있음**
* **IP 풀은 수동으로 설정하거나 DHCP를 통해 동적으로 할당 가능**



**IP 주소 풀 생성 및 설정**

1. **NSX Manager UI에서 “IP Address Pools” 메뉴로 이동**

2.	**“ADD IP ADDRESS POOL” 버튼을 클릭하여 새 IP 풀 생성**

3. **풀 이름 및 설명 입력 후 “Set”을 클릭하여 서브넷 추가**

4. **“ADD SUBNET” 버튼을 눌러 IP 블록 또는 IP 범위 설정**

5. **CIDR, 게이트웨이 IP 및 DNS 정보를 입력하고 저장**



**IP 주소 풀 구성 옵션**

​	•	**IP Block**: CIDR 형식으로 IP 블록을 설정하여 TEP에 자동으로 할당

​	•	**IP Ranges**: 특정 IP 범위를 정의하여 수동으로 할당 가능



**CIDR: classless inter domain routing - 서브넷 마스크를 명시하는 것**

각 TEP의 IP 주소는 동일한 서브넷에 존재할 수도 있고, 서로 다른 서브넷에 존재할 수도 있다. 이는 **IP 풀 및 DHCP 설정에 따라 달라진다**.



------

------



## **Transport Zone 개요 및 역할**

![image-20250317153428349](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250317153428349.png)

**Transport Zone(트랜스포트 존)**은 NSX 환경에서 논리적 네트워크의 경계를 정의하는 영역이다. 트랜스포트 존은 트랜스포트 노드들이 서로 통신할 수 있도록 범위를 결정하며, 네트워크 유형에 따라 Overlay와 VLAN으로 나뉜다.



------



**1. Transport Zone의 개념**

​	•	트랜스포트 존은 논리적 네트워크의 범위를 정의하며, 특정 네트워크에서 통신할 수 있는 트랜스포트 노드들의 그룹을 설정한다.

​	•	트랜스포트 노드(ESXi 호스트, NSX Edge, 베어메탈 서버 등)는 하나 이상의 트랜스포트 존에 속할 수 있다.

​	•	트랜스포트 존은 Overlay 트래픽과 VLAN 트래픽을 각각 관리할 수 있도록 두 가지 유형으로 구분된다.



------



**2. Transport Zone 유형**



NSX에서 트랜스포트 존은 Overlay Transport Zone과 VLAN Transport Zone으로 나뉜다.



**Overlay Transport Zone**

​	•	NSX 오버레이 네트워크에서 사용되며 Geneve 캡슐화를 통한 트래픽 전송을 지원한다.

​	•	NSX 호스트와 NSX Edge 트랜스포트 노드 간 내부 터널(TEP)을 형성한다.

​	•	논리적 L2 네트워크를 확장하며, 트랜스포트 노드가 서로 다른 물리적 위치에 있어도 동일한 L2 네트워크를 사용할 수 있도록 한다.



**VLAN Transport Zone**

​	•	물리적 네트워크와 NSX 환경을 연결하는 역할을 하며, 일반적으로 NSX Edge Uplink에 사용된다.

​	•	802.1Q VLAN 태깅을 지원하여 물리적 네트워크 장비와의 연동이 가능하다.

​	•	Edge 노드의 업링크 인터페이스와 물리적 라우터 간의 **North-South 트래픽**을 처리하는 데 사용된다.



------



**3. Transport Zone의 역할**

​	•	NSX 환경에서 **논리적 네트워크의 경계를 설정**하여, 특정 트랜스포트 존에 속한 노드들만 상호 통신할 수 있도록 한다.

​	•	Overlay 트래픽과 VLAN 트래픽을 명확히 구분하여 **효율적인 네트워크 운영**을 지원한다.

​	•	트랜스포트 존을 활용하여 **네트워크 격리 및 멀티 테넌시 환경**을 구성할 수 있다.



------



------

## **Transport Zone(트랜스포트 존) 개요 (2)**



트랜스포트 존은 특정 네트워크에 어떤 호스트들이 참여할 수 있는지를 결정하며, 다음과 같은 특징을 가진다.

​	•	하나의 트랜스포트 존에는 ESXi, 베어메탈 서버, NSX Edge 등 모든 유형의 트랜스포트 노드를 포함할 수 있다.

​	•	트랜스포트 존은 **보안 경계를 의미하지 않는다**.

​	•	하나의 트랜스포트 노드는 여러 개의 트랜스포트 존에 속할 수 있으며, **하나의 오버레이 트랜스포트 존과 여러 개의 VLAN 트랜스포트 존을 가질 수 있다**.

​	•	하나의 세그먼트(Segment)는 **오직 하나의 트랜스포트 존에만 속할 수 있다**.

------

------

## **Transport Node Switch Configuration (트랜스포트 노드 스위치 구성)**

![image-20250317154417087](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250317154417087.png)





**트랜스포트 노드는 N-VDS 또는 VDS를 사용하여 트랜스포트 존(Transport Zone)에 연결된다.**

트랜스포트 노드의 스위치는 다양한 방식으로 구성될 수 있으며, ESXi, NSX Edge, 베어메탈 환경에서 각각 다르게 적용된다.



------



1. **트랜스포트 노드의 스위치 유형**

   * **ESXi 트랜스포트 노드**
     * **VDS(Virtual Distributed Switch)만 사용 가능**.
     * NSX 4.0.0.1부터 **N-VDS 지원이 중단됨** → 기존 환경에서는 **VDS로 마이그레이션 필요**.

   * **NSX Edge 및 베어메탈 노드**
     * **N-VDS만 사용 가능**.
     * NSX Edge에서는 **오버레이 트랜스포트 존(Overlay Transport Zone)** 에 **단일 N-VDS만 연결 가능**.

   * **NSX 4.0.0.1 이후 주의사항**
     * ESXi에서 N-VDS 사용 불가 → **VDS로 이전 필수**.
     * 기존 NSX Edge, 퍼블릭 클라우드 NSX 에이전트, 베어메탈 노드는 여전히 **N-VDS 유지** 가능.



------



**2. 트랜스포트 존과 스위치 연결 방식**

​	•	**각 N-VDS 또는 VDS 스위치는 여러 트랜스포트 존(Transport Zones)에 연결될 수 있음**.

​	•	**각 스위치는 전용 물리 NIC가 필요**하며, 다른 트래픽과 NIC를 공유하지 않음.

​	•	**Edge 트랜스포트 노드**의 경우, **오버레이 트랜스포트 존에 하나의 N-VDS만 연결 가능**.



------



**3. VLAN 세그먼트 고려사항**

​	•	여러 VLAN 세그먼트가 동일한 VLAN ID를 사용할 경우, **하나의 세그먼트만 적용됨**.

​	•	NSX Edge 및 ESXi에서 **각 VLAN 트랜스포트 존을 분리하여 관리 가능**.





------



**그림 설명**



**트랜스포트 노드 스위치 구성 다이어그램**

**그림 설명:**

​	•	**VLAN 및 Overlay Transport Zones 간의 연결 관계**를 나타냄.

​	•	**NSX Edge, ESXi, 베어메탈 환경에서의 스위치(N-VDS 및 VDS) 구성 방식을 표현**.

​	•	**각 트랜스포트 존(VLAN 1-TZ, VLAN 2-TZ, Overlay 1-TZ, Overlay 2-TZ)의 포함 범위를 색상으로 구분**.

​	•	**VLAN 및 Overlay 세그먼트가 트랜스포트 노드(ESXi, NSX Edge, Bare-Metal)에 어떻게 적용되는지 시각적으로 표현**.



------

------

## **VDS(가상 분산 스위치) 개요 및 역할**

![image-20250317154739569](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250317154739569.png)

**VDS(Virtual Distributed Switch)**는 vCenter Server에서 관리되는 ESXi 호스트에 적용되는 가상 네트워크 스위치로, NSX에서 트랜스포트 노드 준비 시 필수적으로 사용된다. NSX 4.0.0.1부터는 ESXi 호스트에서 **N-VDS 호스트 스위치를 지원하지 않으며, 오직 VDS만 사용 가능**하다.



------



1. **VDS의 개념 및 특징**
   * **NSX Manager에서 생성한 세그먼트**는 **vCenter Server에서 분산 포트 그룹(Distributed Port Groups)으로 실현(Realized)됨**.
   * **독립 실행형(Standalone) ESXi 호스트에서 NSX 구성이 더 이상 지원되지 않음**.
   * VDS를 NSX에서 활용하려면 **MTU 크기를 최소 1,600바이트 이상으로 설정해야 함**.
   * VDS는 **단일 Overlay Transport Zone과 여러 VLAN Transport Zone에 연결 가능**.



------



2. **NSX에서 VDS 설정 방식**
   * **vCenter Server에서 관리되는 ESXi 호스트**는 **VDS 기반으로 트랜스포트 노드 준비**됨.
   * **독립 실행형 ESXi 호스트**에서는 NSX 사용 불가.
   * **vSphere Client를 통해 VDS MTU 설정을 1,600바이트 이상으로 조정**해야 NSX에서 정상적으로 동작.



------



3. **VDS와 NSX 연동**
   * **NSX Manager에서 세그먼트(Segments) 생성 → vCenter Server에서 분산 포트 그룹(Distributed Port Groups)으로 변환됨**.
   * **VDS는 Overlay Transport Zone 1개와 VLAN Transport Zone 여러 개를 연결 가능**.



------



4. **그림 설명**



위 그림은 **NSX Manager에서 생성한 App-Segment, DB-Segment, Web-Segment가 vSphere Client의 분산 포트 그룹(Distributed Port Groups)으로 매핑되는 과정**을 보여준다.

* **NSX Manager의 Segments 탭에서 App-Segment, DB-Segment, Web-Segment가 생성됨**.
* **vSphere Client에서 동일한 세그먼트가 App-Segment, DB-Segment 등의 포트 그룹으로 표시됨**.
* **App-Segment는 Prod-Overlay-TZ(Overlay Transport Zone)에 속함**.



------

**결론**

NSX 환경에서 ESXi 호스트는 **VDS 기반으로만 트랜스포트 노드가 준비되며**, 세그먼트는 vCenter Server에서 **분산 포트 그룹(Distributed Port Groups)으로 변환되어 사용된다**.

------

------

## **N-VDS(논리적 스위치) 개요 및 역할**

![image-20250317154957214](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250317154957214.png)

**N-VDS(Namespace Virtual Distributed Switch)**는 **NSX 환경에서 트랜스포트 노드에서 패킷을 전달하는 논리적 스위치**로, NSX Manager에 의해 생성 및 중앙 관리된다.

이는 **NSX Edge 및 베어메탈 트랜스포트 노드에 배포되며, 일관된 네트워크 구성을 제공**한다.



------



1. **N-VDS의 개념 및 특징**
   * **NSX Manager가 N-VDS 인스턴스를 생성하고 중앙에서 관리**.
   * **NSX Edge 및 베어메탈 트랜스포트 노드에 배포되어 스위칭 기능 수행**.
   * **각 N-VDS는 개별적으로 생성되며, 서로 독립적으로 동작**.
   * **하나의 트랜스포트 노드에서 여러 개의 NIC를 사용할 수 있음**.
   * **N-VDS는 이름을 할당하여 그룹화 및 관리가 가능**.



------



2. **N-VDS의 주요 기능**
   * **트랜스포트 노드에서 패킷 전달 역할** 수행.
   * **각 N-VDS는 독립적으로 동작**하며, 다른 N-VDS와 간섭하지 않음.
   * **NSX Edge 또는 베어메탈 트랜스포트 노드에서 생성 가능**.
   * **여러 개의 N-VDS를 서로 다른 네트워크 목적에 맞게 구성 가능**.



------



3. **N-VDS의 사용 사례**
   * **NSX Edge 노드**: 게이트웨이 및 방화벽 역할을 수행하는 NSX Edge에서 N-VDS 인스턴스를 생성하여 스위칭 기능을 제공.
   * **베어메탈 트랜스포트 노드**: 물리 서버 환경에서도 N-VDS를 사용하여 NSX 네트워크에 연결 가능.



------



4. **그림 설명**

위 그림은 **NSX 관리 클러스터가 두 개의 랙(Rack A, Rack B)에 N-VDS를 배포하는 구조를 보여준다**.

* **Rack A, Rack B에 각각 두 개의 N-VDS 인스턴스(Lab, Prod)가 구성**됨.
* **Lab N-VDS와 Prod N-VDS는 독립적으로 동작**하며, 각각 개별 네트워크 목적에 따라 활용됨.



------



**결론**



N-VDS는 **NSX 환경에서 트랜스포트 노드의 패킷 전달을 담당하는 논리적 스위치**로, **NSX Manager가 중앙에서 관리하며, NSX Edge 및 베어메탈 트랜스포트 노드에서 사용**된다.

이는 **각각 독립적으로 동작하며, 특정 네트워크 목적에 맞춰 여러 개의 N-VDS를 배포할 수 있는 유연성을 제공**한다.



------

------

### 기타

위 과정을 하는 이유

**NSX에서 트랜스포트 존(Transport Zone)이 필요한 이유**

1. 기존 네트워크 환경의 한계
   * 기존 네트워크에서는 **라우터와 ESXi 하이퍼바이저 간의 통신이 비효율적**이며, 특히 **호스트 간(VM-to-VM) 통신을 위해 라우터를 거쳐야 하는 문제가 발생**함.
   * 물리적인 **스위치 기반 네트워크는 오버레이 네트워크(Overlay Network)를 지원하지 않으며**, VLAN을 활용하여 네트워크를 분할해야 하지만, 이는 VLAN ID 제한(4096개 VLAN)과 같은 **확장성 문제**를 초래함.
   * 기존 물리 네트워크에서는 **L2 네트워크의 확장이 제한**되며, 데이터센터 간 **멀티테넌트 네트워크를 구축하는 것이 어렵다**.

------

2. **NSX에서의 해결 방법: 트랜스포트 존**
   * **트랜스포트 존(Transport Zone)**은 **NSX 환경에서 네트워크의 범위를 정의하는 핵심 개념**으로, 특정 네트워크에서 통신할 수 있는 **트랜스포트 노드(ESXi 호스트, NSX Edge, 베어메탈 서버 등)의 그룹을 설정**함.
   * **트랜스포트 존을 활용하면, 물리 네트워크와 관계없이 NSX 오버레이 네트워크를 구성**할 수 있으며, 이는 VM 간의 **직접적인 터널링을 통해 라우터를 거치지 않고 효율적으로 통신할 수 있도록 지원**함.
   * NSX는 **Geneve 캡슐화를 사용하여 오버레이 네트워크를 구축**하며, 이를 통해 **L2 네트워크를 데이터센터 전체에 걸쳐 확장 가능**함.

------

3. **트랜스포트 존의 역할**
   * **트랜스포트 존을 생성하면, 특정 호스트 그룹(트랜스포트 노드)이 동일한 네트워크 범위 내에서 통신 가능**.
   * **Overlay Transport Zone**과 **VLAN Transport Zone**으로 구분되며, **Overlay Transport Zone은 NSX 오버레이 네트워크를 구축하여 L2 확장이 가능**.
   * 트랜스포트 존을 **호스트에 적용함으로써, 기존 물리 네트워크와 무관하게 NSX 네트워크를 활용할 수 있음**.

------

**결론**

따라서 **트랜스포트 존을 사용하면 기존 스위치 기반의 VLAN 네트워크의 한계를 극복하고, VM-to-VM 간 통신을 보다 효율적으로 수행**할 수 있음.

기존 네트워크에서는 **라우터를 통해야 하는 비효율적인 구조**였지만, NSX의 트랜스포트 존을 활용하면 **ESXi 호스트 간 직접적인 통신이 가능하며, 오버레이 네트워크를 통해 확장성 및 유연성이 증가**함.

------

------

NSX 데이터 플레인의 **ESXi 호스트 (VDS), NSX Edge 노드, 트랜스포트 노드**는 각각 독립적인 역할을 수행하지만, **전체 네트워크 통신 과정에서는 유기적으로 연결**되어 함께 동작합니다. 이를 쉽게 이해할 수 있도록 **3가지 요소가 협력하는 과정**을 정리해보겠습니다.



------



**NSX 데이터 플레인의 통신 과정**



**목표:** 가상 머신(VM)이 인터넷(외부)과 통신하는 과정

1. **가상 머신(VM) → VDS (ESXi 호스트)**

​	•	VM이 인터넷 또는 다른 VM과 통신하려고 함.

​	•	**ESXi 호스트의 VDS(가상 분산 스위치)**를 통해 네트워크 트래픽이 전달됨.

​	•	**VDS는 NSX의 논리적 스위치 역할을 하며, Overlay 또는 VLAN 방식으로 데이터를 전달.**



2. **트래픽 전송 → 트랜스포트 노드**

​	•	VDS가 받은 트래픽을 **트랜스포트 노드를 통해 NSX 네트워크로 전달**.

​	•	트랜스포트 노드는 Overlay(Tunnel Endpoint, TEP)를 활용해 데이터를 캡슐화하여 전달함 (L2 over L3).

​	•	즉, **ESXi 호스트에서 다른 호스트(또는 NSX Edge)로 데이터가 이동**할 수 있도록 하는 핵심 역할 수행.



3. **NSX Edge 노드 → 외부 네트워크 연결**

​	•	VM이 인터넷이나 외부 네트워크와 통신하려면 **NSX Edge 노드를 거쳐야 함**.

​	•	Edge 노드는 **NAT(네트워크 주소 변환), VPN, 방화벽 등의 기능**을 제공.

​	•	트래픽을 물리적인 라우터 또는 데이터 센터 외부의 네트워크로 라우팅.



4. **외부 네트워크 → 다시 NSX 내부로 반환**

​	•	외부에서 들어오는 데이터가 Edge 노드에서 수신됨.

​	•	다시 트랜스포트 노드를 통해 NSX 네트워크 내부로 전달.

​	•	최종적으로 VDS를 통해 목적지 VM에 도착.



------



**요약 (전체 흐름)**

​	1.	**VM → VDS** (가상 네트워크 스위치 역할)

​	2.	**VDS → 트랜스포트 노드** (데이터 캡슐화 및 전달)

​	3.	**트랜스포트 노드 → NSX Edge 노드** (외부 네트워크와 연결)

​	4.	**Edge 노드 → 인터넷 또는 외부 네트워크**

​	5.	**반대로 외부에서 들어오는 데이터도 동일한 경로를 거쳐 내부 VM으로 전달.**



------



**결론: 3가지 요소는 함께 동작함**



✔ **각 요소는 독립적인 역할을 수행하지만, 전체 네트워크에서 반드시 서로 협력해야 함.**

✔ **ESXi의 VDS는 NSX 네트워크의 기본 가상 스위치 역할, 트랜스포트 노드는 트래픽 전달, Edge 노드는 외부 연결 담당.**

✔ **즉, NSX 네트워크가 정상적으로 작동하려면 3가지 요소가 서로 연결되어야 함.**

------

------

## Creating Transport Zones

![image-20250318101526804](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318101526804.png)

------

------

**물리 NIC, LAG 및 업링크(Physical NICs, LAGs, and Uplinks)**

![image-20250318101759908](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318101759908.png)

요청한 내용을 자세히 요약하여 제공하겠습니다.



ESXi 호스트는 여러 개의 물리적 네트워크 인터페이스 카드(NIC)를 가질 수 있으며, 이러한 NIC들은 네트워크 트래픽을 처리하는 중요한 요소이다. 이를 논리적으로 그룹화하여 트래픽을 보다 효과적으로 분배하는 방식이 존재하며, 이는 **업링크(Uplink)** 및 **링크 집계 그룹(LAG, Link Aggregation Group)** 을 통해 이루어진다.



------



**1. 주요 개념**



**① 물리적 NIC(Physical NICs)**

​	•	ESXi 호스트에는 여러 개의 물리적 네트워크 인터페이스 카드(NIC)가 장착될 수 있다.

​	•	각각의 물리적 NIC는 트래픽을 물리적 네트워크와 주고받는 역할을 한다.

​	•	예제에서는 p1, p2, p3의 세 개의 NIC가 존재한다.



**② 업링크(Uplinks)**

​	•	**업링크(Uplink)** 는 **VDS(VSphere Distributed Switch)** 상에서 논리적 인터페이스 역할을 한다.

​	•	업링크를 통해 물리적 네트워크와 가상 네트워크 간의 연결이 이루어진다.

​	•	업링크는 물리적 NIC와 직접 연결되지 않고, 논리적인 방식으로 구성된다.

​	•	예제에서는 u1, u2 두 개의 업링크가 사용되었다.



**③ 링크 집계 그룹(LAG, Link Aggregation Group)**

​	•	여러 개의 물리적 NIC를 하나의 논리적 링크로 묶어 **대역폭을 증가** 시키고 **가용성을 향상** 시키는 방식이다.

​	•	**LAG는 LACP(Link Aggregation Control Protocol)를 이용** 하여 트래픽을 여러 링크로 분산시킬 수 있다.

​	•	예제에서는 u1 업링크가 p1, p2를 포함하는 LAG로 구성되어 있다.



------



**2. VDS(가상 분산 스위치)에서의 업링크 구성**

​	•	**NSX에서 업링크는 VDS의 업링크와 직접 연결해야 하며, 개별 NIC와 직접 연결하면 안 된다.**

​	•	VDS 업링크는 오버레이(Overlay) 및 VLAN 트래픽을 처리할 수 있도록 구성해야 한다.

​	•	물리적인 NIC를 업링크와 올바르게 매핑해야 트래픽이 원활하게 흐를 수 있다.



------



**3. 예제에서의 매핑 구조**

​	•	**물리적 NIC(p1, p2, p3)**

​	•	p1, p2: LAG 그룹으로 묶여 u1 업링크에 매핑됨.

​	•	p3: u2 업링크에 단독 매핑됨.

​	•	**논리적 업링크(u1, u2)**

​	•	u1 → LAG(p1, p2)에 매핑됨.

​	•	u2 → p3에 직접 매핑됨.



이러한 구성은 **네트워크 대역폭을 효과적으로 활용** 하고, **다중 경로를 통해 가용성을 확보** 할 수 있도록 한다.



------



**4. 요약**

​	1.	ESXi 호스트는 여러 개의 물리적 NIC(p1, p2, p3)를 가질 수 있다.

​	2.	**업링크(Uplink)** 는 **VDS 상의 논리적 인터페이스** 로, 트래픽을 가상 네트워크와 물리 네트워크 간에 전달한다.

​	3.	**LAG(Link Aggregation Group)** 을 사용하면 **NIC 여러 개를 묶어** 대역폭을 확장하고, LACP를 통해 트래픽을 분산시킬 수 있다.

​	4.	**NSX에서 업링크는 VDS의 업링크와 연결해야 하며, 물리적 NIC에 직접 연결해서는 안 된다.**

​	5.	**LAG를 구성하면 가용성과 성능을 향상** 시킬 수 있다.



------



이렇게 구성된 네트워크는 **네트워크 병목 현상을 줄이고, 확장성을 향상** 시키는 데 중요한 역할을 한다.

------

------

## **업링크 프로필(Uplink Profiles) 개요 및 기능**

![image-20250318102935602](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318102935602.png)



**업링크 프로필(Uplink Profile)** 은 **VDS(Virtual Distributed Switch)** 가 물리 네트워크와 연결되는 방식을 정의하는 템플릿 역할을 한다. 업링크 프로필을 통해 여러 호스트 또는 노드에서 **일관된 네트워크 설정을 적용**할 수 있다.



------



**업링크 프로필의 주요 기능**

​	1.	**업링크의 형식 지정**

​	•	VDS에서 사용할 업링크 형식을 설정한다.

​	•	물리 NIC을 어떻게 활용할 것인지 정의할 수 있음.

​	2.	**팀 구성 정책(Teaming Policy) 정의**

​	•	업링크 간의 로드 밸런싱 및 장애 조치 정책을 설정한다.

​	•	액티브(Active) 및 대기(Standby) 업링크를 지정 가능.

​	3.	**트랜스포트 VLAN 설정**

​	•	오버레이 트래픽을 전달할 **VLAN ID**를 지정.

​	4.	**MTU(Maximum Transmission Unit) 설정**

​	•	**네트워크 패킷 크기**를 설정하여 최적의 성능을 보장.

​	•	NSX 환경에서는 **1600바이트 이상의 MTU 설정이 필수적**임.

​	5.	**일관된 네트워크 설정 적용**

​	•	업링크 프로필을 사용하면 여러 호스트 및 트랜스포트 노드에서 동일한 네트워크 구성을 유지할 수 있음.

​	•	관리자가 특정 설정을 변경하면, **해당 업링크 프로필을 따르는 모든 트랜스포트 노드에서 자동으로 업데이트됨**.



------



**업링크 프로필 적용 예시**

​	•	**ESXi Transport Node의 업링크 구성**

​	•	VDS에서 **업링크-1(Uplink-1)** 에 **LAG-1**(물리 NIC P1, P2)을 매핑 → **Active 역할** 수행

​	•	VDS에서 **업링크-2(Uplink-2)** 에 **LAG-2**(물리 NIC P3, P4)를 매핑 → **Standby 역할** 수행

​	•	기본적으로 **Active 업링크는 트래픽을 전달**하고, 장애 발생 시 **Standby 업링크가 활성화됨**

​	•	**Bare-Metal NSX Edge의 업링크 프로필**

​	•	기본 업링크 프로필은 **1개의 액티브(Active) 업링크**와 **1개의 대기(Standby) 업링크**를 요구.



------



**그림 설명**



해당 그림에서는 **ESXi 트랜스포트 노드의 업링크 구성**을 시각적으로 보여준다.

* **왼쪽 그림:**
  * P1, P2는 LAG-1을 구성하며 Uplink-1에 연결되어 **Active 상태**
  * P3, P4는 LAG-2를 구성하며 Uplink-2에 연결되어 **Standby 상태**

* **오른쪽 그림:**

  * P1, P2는 LAG-1에 연결되어 **Active 상태**

  * P3는 Uplink-2의 **Standby 상태**, P4는 **미사용(Unused) 상태**



------



**결론**



업링크 프로필을 사용하면 **VDS와 물리 네트워크 간의 연결을 표준화**하고, **일관된 네트워크 정책을 적용**할 수 있다.

​	•	**트랜스포트 노드가 동일한 네트워크 구성을 유지**하도록 관리 가능.

------

------

## **Default Uplink Profiles(기본 업링크 프로필)**

![image-20250318104225201](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318104225201.png)

업링크 프로필(Uplink Profile)은 여러 트랜스포트 노드에서 네트워크 어댑터의 설정을 일관되게 구성할 수 있도록 하는 템플릿 역할을 한다. 이를 통해 네트워크 표준화를 유지하고 관리자가 쉽게 설정을 적용할 수 있다.



**주요 기능**

​	•	**일관된 네트워크 설정**: 동일한 Uplink Profile을 여러 트랜스포트 노드에 적용하여 네트워크 구성을 통합 관리할 수 있다.

​	•	**VDS(가상 분산 스위치)와의 연계**: NSX는 VDS와 함께 Uplink Profile을 사용하여 물리적 네트워크와 연결된다.

​	•	**로드 밸런싱 및 장애 조치 정책 설정**: Uplink Profile을 통해 Active 및 Standby Uplink를 설정하여 네트워크 안정성을 향상시킨다.

​	•	**MTU 및 VLAN 설정 가능**: 트랜스포트 VLAN을 정의하고 MTU 값을 조정할 수 있다.



**기본 업링크 프로필 확인 방법**



NSX UI에서 **System > Fabric > Profiles > Uplink Profiles** 로 이동하면 기본 Uplink Profile을 확인할 수 있다.



**그림 설명**



업링크 프로필을 설정하는 UI 화면이다. **Teaming Policy(팀 구성 정책), Active Uplinks(활성 업링크), Standby Uplinks(대기 업링크), Transport VLAN, MTU 값**을 포함한 기본 업링크 프로필이 표시된다. 이를 통해 다양한 트랜스포트 노드에 표준화된 네트워크 구성을 적용할 수 있다.

------

------

## **Teaming Policies(팀 구성 정책)**

![image-20250318110046633](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318110046633.png)

**개요**

Teaming Policy(팀 구성 정책)는 Uplink Profile(업링크 프로파일) 설정의 일부로, **Uplink의 이중화 및 장애 복구 모델**을 정의한다. VDS(Virtual Distributed Switch)에서 **하나의 팀 구성 정책만 적용 가능**하며, 전체 트래픽 분산 방식에 영향을 준다.



------



**주요 기능**

1. **트래픽 분산 및 로드 밸런싱**
   * NSX 가상 스위치가 업링크를 통해 트래픽을 어떻게 균등하게 분배하는지 결정한다.
   * Uplink는 개별 pNIC(물리 네트워크 인터페이스) 또는 LAG(Link Aggregation Group)일 수 있다.

2. **LAG 기반 해시 옵션**
   * LAG(링크 집계 그룹) 업링크는 자체적인 해시 옵션을 제공한다.
   * 하지만 이 해시 옵션은 LAG에 속한 개별 물리 인터페이스 간 트래픽을 분배하는 역할만 한다.

3. **NSX 가상 스위치 업링크 분배 방식**
   * **Failover Order(장애 조치 순서)**: 기본 활성 Uplink가 장애 발생 시 Standby Uplink가 트래픽을 처리하도록 설정.
   * **Load Balance Source(소스 기반 부하 분산)**: 트래픽 소스 MAC 주소를 기반으로 업링크를 자동 선택하여 로드 밸런싱 수행.
   * **Load Balance Source MAC(소스 MAC 기반 부하 분산)**: 특정 MAC 주소별로 트래픽을 분산하여 다수의 업링크를 효과적으로 활용.



------



**설명 이미지**

* **좌측 이미지(Failover Order)**
  * 기본적으로 **Uplink-1(LAG-1)**이 Active 상태로 동작하며,
  * **Uplink-2(LAG-2)**는 Standby 상태로 설정됨.
  * Active Uplink가 장애 발생 시 Standby Uplink로 자동 전환됨.

* **우측 이미지(Load Balance Source / Load Balance Source MAC)**
  * *Uplink-1과 Uplink-2 모두 Active** 상태에서 트래픽을 처리.
  * 로드 밸런싱을 활용하여 각 VM이 다른 업링크를 통해 트래픽을 송출하도록 분산.



이러한 팀 구성 정책을 사용하여 NSX 환경에서 **트래픽 안정성 및 효율성을 최적화**할 수 있다.



VNI - 예시로 vlan 10, 20 은 다른 네트워크이지만, VNI 로 매핑을 하면 10 과 20은 같은 네트워크가 됨

------

------

## Teaming Policy Modes

## ![image-20250318111458419](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318111458419.png)

------

------

**Link Layer Discovery Protocol (LLDP)**

![image-20250318111537303](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318111537303.png)

**개요**

LLDP(Link Layer Discovery Protocol)는 네트워크 장치의 **식별 정보, 기능, 연결된 장치 정보**를 광고(Advertise)하여 **물리 네트워크 토폴로지를 정확하게 파악**할 수 있도록 도와주는 프로토콜이다.



------



**LLDP의 주요 기능 및 장점**

1. **다중 벤더 환경에서 네트워크 관리 도구 활용을 단순화**
   * LLDP는 표준 기반 프로토콜이므로, 다양한 네트워크 장치 간 상호 운용성을 제공한다.
   * 특정 제조사(Cisco, Juniper 등) 장치에 종속되지 않고, 이기종 네트워크에서도 쉽게 네트워크를 관리할 수 있다.

2. **정확한 물리 네트워크 토폴로지 자동 탐색**
   * 연결된 네트워크 장치 정보를 동적으로 학습하여 토폴로지를 자동으로 구성한다.
   * 관리자는 수동으로 장치를 매핑할 필요 없이, LLDP 데이터를 통해 네트워크 구조를 쉽게 파악할 수 있다.

3. **네트워크 문제 해결(Troubleshooting) 간소화**
   * LLDP 정보를 기반으로 장치 간 연결 상태를 쉽게 확인할 수 있어, 장애 발생 시 빠르게 원인을 분석하고 해결할 수 있다.



------



**vSphere에서 LLDP 설정 방법**

​	•	LLDP는 **vCenter Server Distributed Switch의 고급 속성(Advanced Property)** 에서 설정 가능하다.

​	•	vSphere Client를 통해 Distributed Switch(DVS) 설정을 편집할 때 **Discovery Protocol** 옵션에서 LLDP를 활성화할 수 있다.

​	•	LLDP 외에도 **Cisco Discovery Protocol (CDP)** 을 선택하여 Cisco 환경과의 네트워크 탐색 기능을 사용할 수도 있다.



------



**설명 이미지**

​	•	**Distributed Switch - Edit Settings 창에서 LLDP를 설정하는 모습**

​	•	**Discovery Protocol**: 네트워크 탐색 프로토콜 선택

* 선택 가능한 옵션:
  * **Disabled(비활성화)**
  * **Cisco Discovery Protocol (CDP)**
  * **Link Layer Discovery Protocol (LLDP)**
  * MTU(최대 전송 단위) 및 Multicast 필터링 모드도 함께 설정 가능.



LLDP를 활용하면 네트워크 운영자가 **물리 및 가상 네트워크의 연결 관계를 쉽게 관리**하고, **네트워크 트러블슈팅을 빠르게 수행할 수 있다.**

------

------

## **Transport Node Profile**

![image-20250318111844924](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318111844924.png)

**개요**

Transport Node Profile은 **트랜스포트 노드를 생성하는 데 필요한 설정을 캡처하는 프로파일**이다.

이 프로파일을 기존 **vSphere 클러스터에 적용**하면, 해당 클러스터의 모든 호스트가 자동으로 **트랜스포트 노드로 변환**된다.



------



**Transport Node Profile의 주요 기능**

1. **트랜스포트 존(Transport Zone) 지정**
   * 트랜스포트 노드가 참여할 **오버레이(Overlay) 또는 VLAN 기반 트랜스포트 존**을 정의한다.

2. **VDS(Virtual Distributed Switch) 설정**
   * 트랜스포트 노드가 연결될 **가상 분산 스위치(VDS)** 를 지정하고 설정한다.
   * vSphere 환경에서 **VDS를 통해 NSX 트래픽을 처리**할 수 있도록 구성.

3. **업링크 프로파일(Uplink Profile) 정의 및 물리 NIC 매핑**
   * 업링크 프로파일을 사용하여 **업링크 포트, 로드 밸런싱 정책, VLAN ID 설정** 등을 일관되게 적용.
   * 물리 NIC을 트랜스포트 노드의 **논리 업링크와 매핑(mapping)** 한다.

4. **IP 할당(IP Assignment)**
   * 터널 엔드포인트(TEP, Tunnel Endpoint)에 사용할 IP 주소를 자동 또는 수동으로 할당한다.
   * IP 풀을 사용하여 클러스터 내 모든 트랜스포트 노드에 TEP를 자동 할당 가능.



------



**트랜스포트 노드 프로파일 적용 과정**

1. **Transport Node Profile을 vSphere 클러스터에 적용**

2. **NSX Manager가 클러스터 내 모든 호스트를 준비(Prepare)**

3. **NSX 구성 요소가 각 호스트에 설치됨**

4. **각 호스트가 트랜스포트 노드로 변환됨**



------



**설명 이미지**

​	•	NSX에서 vSphere 클러스터를 위한 설정을 Transport Node Profile을 통해 적용한다.

​	•	Transport Node Profile은 다음과 같은 요소를 포함한다:

​	•	**IP 할당**

​	•	**업링크 프로파일 및 물리 NIC 매핑**

​	•	**트랜스포트 존 지정**

​	•	**VDS 설정**

​	•	Transport Node Profile이 적용되면 NSX Manager가 클러스터 내 모든 호스트를 준비하고, NSX 관련 설정을 자동으로 배포한다.



Transport Node Profile을 사용하면 **vSphere 클러스터 전체를 일괄적으로 트랜스포트 노드로 변환할 수 있어, 설정을 표준화하고 관리 효율성을 높일 수 있다.**





------

------

## **Transport Node Profiles(트랜스포트 노드 프로필)의 이점**

![image-20250318112506197](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318112506197.png)



트랜스포트 노드 프로필은 vSphere 클러스터를 사용하여 배포를 단순화하고 일관성을 유지하는 데 도움을 준다.



**이점**

* **배포 속도 향상(Speed deployments)**
  * vSphere 클러스터를 활용하여 NSX 인프라 구성 요소를 보다 빠르게 배포할 수 있다.

* **NSX 인프라 구성 요소의 신속한 배포(Enable the faster deployment of NSX infrastructure components)**
  * 클러스터 내 모든 호스트에 동일한 설정을 적용하여 빠른 NSX 환경 구축이 가능하다.

* **일관성 유지 및 수동 오류 방지(Drive consistency and avoid manual errors)**
  * 수동 설정 과정에서 발생할 수 있는 오류를 줄이고, 모든 트랜스포트 노드에 동일한 구성을 적용한다.

* **반복 사용 가능(Can be reused multiple times)**
  * 동일한 트랜스포트 노드 프로필을 여러 클러스터에서 재사용할 수 있어 운영 효율성이 향상된다.



------

**관련 그림 설명**

해당 그림은 트랜스포트 노드 프로필이 vSphere 클러스터에서 적용되는 구조를 보여준다.

* Compute Cluster에서는 트랜스포트 노드 프로필(TNP-1, TNP-2)이 가상 머신과 함께 적용된다.
* Management Cluster/Edge Cluster에서는 관리 및 엣지 노드들이 트랜스포트 노드 프로필을 통해 설정된다.
* 이를 통해 전체 클러스터에 걸쳐 일관된 NSX 인프라 구성을 유지할 수 있다.



------

------

## Creating a Transport Node Profile

![image-20250318113254995](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318113254995.png)

------

------

## Attaching a Transport Node Profile to the vSphere Cluster

![image-20250318113315684](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318113315684.png)

------

------

## Reviewing the ESXi Transport Node Status (1)

![image-20250318113332906](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318113332906.png)

------

------

## Reviewing the ESXi Transport Node Status (2)

![image-20250318113737486](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318113737486.png)

------

## Verifying the ESXi Transport Node by CLI

![image-20250318113825310](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-3: Lesson 3: Preparing the Data Plane//image-20250318113825310.png)

**nsx-proxy : (lcp) ccp랑 통신할 컴포넌트 존재**

NSX 커널 모듈은 VIB(ESXi 패키지) 파일에 포함되어 있으며, 호스트에 다운로드됩니다. 이 커널 모듈은 **분산 라우팅(Distributed Routing)**, **분산 방화벽(Distributed Firewall)** 등의 서비스를 제공



**VIB(VMware Installation Bundle)의 기능**

​	•	**nsx-esx-datapath**: NSX 데이터 평면의 패킷 처리 기능을 제공

​	•	**nsx-exporter**: 실행 시간(runtime) 상태를 집계 서비스에 보고하는 호스트 에이전트 제공

​	•	**nsx-host**: 호스트에 설치된 VIB 번들의 메타데이터 제공

​	•	**nsx-mpa**: NSX Manager와 하이퍼바이저 호스트 간의 통신 기능 제공

​	•	**nsx-python-protobuf**: 프로토콜 버퍼(Protocol Buffers)를 위한 Python 바인딩 제공

​	•	**nsx-sfhc**: 서비스 패브릭 호스트 컴포넌트(SFHC)로, 하이퍼바이저의 패브릭 호스트 수명 주기를 관리하는 호스트 에이전트 제공

​	•	**nsxcli**: 하이퍼바이저 호스트에서 NSX CLI를 제공