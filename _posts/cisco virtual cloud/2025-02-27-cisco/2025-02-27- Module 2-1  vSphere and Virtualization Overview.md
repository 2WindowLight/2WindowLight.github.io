---
title: <h0>Module 2-1vSphere and Virtualization Overview</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-27-cisco]
tags: [Network, Cloud]








---

# Module 2-1 : vSphere and Virtualization Overview

## **가상화 관련 주요 용어 정리**

가상화는 여러 핵심 개념, 제품, 기능과 관련이 있다.

| **용어(Term)**                             | **정의(Definition)**                                         | **예시(Examples)**        |
| ------------------------------------------ | ------------------------------------------------------------ | ------------------------- |
| **운영 체제 (Operating System)**           | 물리적 자원을 애플리케이션에 할당하는 소프트웨어             | Microsoft Windows, Linux  |
| **애플리케이션 (Application)**[OS 위]      | 운영 체제에서 실행되며, 물리적 자원을 소비하는 소프트웨어    | Microsoft Office, Chrome  |
| **하이퍼바이저 (Hypervisor)** [ESXi]       | 가상 머신(VM)을 실행하기 위해 설계된 특수한 운영 체제        | ESXi, Workstation, Fusion |
| **가상 머신 (Virtual Machine, VM)**        | 하드웨어 리소스를 소프트웨어로 추상화하는 특수한 애플리케이션 | -                         |
| **게스트 (Guest)** [가상 머신에 설치된 OS] | 가상 머신 내에서 실행되는 운영 체제 (게스트 OS라고도 함)     | Microsoft Windows, Linux  |
| **호스트 (Host)**                          | ESXi 하이퍼바이저에 자원을 제공하는 물리적 컴퓨터            | -                         |

이 표는 **가상화 기술에서 사용되는 주요 개념 및 역할**을 요약한 것이다.

------

**가상화 관련 주요 용어 정리 (2)**

| **용어(Term)**       | **정의(Definition)**                                         |
| -------------------- | ------------------------------------------------------------ |
| **vSphere**          | VMware의 서버 가상화 제품으로, ESXi 하이퍼바이저와 vCenter Server 관리 플랫폼을 결합한 솔루션 |
| **Datastore**        | 가상 머신 파일을 저장하는 스토리지 위치                      |
| **Maintenance Mode** | 유지보수 작업을 수행하기 전에 ESXi 호스트 및 데이터스토어를 지정하는 모드 |
| **Cluster**          | **가상 머신(VM)이 공유하는 ESXi 호스트 그룹** (그룹이 아니면 HA 기능을 사용 못함) |
| **vSphere vMotion**  | 실행 중인 가상 머신(VM)을 **서비스 중단 없이** 한 호스트에서 다른 호스트로 마이그레이션하는 기능 |
| **vSphere DRS**      | vSphere vMotion을 활용하여 VM을 호스트에 자동 배치하고, VM이 필요한 리소스를 받을 수 있도록 조정하는 클러스터 기능 |
| **vSphere HA**       | 호스트 하드웨어 장애 발생 시 정상적으로 실행 중인 호스트에서 VM을 자동으로 재시작하여 보호하는 클러스터 기능 |

이 표는 **vSphere 환경에서 필수적으로 사용되는 개념 및 기능**을 정리한 것이다.

------

## Data Center Physical Infrastructure

![image-20250227112611897](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227112611897.png)

이 그림은 **물리적 호스트(Physical Host), 운영 체제(OS), 애플리케이션(APP)**이 **Ethernet과 Fibre Channel을 통해 다양한 스토리지와 연결되는 방식**을 보여준다.

**1. 주요 구성 요소**

- **물리적 호스트(Physical Host)**

  - 여러 개의 서버가 있으며, 각 서버에는 **운영 체제(OS)와 애플리케이션(APP)이 실행됨**

  - 이 서버들은 네트워크를 통해 스토리지와 연결됨

- **네트워크 연결 방식**

  - **Ethernet(이더넷)**: iSCSI Storage 및 NFS Storage에 연결

  - **Fibre Channel(파이버 채널)**: Fibre Channel Storage에 연결

