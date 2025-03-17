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
     * **NSX Edge 노드 및 베어메탈 워크로드에서 지원**
     * NSX가 관리하는 가상 스위치
     * **NSX 4.0.0.1부터 ESXi 호스트에서는 지원되지 않음**
     * 기존 환경(브라운필드)에서는 **업그레이드 전 ESXi를 VDS로 마이그레이션 필요**



2. **NSX-Proxy**
   * **모든 트랜스포트 노드에서 실행되는 에이전트**
   * 중앙 제어 플레인(CCP)으로부터 **네트워크 및 보안 정책을 수신 및 적용**
   * 트랜스포트 노드와 NSX 관리 노드 간의 **구성 및 제어 메시지를 전달**



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