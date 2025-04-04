---
title: <h0>VMware NSX Install, Configure, Manage</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-14-cisco]
tags: [Network, Cloud, NSX]













---

#  VMware NSX: Install, Configure, Manage

------

## **NSX의 중요성**



**NSX는 네트워크 가상화 및 보안 플랫폼으로, 가상 클라우드 네트워크(Virtual Cloud Network)를 구현하는 핵심 기술이다.**



1. **가상 클라우드 네트워크란?**
   * **소프트웨어 정의 네트워킹(SDN) 방식으로 구축된 네트워크.**
   * **데이터 센터, 클라우드, 애플리케이션 프레임워크를 아우르는 확장성을 제공.**
   * **기존 물리적 네트워크 인프라에 의존하지 않고 유연한 네트워크 환경 구축 가능.**



2. **NSX의 역할**

* **애플리케이션 실행 환경에 최적화된 네트워킹 및 보안 제공**
  * 애플리케이션은 가상 머신(VM), 컨테이너, 베어메탈 서버에서 실행될 수 있음.
  * NSX는 애플리케이션이 실행되는 위치와 가까운 곳에서 네트워킹 및 보안 정책을 적용.

* **다양한 환경에서 일관된 네트워크 제공**
  * ESXi 호스트, 컨테이너 플랫폼, 베어메탈 서버, 퍼블릭 클라우드 환경 지원.

* **네트워크 및 보안 정책을 중앙에서 관리**
  * 방화벽, 마이크로 세그멘테이션, 로드 밸런싱, VPN 등의 기능을 통합 제공.



3. **NSX의 장점**
   * **유연성**: 환경에 맞는 기술을 선택하여 네트워크 구축 가능.
   * **운영 효율성**: 다양한 관리 도구(NSX Manager, API 등)를 통해 운영 및 관리 가능.
   * **보안 강화**: 마이크로 세그멘테이션을 통해 애플리케이션별 보안 정책을 적용 가능.



**결론**

NSX는 데이터 센터 및 클라우드 환경에서 **소프트웨어 정의 네트워킹과 보안을 제공**하여, **애플리케이션이 실행되는 위치에 최적화된 네트워크 인프라를 구축**하는 핵심 솔루션이다.

------

------

## **학습 목표 (Learner Objectives)**



이 과정에서는 **VMware NSX의 아키텍처, 주요 구성 요소, 기능, 배포 및 운영 방법**을 학습한다.



------



1. **NSX의 아키텍처 및 주요 구성 요소 설명**

   * NSX는 **네트워크 가상화 및 보안 플랫폼**으로, 데이터 센터, 클라우드, 애플리케이션을 아우르는 **소프트웨어 정의 네트워크(SDN) 환경**을 제공.

   * **주요 구성 요소**:
     * **NSX Manager**: NSX의 중앙 관리 및 API 인터페이스 제공.
     * **NSX Edge Nodes**: 게이트웨이 기능 제공(Tier-0, Tier-1 라우팅, VPN, NAT, LB 등)
     * **Distributed Switch & Router**: 분산 네트워크 및 논리적 라우팅 수행.
     * **NSX Security**: 분산 방화벽 및 마이크로 세그멘테이션 기능 제공.



------



2. **NSX의 기능 및 장점 설명**
   * **기능**:
     * **논리적 네트워크(Segments) 생성 및 관리.**
     * **논리적 라우팅(Tier-0, Tier-1 Gateway) 지원**.
     * **분산 방화벽(DFW) 및 게이트웨이 방화벽(GFW) 적용**.
     * **네트워크 자동화 및 확장 지원**.
     * **NSX Edge를 통한 부하 분산, NAT, VPN 기능 제공**.
     * **장점**:
       * **네트워크 가상화**를 통해 물리적 네트워크 인프라에 종속되지 않음.
       * **보안 강화**: 마이크로 세그멘테이션을 통한 애플리케이션 단위의 보안 제공.
       * **운영 효율성 증가**: 중앙 집중식 관리 및 자동화 지원.



------



3. **NSX 관리 클러스터 및 NSX Edge 노드 배포**

​	•	**NSX Manager를 구성하여 네트워크 가상화를 관리**.

​	•	**NSX Edge Nodes를 배포하여 게이트웨이 및 네트워크 서비스 활성화**.



------



**4. ESXi 호스트를 NSX 네트워크에 참여하도록 준비**

​	•	ESXi 호스트에 NSX VIB(커널 모듈)를 설치하여 **NSX의 데이터 패스 및 분산 방화벽 기능을 활성화**.

​	•	NSX-T에서 **Transport Zone을 구성하여 호스트 간 네트워크를 연결**.



------



**5. Layer 2 네트워크 세그먼트 생성 및 구성**

​	•	**NSX Segments를 생성하여 가상 머신 간 L2 네트워크 연결 제공**.

