---
title: <h0>NSX Key Concepts</h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-14-cisco]
tags: [Network, Cloud]




---

#  NSX Key Concepts



**NSX 핵심 개념 정리 (Key Concepts)**

------



1. **컴퓨트 및 네트워크 관리**

   * **컴퓨트 매니저 (Compute Manager)**:
     * 호스트와 VM을 관리하는 시스템.
     * NSX는 vCenter를 컴퓨트 매니저로 사용.
     * **vCenter 서버**

   * **관리 플레인 (Management Plane)**:
     * 사용자 설정을 저장하고 관리하는 NSX의 중앙 시스템.
     * API, CLI, 웹 UI를 통해 관리 가능.
     * 사용자가 사용할 수 았는 포인트를 제공

   * **컨트롤 플레인 (Control Plane)**:
     * 네트워크 경로와 상태를 계산하고, 데이터 플레인으로 전달하는 역할.
     * NSX 컨트롤 플레인은 **중앙 컨트롤 플레인(CCP)**과 **로컬 컨트롤 플레인(LCP)**으로 구성됨.

   * **데이터 플레인 (Data Plane)**:
     * 실제 패킷 전송을 담당하는 계층.
     * NSX 데이터 플레인은 **ESXi 호스트 또는 NSX Edge 노드에서 실행됨**.

   * **NSX Edge 노드 (NSX Edge Node)**:
     * NAT, VPN, 부하 분산 등의 기능을 처리하는 전용 네트워크 장치.
     * 가상 머신(VM) 또는 베어메탈 서버에서 실행 가능.
     * 서비스 역할



------



2. **네트워크 연결 및 트래픽 흐름**

   * **외부 네트워크 (External Network)**:
     * NSX에서 관리하지 않는 VLAN 또는 물리 네트워크.
     * NSX 논리 네트워크와 Tier-0, Tier-1 게이트웨이를 통해 연결 가능.
     * 오버레이 네트워크를 외부와 연결
   * **논리적 포트 (Logical Port)**:
     * **이그레스(Egress)**: VM에서 네트워크로 나가는 트래픽.
     * **인그레스(Ingress)**: 네트워크에서 VM으로 들어오는 트래픽.
   * **게이트웨이 (Gateway)**:
     * 서로 다른 L2 네트워크를 연결하는 NSX 라우팅 장치.
     * 트랜스 포트에 만들어지는 장비
   * **Tier-0 게이트웨이**: 물리 네트워크와 연결 (BGP 지원).
   * **Tier-1 게이트웨이**: 내부 네트워크와 Tier-0 게이트웨이를 연결.
   * **터널 엔드포인트 (TEP, Tunnel Endpoint)**:
     * NSX 오버레이 네트워크에서 트래픽을 캡슐화하는 장치.
     * GENEVE 터널을 사용하여 ESXi 호스트 또는 Edge 노드 간 트래픽 전송.



------



3. **NSX 가상 네트워크 요소**

   * **세그먼트 (Segment, 기존 Logical Switch [숫자에 제한 x])**:
     * VM과 게이트웨이를 연결하는 L2 네트워크.
     * VLAN 또는 Overlay 기반으로 동작.

   * **게이트웨이 포트 (Gateway Port)**:
     * 논리적 네트워크와 물리 네트워크를 연결하는 포트.

   * **NSX Managed Virtual Distributed Switch (N-VDS)**:
     * 논리 네트워크와 물리 네트워크 간 트래픽을 전송하는 NSX 전용 가상 스위치.
     * ESXi 환경에서는 더 이상 사용되지 않고 **vSphere Distributed Switch(VDS)로 대체됨**.

   * **vSphere Distributed Switch (VDS)**:
     * NSX 3.0 이후부터 NSX가 VDS 위에서 동작 가능.
     * Overlay 및 VLAN 세그먼트 생성 가능.



------



4. **보안 및 서비스**

* **NSX 방화벽 (Distributed Firewall, Gateway Firewall)**:
  * VM 간 트래픽(East-West) 및 외부 트래픽(North-South)을 보호.

* **NAT 및 NAT64**:
  * 내부 네트워크와 외부 네트워크 간의 IP 변환 기능.