- **스토리지 유형**

  - **iSCSI Storage**: TCP/IP 기반의 **iSCSI 프로토콜**을 사용하여 스토리지에 접근

  - **NFS Storage**: **네트워크 파일 시스템(NFS)**을 통해 파일 공유 방식으로 스토리지에 접근

  - **Fibre Channel Storage**: 고속 **Fibre Channel 네트워크**를 통해 스토리지에 연결됨

**2. 핵심 개념 요약**

- **물리적 호스트(서버)는 운영 체제 및 애플리케이션을 실행하며, 네트워크를 통해 스토리지에 연결됨**

- **Ethernet 기반 연결(iSCSI, NFS)과 Fibre Channel 기반 연결이 스토리지와의 통신을 담당함**

- **iSCSI와 NFS는 일반적인 네트워크(Ethernet)에서 동작하고, Fibre Channel은 별도의 전용 네트워크를 사용하여 고성능 스토리지 연결을 제공**



이 그림은 **가상화 환경에서 스토리지 연결 방식의 개요**를 보여주는 구조도이다.

------

## 가상 환경

![image-20250227113630549](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227113630549.png)

검은색 선 : 실제 데이터가 지나라는 링크 (production link)

녹색 선 : 스토리지 트래픽이 지나가는 링크 즉, 경로

보라색 선 : 관리용 트래픽이 지나라는 링크 (manangement link)

**가상화 환경에서의 ESXi 호스트 및 스토리지 연결**



이 그림은 **VMware vSphere 환경에서 ESXi 호스트와 스토리지가 어떻게 연결되는지**를 보여준다.

**1. 주요 구성 요소**

-  **가상 머신(Virtual Machines)**

  - 각 가상 머신은 **운영 체제(OS)와 애플리케이션(APP)**을 실행

  - 여러 개의 가상 머신이 **하나의 물리적 서버(ESXi Host)에서 실행됨**

- **하이퍼바이저(Hypervisor)**

  - **VMware vSphere** 하이퍼바이저가 **ESXi 호스트에서 실행되며 가상 머신을 관리**

  - 하이퍼바이저를 통해 가상 머신과 물리적 하드웨어가 연결됨

- **ESXi 호스트(ESXi Host)**
  - 물리적인 서버이며, 가상 머신을 실행하는 **컴퓨팅 리소스를 제공**
  - *이더넷(Ethernet) 및 파이버 채널(Fibre Channel)을 통해 스토리지에 연결**

**2. 네트워크 및 스토리지 연결**

- **이더넷(Ethernet) 연결**
  - **iSCSI Storage 및 NFS Storage**에 연결
  - 일반적인 네트워크를 통해 스토리지에 접근
  - **iSCSI**: 블록 스토리지 방식, 네트워크를 통해 디스크처럼 사용
  - **NFS**: 파일 공유 스토리지 방식, 여러 호스트가 공유 가능

- **파이버 채널(Fibre Channel) 연결**
  - **Fibre Channel Storage**에 연결
  - 고속 스토리지 네트워크로 안정적이고 높은 성능 제공

**3. 핵심 개념 요약**

- **ESXi 호스트는 하이퍼바이저(VMware vSphere)를 통해 가상 머신을 실행**
- *네트워크(Ethernet) 또는 고속 스토리지 네트워크(Fibre Channel)를 통해 저장 장치와 연결됨***
- iSCSI 및 NFS는 일반 네트워크 기반 스토리지, Fibre Channel은 전용 고속 스토리지*
- *이러한 구성을 통해 가상 머신이 안정적으로 실행되고 필요한 스토리지에 접근 가능**



이 그림은 **VMware vSphere 환경에서 가상 머신, ESXi 호스트, 스토리지 연결 구조를 시각적으로 표현한 것**이다.



------

## About Virtual Machines

![image-20250227120008637](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227120008637.png)

가상 머신(VM)은 **물리적 컴퓨터 및 구성 요소를 소프트웨어로 구현한 것**이다.

**1. 가상 머신의 주요 구성 요소**