​	•	기존 VLAN 또는 오버레이 네트워크로 L2 트래픽을 전달할 수 있도록 구성.



------



**6. 논리적 라우팅: Tier-0 및 Tier-1 게이트웨이 구성**

​	•	**Tier-0 Gateway**: 데이터 센터와 외부 네트워크(Underlay) 간 연결을 담당.

​	•	**Tier-1 Gateway**: 가상 머신 및 내부 네트워크의 트래픽을 처리하고 Tier-0에 연결.

​	•	**다단계 논리적 라우팅을 통해 효율적인 트래픽 흐름을 구축**.



------



**7. NSX 분산 방화벽(DFW) 및 게이트웨이 방화벽(GFW) 활용**

​	•	**DFW(Distributed Firewall)**: 개별 VM 수준에서 **동서(East-West) 트래픽 필터링**.

​	•	**GFW(Gateway Firewall)**: 네트워크 경계를 통과하는 **남북(North-South) 트래픽 필터링**.

​	•	정책 기반 보안 설정을 통해 **트래픽 흐름을 제어하고 보안 강화**.



------



**8. 고급 위협 방지(Advanced Threat Prevention, ATP) 기능 구성**

​	•	**침입 탐지 및 방지 시스템(IDS/IPS), 악성코드 방어, 네트워크 트래픽 분석** 기능 활성화.

​	•	**AI 기반 위협 탐지 및 네트워크 이상 탐지**를 통해 보안 강화.



------



**9. NSX Edge 노드에서 네트워크 서비스 구성**

​	•	**NAT(Network Address Translation)** 설정.

​	•	**VPN 서비스(IPSec, SSL VPN) 활성화**.

​	•	**부하 분산(Load Balancing) 기능 구성**.



------



**10. VMware Identity Manager 및 LDAP을 사용한 사용자 및 접근 관리**

​	•	**NSX 관리자 및 운영자 계정 관리**.

​	•	LDAP 또는 VMware Identity Manager(SAML, AD 연동)로 **역할 기반 접근 제어(RBAC) 적용**.



------



**11. NSX Federation의 중요성 및 아키텍처 설명**

​	•	NSX Federation을 통해 **여러 데이터 센터 간 중앙 집중식 관리 및 정책 동기화 가능**.

​	•	여러 지역에 걸친 네트워크 확장을 지원하고, **고가용성 및 재해 복구(DR) 환경 구축 가능**.



------

### 순서 요약 정리

1. nsx management 배포
2. host preparation
3. edge cluster 배포
4. segment
5. gateway( t - 0, t - 1)
   * sr 사용 유무
   * single tier vs. multi tier
   * 라우팅 구성
6. 패킷 filtering (DFW, GFW)
7. 네트워크 서비스
   * nat
   * sbl -> nsx alb
   * dhcp
   * dns forwarder



------



**결론**



이 과정에서는 **NSX의 주요 기능과 아키텍처를 학습하고, 네트워크 가상화 및 보안을 구성하는 방법을 익힌다.**

NSX를 활용하면 **소프트웨어 정의 네트워크(SDN)를 구축하여 데이터 센터, 클라우드, 애플리케이션 환경을 통합적으로 운영할 수 있다.**

------

------

## **코스 개요 (Course Outline)**



이 과정에서는 **VMware NSX를 활용한 네트워크 가상화 및 보안 구축 방법**을 학습한다.



------



**1. 과정 소개 (Course Introduction)**

​	•	VMware NSX와 **가상 클라우드 네트워크(Virtual Cloud Network)** 개념을 학습.

​	•	소프트웨어 정의 네트워킹(SDN)을 활용하여 **데이터 센터, 클라우드, 애플리케이션 환경을 통합하는 방법**을 익힘.



------



**2. VMware 가상 클라우드 네트워크 및 NSX 개요**

​	•	VMware NSX가 제공하는 **네트워크 가상화 및 보안 기능 설명**.

​	•	기존 물리적 네트워크 인프라와 비교하여 **NSX의 장점 및 활용 사례 분석**.

​	•	**NSX-T와 NSX-V의 차이점 이해**.



------



**3. NSX 인프라 준비 (Preparing the NSX Infrastructure)**

​	•	NSX 배포를 위한 **필수 구성 요소 및 요구 사항 확인**.

​	•	**NSX Manager 및 NSX Edge 노드 설치 및 구성**.

​	•	ESXi 호스트에 NSX 커널 모듈(VIB) 설치 및 **호스트 준비(Transport Node 구성)**.



------



**4. NSX 논리적 스위칭 (NSX Logical Switching)**

​	•	**NSX Segments(VLAN 및 오버레이 네트워크) 생성 및 관리**.

​	•	VXLAN과 GENEVE를 활용한 **가상 네트워크 오버레이 구성**.

​	•	분산 스위칭(DVS)과의 차이점 및 활용 방법.



------