* **VPN (L2 VPN, IPSec VPN)**:
  * NSX 네트워크와 외부 네트워크 간 암호화된 연결 지원.

* **로드 밸런싱 (Load Balancing)**:
  * NSX Edge를 사용하여 부하 분산 수행.



------



5. **네트워크 인프라 관리**

   * **전송 존 (Transport Zone)**:
     * NSX 네트워크가 확장될 수 있는 범위를 정의.
     * 여러 ESXi 호스트 및 Edge 노드 간 네트워크 연결을 제공.
     * transport node의 컬렉션
   * **전송 노드 (Transport Node)**:
     * NSX 네트워크에 참여하는 ESXi 호스트, Edge 노드, KVM 호스트.
     * 분산 스위치를 만들어서 사용해야 한다.
     * transport zone에 esxi 소프트웨어를 설치하면 transport node 가 된다.
     * 결국 세그먼트를 만들 수 있는 스위치가 됨
   * **업링크 프로파일 (Uplink Profile)**:
     * NSX 네트워크와 물리 네트워크 간 연결 정책을 설정.
     * VLAN ID, MTU 크기, 트래픽 분산 정책 등을 포함.



------



6. **기타 개념**

   * **Open vSwitch (OVS)**:
     * KVM 및 베어메탈 환경에서 사용되는 오픈소스 가상 스위치.
   * **Corfu 서비스**:
     * NSX Manager 노드에서 실행되는 분산 데이터 저장소.
   * **NSX Manager 및 클러스터**:
   * **NSX Manager**: API, UI, 관리 및 제어 기능 제공.
   * **NSX Manager Cluster**: 고가용성을 위한 여러 개의 NSX Manager 인스턴스.
   * **Opaque Network**:
     * vSphere에서 보이긴 하지만, 직접 관리되지 않는 NSX 논리 네트워크.
     * VM이 연결된 NSX 네트워크를 vCenter에서 “Opaque Network”로 표시.
   * 터널 프로토콜: 



------



**한눈에 보는 NSX 개념 정리**

| **개념**                    | **설명**                                                     |
| --------------------------- | ------------------------------------------------------------ |
| **컴퓨트 매니저**           | vCenter 같은 VM 및 호스트를 관리하는 시스템                  |
| **관리 플레인**             | 설정 저장 및 관리, API/웹 UI 제공                            |
| **컨트롤 플레인**           | 네트워크 경로 계산 및 데이터 플레인에 전달                   |
| **데이터 플레인**           | 실제 패킷을 전달하는 계층                                    |
| **NSX Edge 노드**           | NAT, VPN, 로드 밸런싱 기능 제공                              |
| **TEP (터널 엔드포인트)**   | 오버레이 네트워크 트래픽 캡슐화                              |
| **세그먼트 (논리 스위치)**  | VM 및 게이트웨이를 연결하는 L2 네트워크                      |
| **Tier-0 게이트웨이**       | 외부 네트워크와 연결, BGP 지원, north-south를 핸들링하는 라우터 |
| **Tier-1 게이트웨이**       | 내부 네트워크 및 Tier-0 게이트웨이 연결, 자동 선택, east-west |
| **Distributed Firewall**    | VM 간 트래픽 보호                                            |
| **NAT, VPN, Load Balancer** | 네트워크 서비스 기능 제공                                    |
| **전송 존**                 | NSX 네트워크가 확장될 수 있는 범위                           |
| **Opaque Network**          | vSphere에서 직접 관리되지 않는 NSX 네트워크                  |
| **NSX Manager Cluster**     | NSX 관리의 고가용성 제공                                     |





------

**결론**

이 개념들을 이해하면 **NSX의 네트워크 가상화, 보안, 운영 원리**를 쉽게 파악할 수 있다.

**NSX는 네트워크의 모든 계층을 가상화하고, 온프레미스 및 클라우드 환경을 통합하여 관리하는 강력한 SDN(소프트웨어 정의 네트워킹) 솔루션이다.**



------

------

### 기타

**overlay protocol (L2 over L3)**

1. VxLAN: cisco ACl
2. GENEVE: vmware NSX
3. Cisco OTV (over Transport virtualization )
4. LISP



​     router header        	inner header

eht | ip | udp | geneve | eth(802.1q) | ip | data

tunneling protocol = overlay protocol