1.	**게스트 운영 체제(Guest OS)**

​	•	가상 머신에서 실행되는 운영 체제

​	•	Windows, Linux 등 다양한 OS 설치 가능

2. **VMware Tools** ( 즁요)

​	•	VMware 환경에서 가상 머신의 성능을 최적화하고, 기능을 확장하는 소프트웨어

3.	**가상 리소스(Virtual Resources)**

​	•	**CPU 및 메모리**: 가상 머신이 실행되기 위한 컴퓨팅 자원

​	•	**네트워크 어댑터(Network Adapters)**: 가상 머신이 네트워크와 통신할 수 있도록 지원

​	•	**디스크 및 컨트롤러(Disks and Controllers)**: 가상 머신의 저장 장치 및 데이터 전송을 담당

​	•	**GPU**: 가상 머신에서 그래픽 처리 기능을 지원

**2. 핵심 개념 요약**

​	•	**가상 머신은 물리적 하드웨어 없이 독립적으로 실행되는 컴퓨팅 환경**

​	•	**게스트 운영 체제와 가상화된 하드웨어 자원을 포함하여 독립적인 실행이 가능**

​	•	**VMware Tools를 사용하면 성능 최적화 및 추가 기능 활용 가능**



이 그림은 **가상 머신의 개념과 주요 구성 요소를 시각적으로 나타낸 것**이다.

------

## **가상 머신(VM) 사용의 이점**

![image-20250227120225558](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227120225558.png)

## 



**물리적 머신의 한계**

1. **이동 및 복사가 어려움**

​	•	물리적 하드웨어에 종속되어 있어 시스템을 다른 환경으로 이전하기 어려움

2. **특정 하드웨어에 종속됨**

​	•	물리 서버는 CPU, 메모리, 저장장치 등 특정 하드웨어 구성에 의존

3. **수명이 짧음**

​	•	하드웨어 노후화로 인해 일정 기간 사용 후 교체가 필요

4. **업그레이드 시 물리적 작업 필요**

​	•	하드웨어를 업그레이드하려면 직접 장비를 교체해야 함

**가상 머신(VM)의 장점**



✅ **이동성과 복사 용이**: VM 파일만 복사하면 쉽게 다른 서버로 이동 가능

✅ **하드웨어 독립성**: 특정 하드웨어에 종속되지 않으며 다양한 환경에서 실행 가능

✅ **수명 연장 가능**: 물리적 하드웨어 교체 없이 지속적인 운영 가능

✅ **원격 관리 가능**: 하드웨어 업그레이드 없이 VM 리소스를 동적으로 조정 가능



가상 머신을 사용하면 **물리적 하드웨어의 한계를 극복하고, 유연성과 효율성을 극대화할 수 있다**.

------

![image-20250227120318161](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227120318161.png)

## **가상 머신(VM)의 장점**

1. **이동 및 복사가 용이**

​	•	VM은 **파일 형태로 저장**되므로, 다른 서버로 쉽게 이동하거나 복사 가능

2. **물리적 하드웨어와 독립적**

​	•	가상 머신은 **파일로 encapsulation(캡슐화)되어** 특정 하드웨어에 종속되지 않음

​	•	다양한 하드웨어 환경에서 실행 가능

3. **다른 VM과 격리된 환경 제공**

​	•	동일한 물리 서버에서 여러 VM이 실행되더라도 **각 VM은 독립적으로 동작**

​	•	한 VM의 장애가 다른 VM에 영향을 주지 않음

4. **물리적 하드웨어 변경에 영향 없음**

​	•	하드웨어를 변경하거나 업그레이드해도 **VM 운영에 영향을 미치지 않음**



✅ **VM을 사용하면 유연성과 이동성이 향상되며, 안정적인 가상화 환경을 구축할 수 있다.**

------

![image-20250227120556349](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227120556349.png)

## **vSphere 개요**



**vSphere**는 **가상화 플랫폼**으로, 가상 머신(VM)을 운영하는 두 가지 핵심 관리 구성 요소를 포함한다.

**1. 주요 구성 요소**

​	1.	**ESXi**