**5. NSX 논리적 라우팅 (NSX Logical Routing)**

​	•	Tier-0 및 Tier-1 라우터를 활용한 **논리적 라우팅 구성**.

​	•	**ESXi 호스트에서 실행되는 분산 라우터(DR)와 NSX Edge에서 실행되는 서비스 라우터(SR)의 역할 이해**.

​	•	**BGP 및 OSPF 동적 라우팅 구성**.



------



**6. NSX 논리적 브리징 (NSX Logical Bridging)**

​	•	물리 네트워크(VLAN)와 가상 네트워크(NSX Overlay) 간 **L2 연결을 위한 브릿지 구성**.

​	•	기존 VLAN 기반 워크로드와 **NSX 환경을 통합하는 방법 학습**.



------



**7. NSX 방화벽 (NSX Firewalls)**

​	•	**분산 방화벽(DFW)**: VM 간 트래픽(East-West)을 제어하는 마이크로 세그멘테이션 적용.

​	•	**게이트웨이 방화벽(GFW)**: 데이터 센터 경계를 통과하는 North-South 트래픽 제어.

​	•	**방화벽 정책 구성 및 적용**.



------



**8. NSX 고급 위협 방지 (NSX Advanced Threat Prevention)**

​	•	**IDS/IPS(침입 탐지 및 방지 시스템) 구성**.

​	•	네트워크 이상 탐지(NTA) 및 AI 기반 보안 분석.

​	•	실시간 보안 정책 적용을 통한 위협 대응.



------



**9. NSX 네트워크 서비스 (NSX Services)**

​	•	**NAT(Network Address Translation) 설정**.

​	•	**VPN(IPSec, SSL VPN) 구성**.

​	•	**부하 분산(Load Balancer) 서비스 활용**.





------



**10. NSX Federation**

​	•	여러 데이터 센터 간 **NSX 환경을 통합하는 방법** 학습.

​	•	**중앙 집중식 정책 관리 및 글로벌 네트워크 운영 모델 구축**.

​	•	NSX Federation을 활용한 **멀티 사이트 네트워크 확장 및 재해 복구(DR) 시나리오 구성**.

멀티 사이트



------



**결론**



이 과정에서는 **NSX의 주요 기능(네트워크 가상화, 보안, 라우팅, 방화벽, 연동 서비스)을 익히고, 실무 환경에서 NSX를 활용하여 소프트웨어 정의 데이터 센터(SDDC)를 구축하는 방법을 학습한다.**



------

------

## 컨트롤 플레인의 두 가지 구성 요소



컨트롤 플레인은 **중앙 컨트롤 플레인(CCP)**과 **로컬 컨트롤 플레인(LCP)**으로 나뉜다.



**1) 중앙 컨트롤 플레인(CCP, Central Control Plane)**

​	•	**NSX Manager 및 컨트롤러 클러스터에서 실행됨**

​	•	**네트워크 설정을 계산하고 최적의 경로를 찾음**

​	•	**각 트랜스포트 노드에 정보를 전달하여 전체적인 네트워크를 관리함**



**2) 로컬 컨트롤 플레인(LCP, Local Control Plane)**

​	•	**각 트랜스포트 노드에서 실행됨**

​	•	**CCP에서 받은 정보를 바탕으로 개별 노드에서 최적의 네트워크 동작을 수행**

​	•	**링크 상태를 모니터링하고 장애 발생 시 빠르게 대응**

------

## **컨트롤 플레인의 작동 방식**

컨트롤 플레인은 **네트워크 경로를 계산하고 설정을 배포하는 과정**에서 다음과 같이 작동한다.

​	1.	**NSX Manager(관리 플레인)**이 네트워크 정책을 생성한다.

​	2.	**CCP(중앙 컨트롤 플레인)**에서 정책을 분석하고, 각 트랜스포트 노드가 수행해야 할 역할을 정의한다.

​	3.	**LCP(로컬 컨트롤 플레인)**이 각 트랜스포트 노드에서 실제 동작을 수행하며, 네트워크 상태를 모니터링한다.

​	4.	**Data Plane(데이터 플레인)**이 실제 패킷을 전달하며, LCP가 이를 최적화한다.

------

------

### 기타

- NSX-V: ESX ver 6.4i (2018년부터 end of sales  (support) )



- NSX-T: ver 3.2 --> NSX, ESXi, KVM, bare metal (win, linux)

--> NSX : ESXi, bare metal (win, linux)



------

**NSX**

1. segment logical L2 switch

2. gateway: logical router
   - tier-0 gateway , tier-1 gateway

3. firewall: security platform
   - distributed firewall(동서[데이터센터 내] 트래픽에 대한 정책 적용), gateway firewall (남북[내부에서 외부]에 대한 트래픽 정책 적용)

4. Network services: DHCP, NAT, DNS cacher, SLB (server load balancer, NSX ALB 로 구현)