---
title: <h0Module 2 VMware Virtual Cloud Network and VMware NSX</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-14-cisco]
tags: [Network, Cloud]














---

# Lesson 1: VMware Virtual Cloud Network and VMware NSX

------

## **Virtual Cloud Network 프레임워크 개요**

![image-20250314112830272](/assets/cisco_post_img/2025-03-13-VMware NSX Module 2: VMware Virtual Cloud Network and VMware NSXmd//image-20250314112830272.png)





Virtual Cloud Network(VCN)는 **다양한 환경에서 실행되는 워크로드(VM, 컨테이너, 베어메탈)를 연결하고 보호하는 VMware의 네트워크 프레임워크**이다.



이 프레임워크는 **NSX 기술을 기반으로 구축**되었으며, **데이터 센터, 클라우드, 엣지 인프라 간 연결을 제공하면서 가시성과 보안을 강화**하는 역할을 한다.



------



**Virtual Cloud Network의 핵심 요소**

1. **워크로드 지원**
   * **가상 머신(VM)**: ESXi 또는 하이퍼바이저 환경에서 실행되는 VM 지원.
   * **컨테이너**: Kubernetes 기반 컨테이너 네트워크 지원.
   * **베어메탈 서버**: 물리 서버에서 실행되는 애플리케이션도 동일한 네트워크 정책을 적용 가능.

2. **연결 대상**
   * **프라이빗 클라우드** (Private Cloud): 온프레미스 데이터 센터에서 운영되는 워크로드 연결.
   * **퍼블릭 클라우드** (Public Cloud): AWS, Azure, Google Cloud 등과의 네트워크 연결.
   * **하이브리드 클라우드** (Hybrid Cloud): 온프레미스와 퍼블릭 클라우드를 통합한 환경.
   * **데이터 센터** (Data Center): 전통적인 기업 데이터 센터 네트워크 통합.
   * **브랜치/엣지 (Branch/Edge)**: 원격 사무실, IoT 환경, 엣지 컴퓨팅 인프라와 연결.



------



**온프레미스(온사이트) vs 클라우드**



1. **온프레미스 (On-Premises)**

   * **장비 보유형 환경**으로, 기업이 직접 물리적 하드웨어(서버, 스토리지, 네트워크)를 구축하고 운영.
   * 데이터센터에 **ESXi 또는 NSX 네트워크를 활용하여 가상화된 네트워크 환경을 구성** 가능.
   * 장점:
     * 보안 및 데이터 통제권 강화.
     * 커스터마이징 가능.

   * 단점:
     * 초기 인프라 구축 비용이 높음.
     * 유지보수 및 확장성의 한계.



2. **클라우드 환경**

   * 클라우드 서비스(AWS, Azure, Google Cloud)에서 제공하는 인프라를 사용하여 가상 머신, 컨테이너 등을 배포.
   * NSX 기반으로 **온프레미스와 퍼블릭 클라우드 간 일관된 네트워크 정책 적용 가능**.
   * 장점:
     * 신속한 확장성.
     * 인프라 운영 비용 절감.

   * 단점:
     * 데이터 통제권 감소.
     * 네트워크 비용 및 성능 이슈 발생 가능.



------



**결론**



**Virtual Cloud Network는 온프레미스와 클라우드를 아우르는 소프트웨어 정의 네트워크(SDN) 환경을 제공하며, NSX 기술을 활용하여 가상 머신, 컨테이너, 베어메탈 서버 간 일관된 네트워크 및 보안 정책을 유지할 수 있도록 한다.**

이를 통해 **기업은 자체 하드웨어(온프레미스)와 클라우드를 유연하게 연결하며 최적의 네트워크 인프라를 구축할 수 있다.**

------

------

## Product Name Changes to VMware NSX 4.x

![image-20250314113111861](/assets/cisco_post_img/2025-03-13-VMware NSX Module 2: VMware Virtual Cloud Network and VMware NSXmd//image-20250314113111861.png)

------

------

## **NSX의 주요 사용 사례 (Use Cases for NSX)**

![image-20250314120952576](/assets/cisco_post_img/2025-03-13-VMware NSX Module 2: VMware Virtual Cloud Network and VMware NSXmd//image-20250314120952576.png)

VMware NSX는 다양한 방식으로 활용될 수 있으며, 다음과 같은 주요 목적에 사용된다.



------



1. **보안(Security)**
   * **애플리케이션 중심의 보안 제공**
   * 각 워크로드 수준에서 **세분화된 보안 정책** 적용.
   * **마이크로 세그멘테이션**을 통해 VM, 컨테이너, 베어메탈 서버 간 **수평적(동서, East-West) 트래픽 보호**.
   * **침입 방지(IPS/IDS) 및 위협 감지 기능** 활용.
   * **Zero Trust 보안 모델 구현** 가능.



------



2. **멀티클라우드 네트워킹(Multicloud Networking)**
   * **온프레미스, 퍼블릭 클라우드, 하이브리드 클라우드 간 일관된 네트워킹 및 보안 제공**.
   * 다양한 클라우드 환경(AWS, Azure, Google Cloud)에서 **통합된 네트워크 정책 적용 가능**.
   * **멀티클라우드 운영 단순화 및 보안 강화**.



------



3. **자동화(Automation)**
   * **네트워크 및 보안 정책 자동화**를 통해 배포 속도 향상.
   * 수작업을 줄여 **인적 오류 방지 및 운영 효율성 증가**.
   * **API 및 인프라 코드(IaC)를 활용하여 NSX 환경 자동화 가능**.



------



4. **클라우드 네이티브 애플리케이션(Cloud-Native Applications)**
   * **컨테이너 기반 애플리케이션(Kubernetes 등) 지원**.
   * 컨테이너 네트워크 및 보안을 **애플리케이션 프레임워크에 맞게 자동 구성**.
   * **개발 환경과 운영 환경 간 일관된 네트워크 및 보안 정책 유지**.



------



**결론**



NSX는 **보안, 멀티클라우드 네트워킹, 자동화, 클라우드 네이티브 애플리케이션**을 지원하는 강력한 네트워크 가상화 및 보안 솔루션이다. 이를 통해 **기업은 네트워크를 최적화하고, 보안을 강화하며, 운영을 자동화할 수 있다.**

------

------

## **NSX 주요 기능 (NSX Features) - 1**



NSX는 **Platform, Networking, Security and Services** 영역에서 다양한 기능을 제공한다.



------



1. **플랫폼(Platform) 기능**
   * **정책 기반 구성** (Policy-driven configuration)
   * **베어메탈 서버 지원 (Bare-metal server support)**
   * **VM 및 컨테이너 지원 (Support for VMs and containers)**
   * **AWS 및 Azure 인스턴스 지원** (Support for Amazon AWS and Microsoft Azure instances)
   * **HTML5 기반 관리 UI 제공** (HTML 5 UI for management plane)
   * **DPDK 기반 NSX Edge 노드 지원** (Data Plane Development Kit 기반 NSX Edge: VM 및 베어메탈 지원)
   * **멀티사이트 및 페더레이션(Multisite/Federation) 지원**
   * **V2T(Migration from NSX-V to NSX-T) 마이그레이션 기능**
   * **DPU(데이터 프로세싱 유닛) 가속화 지원**



------



2. **네트워킹(Networking) 기능**
   * **Overlay 및 VLAN 기반 논리적 스위칭 지원**
   * **L2 브리징 및 QoS 지원**
   * **분산 라우팅(Distributed Routing) 기능** tier 1
   * **정적 라우팅 및 ECMP(동일 비용 다중 경로 라우팅) 지원**
   * **BGP 및 OSPF 라우팅 프로토콜 지원** tier 0
   * **중복 IP 감지 기능(Duplicate IP Detection)**
   * **BFD(Bidirectional Forwarding Detection)로 빠른 네트워크 컨버전스 지원**
   * **VRF Lite 및 EVPN 지원**
   * **속도 제한(Rate Limiting) 기능**
   * **Layer 3 멀티캐스트 지원**



------



3. **보안 및 서비스(Security and Services) 기능**
   * **게이트웨이 방화벽(Gateway Firewall) 및 분산 방화벽(Distributed Firewall) 제공**
   * **침입 탐지 및 방지 시스템(IDPS) 지원**
   * **악성코드 방어(Malware Prevention) 기능**
   * **URL 필터링 및 FQDN 분석 지원**
   * **TLS 트래픽 검사(TLS Inspection) 기능**
   * **NSX Intelligence: AI 기반 보안 분석 및 정책 추천 기능**
   * **NSX Network Detection & Response (NDR) 기능 지원**
   * **NAT 및 NAT64(Network Address Translation) 지원**
   * **DNS 및 DHCP 서비스 제공**
   * **로드 밸런싱(Load Balancing) 기능 포함**
   * **L2 VPN 및 IPSec VPN 기능 지원**



------



**결론**



NSX는 **네트워크 가상화, 멀티클라우드 네트워킹, 보안 및 자동화 기능을 제공하는 강력한 플랫폼**이다.

이를 통해 **데이터센터와 클라우드 환경에서 일관된 네트워크 및 보안 정책을 유지할 수 있다.**



------

------



## **NSX 추가 기능 (NSX Features - Automation, Operations, Troubleshooting)**

![image-20250314125842071](/assets/cisco_post_img/2025-03-13-VMware NSX Module 2: VMware Virtual Cloud Network and VMware NSXmd//image-20250314125842071.png)

NSX는 **자동화, 운영, 문제 해결(Troubleshooting)** 영역에서 강력한 기능을 제공하여 네트워크 관리 효율성을 극대화한다.



------



1. **자동화 (Automation) 기능**

   * **NSX API 및 Infrastructure as Code(IaC) 지원**
     * REST API 및 Ansible, Terraform 등을 통한 자동화 구현.

   * **컨테이너 네트워크 자동화 지원**
     * Kubernetes, OpenShift와의 통합 가능.

   * **NSX Policy 및 Intent-based Networking 적용**
     * 정책 기반 네트워크 자동화로 네트워크 변경을 단순화.

   * **자동 프로비저닝 및 NSX 환경 구축**
     * 신규 워크로드에 대한 네트워크 및 보안 정책 자동 적용.



------



2. **운영 (Operations) 기능**

   * **NSX Intelligence를 통한 네트워크 가시성 향상**
     * 머신러닝 기반 트래픽 분석 및 정책 추천.

   * **NSX Federation을 통한 멀티사이트 네트워크 운영 지원**
     * 단일 인터페이스에서 여러 데이터센터 및 클라우드 관리.

   * **VMware Aria Operations for Networks(VRNI)와 연동하여 트래픽 최적화**
     * 실시간 네트워크 분석 및 트러블슈팅 지원.

   * **NSX Manager를 통한 네트워크 및 보안 정책 중앙 관리**
     * GUI 기반 관리 및 대시보드 제공.



------



3. **문제 해결 (Troubleshooting) 기능**

   * **Traceflow를 활용한 네트워크 패킷 경로 분석**
     * 패킷 흐름을 시뮬레이션하여 문제 원인 분석.

   * **연결 상태 모니터링(Connectivity Monitoring) 기능**
     * 특정 VM 또는 네트워크 구성 요소 간 연결성 확인.

   * **IPFIX(NetFlow) 기반 트래픽 분석 지원**
     * NSX 환경에서의 실시간 트래픽 모니터링 가능.

   * **분산 방화벽(DFW) 로그 분석 기능 제공**
     * 보안 정책이 올바르게 적용되었는지 검증 가능.



------



**결론**



NSX는 **자동화, 운영 최적화, 문제 해결 기능을 강화하여 네트워크 및 보안 관리를 더욱 효율적으로 수행할 수 있도록 지원한다.**

이를 통해 **관리자는 멀티클라우드 환경에서도 일관된 네트워크 운영을 유지하고, 보안 정책을 신속하게 적용하며, 문제를 효과적으로 해결할 수 있다.**

------

------

## **NSX의 고수준 아키텍처 (High-Level Architecture of NSX)**

![image-20250314152206060](/assets/cisco_post_img/2025-03-13-VMware NSX Module 2: VMware Virtual Cloud Network and VMware NSXmd//image-20250314152206060.png)



NSX 아키텍처는 **관리(Management), 제어(Control), 데이터(Data) 플레인**의 세 가지 주요 요소로 구성된다.

이러한 분리는 **확장성(Scalability)과 네트워크 성능 향상**을 가능하게 한다.



------



1. **관리 플레인 (Management Plane)**

   * **역할**:
     * 사용자 설정 및 정책을 저장하고 관리.
     * NSX 환경의 API 요청 처리 및 UI 제공.

   * **구성 요소**:
     * **NSX Manager**
     * **REST API 및 웹 UI 인터페이스 제공**.

   * 모든 NSX 설정을 중앙에서 관리.
   * API 기반으로 vRealize Automation, OpenStack, Kubernetes 등과 통합 가능.
   * **NSX Management Cluster**
   * 고가용성을 위해 여러 개의 NSX Manager 노드를 클러스터링.



------



2. **제어 플레인 (Control Plane)**

   * **역할**:
     * 가상 네트워크 및 보안 상태 계산 및 배포.
     * 데이터 플레인(ESXi, NSX Edge)과 동기화하여 네트워크 경로 설정.

   * **구성 요소**:

     * **중앙 컨트롤 플레인 (Central Control Plane, CCP)**
       * 전체 NSX 네트워크의 상태 관리.
       * 네트워크 경로 및 보안 정책을 계산하여 로컬 컨트롤 플레인에 전달.

     * **로컬 컨트롤 플레인 (Local Control Plane, LCP)**
       * ESXi 호스트 및 NSX Edge에서 실행.
       * 중앙 컨트롤 플레인(CCP)에서 받은 정보를 바탕으로 로컬에서 패킷을 처리.

   * **NSX Manager**와 통합되어 **관리 플레인과 컨트롤 플레인이 하나의 노드에서 동작 가능**.



------



3. 데이터 플레인 (Data Plane)

   * **역할**:
     * 실제 패킷 전송을 담당.
     * 네트워크 트래픽을 분산 처리하여 성능 향상.

   * **구성 요소**:
     * **ESXi 호스트 및 VDS (vSphere Distributed Switch)**
     * vCenter에서 관리하는 ESXi 환경에서는 **VDS를 통해 NSX 트래픽을 처리**.

   * **NSX Edge 노드**
     * NAT, VPN, 로드 밸런싱 등의 네트워크 서비스를 제공.

   * **전송 노드 (Transport Nodes)**
     * NSX가 적용된 **ESXi 호스트 및 Edge 노드를 포함하는 데이터 플레인**.



------



4. **소비 플레인 (Consumption Plane)**
   * **NSX를 사용하는 클라우드 관리 플랫폼(CMP)과의 통합 인터페이스**.
   * **NSX UI, REST API, vRealize Automation, Kubernetes, OpenStack 등과 연결 가능**.
   * **사용자는 CMP를 통해 네트워크 가상화를 활용하여 애플리케이션을 배포**.



------



**NSX 아키텍처 요약**

| **플레인**                          | **역할**                         | **주요 구성 요소**                         |
| ----------------------------------- | -------------------------------- | ------------------------------------------ |
| **관리 플레인 (Management Plane)**  | API 요청 처리, 설정 저장 및 관리 | NSX Manager, Management Cluster            |
| **제어 플레인 (Control Plane)**     | 네트워크 경로 계산 및 배포       | CCP(중앙 컨트롤), LCP(로컬 컨트롤)         |
| **데이터 플레인 (Data Plane)**      | 실제 패킷 전송 및 트래픽 처리    | ESXi 호스트, NSX Edge, VDS, 전송 노드      |
| **소비 플레인 (Consumption Plane)** | NSX 활용을 위한 CMP 통합         | vRealize Automation, Kubernetes, OpenStack |





------



**결론**



NSX는 **관리(Management), 제어(Control), 데이터(Data) 플레인**의 구조를 통해 **확장성과 성능을 극대화하는 네트워크 가상화 플랫폼**이다.

이를 통해 기업은 **데이터 센터, 클라우드, 하이브리드 환경에서 일관된 네트워크 및 보안 정책을 운영할 수 있다.**

------

------

## **NSX 관리 및 제어 플레인 (Management and Control Planes)**



NSX에서는 **관리 플레인(Management Plane)과 제어 플레인(Control Plane)이 하나의 NSX 관리 클러스터(NSX Management Cluster) 내에서 운영된다.**



------



**1. 관리 플레인 (Management Plane)**

​	•	**역할**:

​	•	**사용자 요청을 처리**하고 **설정 데이터를 관리**.

​	•	REST API 및 웹 기반 UI(NSX Manager)를 제공.

​	•	**vCenter, Cloud Service Manager, NSX Container Plug-in 등과 연동**하여 네트워크 자동화 및 가상화 관리.

​	•	**구성 요소**:

​	•	**NSX Manager**

​	•	API 및 웹 UI 제공.

​	•	사용자가 NSX 환경을 구성하는 모든 요청을 처리.

​	•	**NSX Management Cluster**

​	•	여러 개의 NSX Manager 노드를 포함하여 **고가용성(HA) 및 확장성 보장**.



------



**2. 제어 플레인 (Control Plane)**

​	•	**역할**:

​	•	NSX 환경의 **네트워크 상태를 계산하고 배포**.

​	•	데이터 플레인(ESXi 호스트 및 NSX Edge)과 동기화하여 **네트워크 경로 설정 및 트래픽 최적화**.

​	•	**구성 요소**:

​	•	**중앙 컨트롤 플레인 (Central Control Plane, CCP)**

​	•	전체 네트워크의 상태를 계산하고 로컬 컨트롤 플레인(LCP)에 전달.

​	•	**로컬 컨트롤 플레인 (Local Control Plane, LCP)**

​	•	개별 ESXi 호스트 및 NSX Edge에서 실행되며, 패킷 포워딩을 담당.



------



**3. 주요 통합 요소**

​	•	**Cloud Service Manager**

​	•	클라우드 환경(AWS, Azure 등)과 연동하여 네트워크를 확장.

​	•	**NSX Container Plug-in**

​	•	Kubernetes 등의 컨테이너 환경과 통합하여 네트워크 자동화.

​	•	**vCenter Server**

​	•	ESXi 환경을 NSX와 연계하여 관리.



------



**NSX 관리 및 제어 플레인 요약**

| **플레인**                         | **역할**                      | **구성 요소**                                                |
| ---------------------------------- | ----------------------------- | ------------------------------------------------------------ |
| **관리 플레인 (Management Plane)** | 설정 저장 및 사용자 요청 처리 | NSX Manager, REST API, UI, Management Cluster                |
| **제어 플레인 (Control Plane)**    | 네트워크 경로 계산 및 배포    | CCP(중앙 컨트롤), LCP(로컬 컨트롤)                           |
| **연동 시스템**                    | 외부 시스템과 통합            | vCenter Server, Cloud Service Manager, NSX Container Plug-in |





------



**결론**



NSX는 **관리 플레인과 제어 플레인을 통합하여 중앙 집중식 운영을 가능하게 하며, 다양한 클라우드 및 컨테이너 환경과 연계하여 확장성을 극대화한다.**

이를 통해 **데이터 센터 및 멀티클라우드 환경에서 일관된 네트워크 및 보안 정책을 유지할 수 있다.**

------

------

## **NSX 관리 클러스터 (NSX Management Cluster) 개요**

![image-20250314165501800](/assets/cisco_post_img/2025-03-13-VMware NSX Module 2: VMware Virtual Cloud Network and VMware NSXmd//image-20250314165501800.png)

NSX 관리 클러스터(NSX Management Cluster)는 **고가용성(HA) 및 확장성(Scalability)을 위해 3개의 NSX Manager 노드로 구성**된다.



------



**1. NSX 관리 클러스터의 역할 및 구성**

​	•	**NSX Manager 노드 개수**: 기본적으로 3개의 노드로 구성.

​	•	**각 노드에는 관리(Management) 및 제어(Control) 기능이 포함됨**.

​	•	**분산된 지속형 데이터베이스(Distributed Persistent Database)를 통해 모든 노드가 동일한 상태를 유지**.

| **플레인**                                        | **구성 요소**                      | **설명**                              |
| ------------------------------------------------- | ---------------------------------- | ------------------------------------- |
| **관리 플레인 (Management Plane)**                | **정책 역할(Policy Role)**         | 네트워크 및 보안 정책을 정의하고 저장 |
|                                                   | **매니저 역할(Manager Role)**      | API/UI를 통해 설정을 관리             |
| **중앙 제어 플레인 (Central Control Plane, CCP)** | **컨트롤러 역할(Controller Role)** | 네트워크 상태를 계산하고 배포         |
| **데이터 저장소**                                 | **분산 지속형 데이터베이스**       | 모든 노드가 동일한 설정을 공유        |





------



**2. NSX Manager의 통합 기능**



NSX Manager는 **정책(Policy), 관리자(Manager), 컨트롤러(Controller) 역할을 하나의 어플라이언스로 통합**하여 운영됨.

따라서, **추가적인 VM 설치 없이 단일 NSX Manager 배포로 모든 역할을 수행할 수 있음**.

​	•	**모든 NSX Manager 노드는 API/UI 요청을 처리 가능** → 요청을 공유하여 부하를 분산.

​	•	**노드 간 상태 동기화** → 한 노드에서 변경된 설정이 다른 노드에도 자동 적용됨.



------



**3. NSX Manager의 크기 및 배포 옵션**



NSX Manager는 환경 규모에 따라 **다양한 크기로 제공**됨.

| **배포 크기**    | **사용 사례**                                    |
| ---------------- | ------------------------------------------------ |
| **소형(Small)**  | 실험실 또는 PoC(Proof of Concept) 환경           |
| **중형(Medium)** | 최대 64개의 호스트를 지원하는 일반적인 배포 환경 |
| **대형(Large)**  | 대규모 고객 환경에서 사용                        |

**자세한 정보**: [VMware Configuration Maximums](https://configmax.vmware.com)



------



**4. NSX 관리 클러스터의 이점**

​	•	**고가용성(HA) 제공**: 3개의 NSX Manager 노드가 서로 백업 역할을 수행.

​	•	**확장성 보장**: 트래픽 부하를 나누어 효율적인 관리 가능.

​	•	**데이터 동기화 유지**: 한 노드에서 변경된 설정이 모든 노드에 반영됨.

​	•	**통합 아키텍처**: 추가적인 VM 없이 단일 어플라이언스로 모든 기능 수행 가능.



------



**결론**



NSX 관리 클러스터는 **3개의 NSX Manager 노드로 구성되며, 모든 노드가 동일한 설정을 공유하면서 API 요청을 분산 처리할 수 있도록 설계**됨.

이를 통해 **고가용성(HA) 및 확장성(Scalability)을 보장하며, 통합 아키텍처를 통해 배포 및 운영이 단순화**된다.



------

------

## **NSX 관리 클러스터와 가상 IP(Virtual IP) 개요**

![image-20250314165526941](/assets/cisco_post_img/2025-03-13-VMware NSX Module 2: VMware Virtual Cloud Network and VMware NSXmd//image-20250314165526941.png)

NSX 관리 클러스터는 **고가용성(HA, High Availability)을 제공하며, 단일 가상 IP(VIP)를 통해 관리된다**.



------



**1. NSX 관리 클러스터(VIP)의 주요 특징**

​	•	**모든 NSX Manager 노드는 동일한 서브넷(Same Subnet)에 있어야 함**.

​	•	**하나의 Manager 노드가 리더(Leader)로 선출됨**.

​	•	**리더 노드에 클러스터 가상 IP(VIP)가 할당됨**.

​	•	**VIP는 API 및 GUI 클라이언트(NSX Manager)에 대한 단일 접속 지점 역할**.

​	•	**리더 노드 장애 발생 시, 새로운 리더가 자동 선출되며 VIP를 인계받음**.

​	•	**VIP를 사용하면 트래픽이 NSX Manager 노드 간 로드밸런싱되지 않음**.



------



**2. VIP를 활용한 트래픽 흐름**

​	•	**VIP는 API 및 GUI 요청을 처리하는 단일 접속 지점 역할**.

​	•	**사용자가 VIP로 요청을 보내면, 리더 노드가 이를 처리**.

​	•	**트래픽이 NSX Manager 노드 간에 분산되지 않고, 리더 노드에서만 처리됨**.

​	•	**트랜스포트 노드(ESXi, NSX Edge)에서 발생하는 트래픽은 개별 NSX Manager 노드의 관리 IP를 사용**.



------



**3. 리더 노드 장애(Failover) 처리 방식**

​	•	**현재 리더 노드가 장애 발생 시, 남은 두 개의 NSX Manager가 새로운 리더를 선출**.

​	•	**새로운 리더 노드는 GARP(Gratuitous ARP) 요청을 네트워크에 브로드캐스트하여 VIP를 소유**.

​	•	**새 리더 노드가 VIP를 인계받으면 이후의 모든 API 및 UI 요청을 처리**.



------



**4. VIP 기반 NSX 클러스터의 특징 및 제한 사항**

| **항목**                     | **설명**                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| **VIP 역할**                 | API 및 GUI 요청을 리더 노드가 단독 처리                      |
| **리더 노드 장애 시**        | 새로운 리더 노드가 자동 선출, VIP 인계(GARP 사용)            |
| **로드밸런싱 여부**          | VIP 사용 시 트래픽이 분산되지 않음 (리더 노드가 모든 요청을 처리) |
| **트랜스포트 노드와의 통신** | 개별 NSX Manager 노드의 관리 IP 사용                         |
| **고가용성(HA) 지원**        | 리더 장애 발생 시 자동 복구                                  |





------



**5. NSX 관리 클러스터 VIP의 장점과 고려 사항**



 **장점**

​	•	**단일 접속 지점(VIP) 제공**: 관리자가 NSX 환경을 보다 쉽게 접근 가능.

​	•	**자동 페일오버(HA 지원)**: 리더 장애 발생 시 자동 복구로 서비스 연속성 유지.

​	•	**설정 및 운영 단순화**: VIP를 활용하여 특정 NSX Manager 노드의 IP를 기억할 필요 없음.



⚠ **고려 사항**

​	•	**로드밸런싱 미지원**: API/UI 트래픽이 리더 노드에 집중될 수 있음.

​	•	**네트워크 장애 발생 시 GARP 동작 필요**: 새 리더 노드가 VIP를 가져가기 위해 GARP 요청을 브로드캐스트해야 함.



------



**6. 결론**



NSX 관리 클러스터는 **VIP를 사용하여 하나의 단일 접속 지점**을 제공하며, **리더 노드 장애 발생 시 새로운 리더가 자동 선출되어 VIP를 인계받음으로써 고가용성을 보장**한다.

그러나 **VIP 기반으로 동작할 경우 트래픽이 특정 리더 노드에 집중되므로, 성능 최적화를 위해 트래픽 부하를 고려해야 한다**.

------

------

## **NSX 관리 클러스터와 로드 밸런서(Load Balancer) 구성**

![image-20250314165749617](/assets/cisco_post_img/2025-03-13-VMware NSX Module 2: VMware Virtual Cloud Network and VMware NSXmd//image-20250314165749617.png)





NSX 관리 클러스터는 **로드 밸런서를 활용하여 고가용성(HA)을 보장**할 수 있다.



------



**1. NSX 관리 클러스터(Load Balancer) 주요 특징**

​	•	**모든 NSX Manager 노드가 활성 상태(Active)로 동작**.

​	•	**API 및 GUI 요청을 모든 NSX Manager에서 처리 가능**.

​	•	**트래픽이 가상 IP(VIP)로 들어오면 여러 Manager 노드로 로드 밸런싱됨**.

​	•	**NSX Manager 노드들이 서로 다른 서브넷(Different Subnet)에 위치 가능**.

​	•	**NSX 또는 서드파티(Third-Party) 로드 밸런서를 통해 트래픽을 분산**.



------



**2. VIP 기반 트래픽 로드 밸런싱 구조**

​	•	**클라이언트(API 또는 GUI)는 VIP로 요청을 보냄**.

​	•	**로드 밸런서가 VIP를 통해 들어오는 요청을 여러 NSX Manager 노드로 분산**.

​	•	**모든 NSX Manager 노드는 API/UI 요청을 처리할 수 있음**.

​	•	**특정 Manager 노드가 장애 발생 시, 로드 밸런서는 나머지 정상 노드로 트래픽을 전환**.



------



**3. 기존 VIP 방식과의 차이점**

| **구분**         | **VIP 기반 NSX 클러스터**       | **로드 밸런서 기반 NSX 클러스터**         |
| ---------------- | ------------------------------- | ----------------------------------------- |
| **노드 상태**    | 리더 노드 1개 + 팔로워 2개      | 모든 노드 활성화(Active)                  |
| **트래픽 분산**  | 단일 리더 노드가 모든 요청 처리 | 모든 NSX Manager 노드가 트래픽 처리       |
| **서브넷 제한**  | 동일한 서브넷 필요              | 서로 다른 서브넷 지원 가능                |
| **고가용성(HA)** | 리더 장애 시 새 리더 선출       | 특정 노드 장애 시 다른 노드로 트래픽 전환 |





------



**4. 로드 밸런서 기반 NSX 클러스터의 장점과 고려 사항**



 **장점**

​	•	**고가용성 강화**: 모든 노드가 활성 상태로 동작하여 장애 발생 시 자동 복구.

​	•	**로드 밸런싱 가능**: API/UI 요청을 여러 노드로 분산하여 성능 최적화.

​	•	**서브넷 제약 없음**: 서로 다른 서브넷에 위치한 NSX Manager 노드 지원 가능.



⚠ **고려 사항**

​	•	**로드 밸런서 추가 필요**: NSX 기본 구성에 포함되지 않으며 별도 구성 필요.

​	•	**설정 복잡성 증가**: 로드 밸런서 설정이 필요하며 유지보수 부담이 증가할 수 있음.



------



**5. 결론**



NSX 관리 클러스터에서 **로드 밸런서를 사용하면 모든 NSX Manager 노드를 활성화하고, 트래픽을 분산하여 성능과 가용성을 향상**시킬 수 있다.

그러나 **로드 밸런서 추가 구성 및 유지보수 필요성도 고려해야 한다**.

------

------





------

------

### 기타

overlay (logical infra) <-> underlay (physical infra)