​	•	**하이퍼바이저(Hypervisor)** 역할 수행

​	•	가상 머신(VM)을 실행하는 **기반 인프라**

​	2.	**vCenter**

​	•	ESXi 호스트, 가상 머신, 스토리지, 네트워크 등을 **중앙에서 관리하는 플랫폼**

​	•	여러 ESXi 호스트를 하나의 관리 인터페이스에서 통합 관리 가능

**2. 그림 설명**

​	•	**vCenter**가 **여러 ESXi 호스트를 중앙에서 관리**

​	•	**각 ESXi 호스트는 다수의 가상 머신(VM)을 실행**

​	•	**vSphere 네트워크를 통해 스토리지와 연결**, 모든 ESXi 호스트가 공유 스토리지에 접근 가능

​	•	**가상화 환경의 핵심 인프라를 구성하며, 효율적인 리소스 관리 및 확장성을 제공**



✅ **vSphere를 사용하면 가상 머신을 중앙에서 관리하고, 고가용성(HA), 부하 분산(DRS), 자동화 기능을 활용할 수 있다.**

------

## Types of Virtualization

![image-20250227120806705](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227120806705.png)

Server Virtualization : VSphere 서버 가상화 솔루션

Network Virtualization : NSX 네트워크 가상화 솔루션

Storage Virtualization : vSAN 스토리지 지역 네트워크

Horizon: desktop 가상화 솔루션



**가상화의 유형 (Types of Virtualization)**



**가상화(Virtualization)**는 **서버, 데스크톱, 네트워크, 스토리지 장치와 같은 물리적 단위를 소프트웨어 기반으로 구현하는 기술**이다.

이 기술은 **IT 비용을 절감하면서도 효율성과 민첩성을 향상시키는 가장 효과적인 방법**이다.

**1. 주요 가상화 유형**

​	1.	**서버 가상화(Server Virtualization)**

​	•	물리적 서버 하나를 여러 개의 **가상 서버(VM)로 분할하여 실행**

​	•	서버 리소스를 효율적으로 활용하고 운영 비용 절감

​	2.	**네트워크 가상화(Network Virtualization)**

​	•	물리적 네트워크 장비(라우터, 스위치 등)를 가상 환경에서 **소프트웨어적으로 구현**

​	•	SDN(소프트웨어 정의 네트워크)과 같은 기술로 유연한 네트워크 운영 가능

​	3.	**스토리지 가상화(Storage Virtualization)**

​	•	여러 개의 스토리지 장치를 하나의 논리적 단위로 통합

​	•	데이터 관리 및 확장성을 향상시켜 스토리지 활용도를 최적화

​	4.	**데스크톱 가상화(Desktop Virtualization)**

​	•	개별 PC 환경을 **가상 머신으로 중앙 서버에서 실행**

​	•	원격 근무 지원 및 보안성 강화

**2. 핵심 개념 요약**



✅ **가상화는 IT 인프라를 최적화하고, 비용 절감과 유연성을 제공하는 핵심 기술**

✅ **서버, 네트워크, 스토리지, 데스크톱 등 다양한 영역에서 가상화를 적용 가능**

✅ **기업 환경에서 더 높은 확장성, 보안성, 유지보수 편의성을 제공**





------

## About the Software-Defined Data Center

![image-20250227121047509](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227121047509.png)

**소프트웨어 정의 데이터 센터(SDDC) 개요**



소프트웨어 정의 데이터 센터(SDDC, Software-Defined Data Center)는 **모든 인프라가 가상화되고, 데이터 센터 제어가 소프트웨어를 통해 자동화되는 환경**이다.

vSphere는 **SDDC의 핵심 기반 기술**로 활용된다.

**1. SDDC의 주요 구성 요소**



**① 물리 계층(Physical Layer)**

​	•	**컴퓨팅(Compute)**: 물리적인 서버 인프라

​	•	**스토리지(Storage)**: 가상화된 저장 장치

​	•	**네트워크(Network)**: 가상 네트워크를 지원하는 물리적 네트워크



**② 가상 인프라 계층(Virtual Infrastructure Layer)**

​	•	**VMware vSphere**: 서버 가상화 핵심 플랫폼

​	•	**VMware vSAN**: 소프트웨어 정의 스토리지(Software-Defined Storage, SDS)

​	•	**VMware NSX**: 네트워크 가상화 및 보안



**③ 클라우드 관리 계층(Cloud Management Layer)**

​	•	**Service Catalog**: IT 서비스 카탈로그 관리

​	•	**Self-Service Portal**: 사용자 셀프서비스 환경 제공

​	•	**Orchestration**: IT 자동화 및 워크플로우 관리



**④ 서비스 관리 및 자동화(Service Management and Automation)**

​	•	**VMware Aria Operations**: 성능 모니터링 및 운영 최적화

​	•	**VMware Aria Automation**: 자동화된 인프라 및 애플리케이션 배포

​	•	**VMware Aria Automation Orchestrator**: 워크플로우 자동화



**⑤ 비즈니스 연속성(Business Continuity)**

​	•	**Site Recovery Manager**: 재해 복구 솔루션

​	•	**vSphere Replication**: 가상 머신 데이터 복제



**⑥ 보안(Security)**

​	•	**NSX Microsegmentation**: 네트워크 마이크로 세분화 보안

​	•	**Carbon Black**: 엔드포인트 보안 및 위협 탐지

**2. 핵심 개념 요약**



✅ **SDDC는 서버, 스토리지, 네트워크를 소프트웨어 기반으로 가상화하여 유연성과 자동화를 극대화**

✅ **vSphere, vSAN, NSX 등의 VMware 솔루션이 핵심 기술로 사용됨**

✅ **자동화된 운영, 보안 강화, 비즈니스 연속성 확보가 가능하여 차세대 데이터 센터 구축에 필수적**

------

## **vSphere+를 통한 클라우드 서비스 접근**

![image-20250227121835034](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227121835034.png)





**vSphere+**는 온프레미스(데이터센터) 환경을 보완하고 향상시키기 위해 **클라우드 서비스를 활용할 수 있는 플랫폼**이다.

**1. 주요 기능 및 서비스**



**① 관리 서비스(Admin Services)**

​	•	**인벤토리 관리(Inventory Management)**: 가상 머신 및 리소스 관리

​	•	**이벤트 및 경고 관리(Events and Alerts Management)**: 시스템 이벤트 및 알림 모니터링

​	•	**VM 프로비저닝(VM Provisioning)**: 가상 머신 배포 및 관리

​	•	**라이프사이클 관리(Lifecycle Management)**: 하드웨어 및 소프트웨어 업데이트 자동화

​	•	**구성 관리(Configuration Management)**: 표준화된 환경 유지



**② 개발자 서비스(Developer Services)**

​	•	**Tanzu Kubernetes Grid**: Kubernetes 기반 컨테이너 환경 구축

​	•	**Tanzu 통합 서비스(Tanzu Integrated Services)**: Tanzu와의 원활한 연동



**③ 추가 서비스(Add-On Services)**

​	•	**재해 복구(Disaster Recovery)**: 클라우드를 활용한 고가용성 및 데이터 복구 지원

**2. 그림 설명**

​	•	**Cloud**

​	•	관리 서비스(Admin Services), 개발자 서비스(Developer Services), 추가 서비스(Add-On Services)가 **Cloud Console을 통해 제공**

​	•	클라우드를 활용하여 **온프레미스 환경을 확장 및 보완**

​	•	**On-Premises**

​	•	**vCenter 및 ESXi 호스트**가 가상 머신을 운영

​	•	**클라우드 기반 관리 기능을 통해 온프레미스 환경을 최적화**



✅ **vSphere+를 활용하면 온프레미스 환경을 클라우드와 연계하여 관리 효율성과 확장성을 극대화할 수 있다.**

------

## vSphere User Interfaces

![image-20250227122103322](/assets/cisco_post_img/2025-02-27- vSphere and Virtualization Overview//image-20250227122103322.png)



------

### 기타

**workload : 가상에서 흐르는 트래픽**

**컨테이너 엔진 - 도커**