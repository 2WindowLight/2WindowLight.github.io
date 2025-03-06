---
title: <h0>Module 6-2 Lesson 2 Fibre Channel Storage</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-06-cisco]
tags: [Network, Cloud]















---

# Lesson 2 Fibre Channel Storage

------

## **Fibre Channel 개요**

![image-20250306112515147](/assets/cisco_post_img/2025-03-06-Module 6-2 Lesson 2 Fibre Channel Storage//image-20250306112515147.png)

Fibre Channel(FC)은 고성능 SAN(Storage Area Network) 환경에서 스토리지 장치를 액세스하기 위해 사용되는 프로토콜이다. 이는 **VM에서 SAN 스토리지까지 SCSI 트래픽을 전송**하는 역할을 하며, **고속 데이터 전송**을 위해 설계된 기술이다.

**1. Fibre Channel SAN 개요**

​	•	Fibre Channel SAN은 **호스트와 고성능 스토리지 장치를 연결하는 특수한 고속 네트워크**이다.

​	•	**Fibre Channel 프로토콜**을 사용하여 **VM에서 SAN 스토리지까지 SCSI 트래픽을 전송**한다.

​	•	**32 Gbps** 속도를 지원하며, Ethernet을 활용한 **Fibre Channel over Ethernet (FCoE)** 도 가능하다.

------

**2. Fibre Channel SAN 구성 요소**

Fibre Channel을 사용하려면 다음과 같은 구성 요소가 필요하다.

1. **호스트(ESXi 서버)**
   * Fibre Channel HBA(Host Bus Adapter)를 장착해야 한다.
   * HBA가 SAN 네트워크와 통신하여 LUN을 마운트한다.

2. **SAN 패브릭(Fabric)**
   * Fibre Channel **스위치**를 포함하여 **스토리지 트래픽을 라우팅**하는 역할을 함.
   * **Fibre Channel Direct Connect** 스토리지를 사용하지 않는 경우, FC 스위치가 필요하다.

3. **스토리지 어레이**
   * SAN 스토리지에 구성된 LUN(Logical Unit Number)을 호스트에서 액세스할 수 있도록 제공한다.
   * 스토리지 풀을 기반으로 **VMFS 데이터스토어** 또는 **vSphere Virtual Volumes 데이터스토어**를 생성할 수 있음.

------

**3. Fibre Channel 스토리지 연결 방식**

1. **Fibre Channel (FC)**
   * 독립적인 **Fibre Channel 네트워크**를 통해 스토리지와 연결됨.
   * 높은 성능과 낮은 지연 시간(Low Latency) 제공.

2. **Fibre Channel over Ethernet (FCoE)**

   * 기존 **Ethernet 네트워크를 활용하여 Fibre Channel 트래픽을 전달**하는 방식.

   * FC 전용 네트워크를 구성하지 않아도 되어 **비용 절감 가능**.

------

**4. Fibre Channel 스토리지 활용**

* ESXi 호스트는 **Fibre Channel 네트워크를 통해 SAN 스토리지의 LUN을 마운트**할 수 있음.
* **VMFS 포맷의 데이터스토어**를 생성하여 가상 머신의 스토리지로 활용.
* **vSphere Virtual Volumes (vVols) 지원** 스토리지의 경우, 스토리지 컨테이너에 vVol 데이터스토어를 생성할 수도 있음.

------

**5. Fibre Channel의 장점**

✔ **고속 데이터 전송** – 최대 32Gbps의 속도 지원.

✔ **낮은 지연 시간** – 빠른 스토리지 응답 속도 제공.

✔ **안정적인 SAN 네트워크** – 독립적인 네트워크 구성으로 트래픽 충돌 없음.

✔ **가상 환경 최적화** – vSphere VMFS 및 vVols와 완벽한 호환성 제공.

**그림 설명**

​	•	**VM**들은 **Fibre Channel HBA**를 갖춘 **ESXi 호스트**를 통해 **SAN 패브릭**에 연결됨.

​	•	**SAN 패브릭(Fibre Channel Switch 포함)**은 **스토리지 어레이의 LUN**을 호스트에 제공.

​	•	**스토리지 어레이의 LUN**은 ESXi 호스트에서 데이터스토어로 마운트되어 가상 머신에 스토리지를 제공.

------

------

**Fibre Channel SAN 구성 요소**

![image-20250306112627049](/assets/cisco_post_img/2025-03-06-Module 6-2 Lesson 2 Fibre Channel Storage//image-20250306112627049.png)

Fibre Channel SAN(스토리지 영역 네트워크, SAN)은 **서버와 스토리지를 고속으로 연결하는 네트워크**이다. 이 환경에서는 **Fibre Channel 스위치와 HBA(Host Bus Adapter)**를 활용하여 **스토리지에 대한 빠르고 안정적인 액세스를 제공**한다.

------

**1. Fibre Channel SAN 구성 요소**

Fibre Channel SAN은 다음과 같은 주요 요소로 구성된다.

1. **Fibre Channel SAN 스위치**
   * SAN 내의 다양한 요소를 연결하는 역할을 한다.
   * **서버에서 스토리지까지 경로를 구성하며, 다중 경로(Redundancy) 설정 가능**.
   * **FC 스위치 간 연결을 통해 SAN 패브릭을 형성**할 수 있다.

2. **SAN 패브릭(Fabric)**
   * SAN의 네트워크 부분을 의미하며, 여러 개의 **SAN 스위치가 연결되어 패브릭을 구성**한다.
   * **Fibre Channel 프로토콜을 사용하여 전체 네트워크에서 통신**.
   * 고가용성을 위해 두 개 이상의 **중복 패브릭(Fabric)을 구성**하는 것이 일반적이다.

3. **HBA(Host Bus Adapter)**
   * 서버가 SAN 패브릭에 연결되기 위한 장치.
   * 서버의 **HBA 포트가 SAN 스위치에 연결되어 스토리지와의 통신을 담당**한다.

4. **스토리지 프로세서(Storage Processor, SP)**
   * 스토리지 시스템이 **SAN 패브릭과 연결되기 위한 장치**.
   * 스토리지가 **SP를 통해 SAN 패브릭을 사용하여 서버와 데이터 전송**을 수행함.

------

**2. Fibre Channel SAN의 동작 방식**

1. **호스트(ESXi 서버) → HBA**
   * 서버에 장착된 **HBA가 SAN 스위치와 연결**된다.

2. **HBA → SAN 패브릭(FC Switch)**
   * Fibre Channel 스위치가 **서버와 스토리지 간 데이터 전송을 관리**한다.
   * 다중 경로 설정이 가능하여, **스위치 장애 시에도 데이터 전송을 유지할 수 있음**.

3. **SAN 패브릭 → 스토리지 프로세서(SP)**
   * **FC 스위치에서 스토리지 어레이의 SP로 데이터가 전달**된다.

4. **스토리지 프로세서(SP) → 디스크 어레이(Disk Array)**
   * SP는 **SAN 스토리지 내의 LUN(Logical Unit Number)을 호스트에 제공**하여, 서버가 해당 스토리지에 액세스할 수 있도록 함.

------

**3. Fibre Channel SAN의 장점**



✔ **고속 데이터 전송** – 높은 대역폭(최대 32 Gbps) 제공

✔ **안정적인 다중 경로 지원** – 스위치 장애 발생 시에도 **중복 경로**를 통한 지속적인 연결 가능

✔ **확장성 및 유연성** – SAN 패브릭을 확장하여 대규모 스토리지 환경 구축 가능

✔ **vSphere와 완벽한 통합** – **VMFS 데이터스토어 또는 vSphere Virtual Volumes (vVols) 사용 가능**

------

**4. 그림 설명**

​	•	**서버(ESXi 호스트)**는 **HBA(Host Bus Adapter)**를 통해 **SAN 패브릭(SAN 스위치)에 연결**됨.

​	•	**SAN 스위치(FC Switch)**는 **HBA와 스토리지 프로세서(SP)를 연결하는 역할**을 함.

​	•	**스토리지 프로세서(SP)**는 **디스크 어레이를 관리하며, LUN을 할당하여 서버가 데이터에 접근할 수 있도록 함**.

​	•	**FC Switch 간 연결을 통해 다중 경로 설정이 가능하며, 장애 발생 시에도 데이터 경로를 유지할 수 있음**.



------

------

## **Fibre Channel 주소 지정 및 접근 제어 (Fibre Channel Addressing and Access Control)**

![image-20250306112904703](/assets/cisco_post_img/2025-03-06-Module 6-2 Lesson 2 Fibre Channel Storage//image-20250306112904703.png)

Fibre Channel SAN에서는 각 **장치(호스트, 스토리지, 스위치)**가 **하나 이상의 포트(Port)**를 통해 SAN에 연결된다. **각 포트는 고유한 WWPN (Worldwide Port Name)**을 가지며, 이를 통해 장치를 식별하고 접근 제어를 수행할 수 있다.

------

**1. Fibre Channel 포트 주소 지정 (WWPN)**

​	•	**WWPN (Worldwide Port Name)**:

​	•	각 **SAN 장치(호스트, 스토리지, 스위치)의 포트마다 부여되는 고유한 글로벌 식별자**.

​	•	Fibre Channel 스위치는 **WWPN을 자동으로 검색하고 해당 포트에 주소를 할당**한다.

​	•	특정 애플리케이션이 WWPN을 사용하여 SAN 포트에 접근할 수 있음.

------

**2. SAN 보안 및 접근 제어 (Zoning & LUN Masking)**



SAN 환경에서 데이터 보호 및 접근 제어를 위해 **Zoning(존 설정)과 LUN Masking(론 마스킹)**을 활용한다.

1. Zoning (존 설정)
   * **SAN에서 특정 장치들끼리만 통신 가능하도록 설정**하는 방식.
   * Zoning을 설정하면 **불필요한 장치 간의 데이터 전송을 차단하여 성능과 보안을 향상**할 수 있음.
   * 일반적으로 **소프트 존(WWPN 기반)과 하드 존(포트 기반)** 방식이 있음.
   * 예시:
     * **테스트 환경과 프로덕션 환경을 분리하여 운영 가능**.
     * 특정 **부서별로 SAN 리소스를 할당하여 접근 제어 가능**.



2. **LUN Masking (LUN 마스킹)**
   * **특정 LUN(Logical Unit Number, 논리적 디스크)에 대한 접근을 제한**하는 기능.
   * 같은 SAN에 연결된 모든 호스트가 **모든 LUN을 볼 필요는 없으므로, 필요하지 않은 호스트에는 LUN을 숨김**.
   * 스토리지 배열에서 설정되며, **특정 호스트만 특정 LUN에 접근할 수 있도록 제한**.
   * 예시:
     * **개별 호스트가 불필요한 스토리지 볼륨을 감지하는 것을 방지**.
     * **운영 서버와 개발 서버가 동일한 SAN을 사용할 경우, 운영 서버만 중요한 데이터에 접근하도록 제한**.

------

**3. Fibre Channel SAN 구성 요소 및 동작 방식**

​	1.	**각 호스트(ESXi 서버)는 Fibre Channel HBA(Host Bus Adapter)를 사용하여 FC 스위치와 연결**.

​	2.	**FC 스위치는 WWPN을 검색하고, SAN 내에서 각 장치가 고유한 주소를 가질 수 있도록 설정**.

​	3.	**스토리지 프로세서(SP)를 통해 LUN이 호스트에 할당됨**.

​	4.	**Zoning과 LUN Masking을 사용하여, 특정 호스트가 특정 LUN에만 접근하도록 제한**.

**4. 그림 설명**

​	•	**호스트(ESXi 서버)는 Fibre Channel HBA를 통해 FC 스위치와 연결**.

​	•	**FC 스위치는 WWPN을 기반으로 각 장치를 식별하고 SAN 패브릭 내에서 통신을 설정**.

​	•	**스토리지 프로세서(SP)는 디스크 배열과 연결되어 LUN을 관리**.

​	•	**Zoning과 LUN Masking을 적용하여, 특정 LUN이 지정된 호스트에서만 접근 가능하도록 설정**.

------

------

## **Fibre Channel 다중 경로(Multipathing) 개요**

![image-20250306113240151](/assets/cisco_post_img/2025-03-06-Module 6-2 Lesson 2 Fibre Channel Storage//image-20250306113240151.png)

Fibre Channel 환경에서 **Multipathing(다중 경로)**은 **호스트에서 LUN(논리적 유닛)까지 여러 개의 경로를 구성하는 기술**이다. 이를 통해 **하드웨어 장애 발생 시에도 지속적인 스토리지 접근을 유지하고, 부하 분산(Load Balancing)을 수행할 수 있다**.

1. ------

   Fibre Channel 경로(Path) 개념

   * **Fibre Channel 환경에서 “경로(Path)“란?**
   * 호스트의 **HBA(Host Bus Adapter) 포트**에서 출발
   * **SAN 패브릭(SAN Switch)을 통과**
   * *스토리지 어레이(Storage Array)의 특정 포트(Storage Processor, SP)로 연결**되는 경로를 의미함.**
   * ESXi 기본 동작
     * **기본적으로 ESXi는 LUN에 대한 단일 경로만 활성화하여 사용.**
     * 사용 중인 경로에 장애가 발생하면, 자동으로 다른 사용 가능한 경로로 전환(Path Failover)**.**
     * **장애 감지 및 전환 과정은 **스토리지 HBA, 케이블, 스위치 포트, 또는 스토리지 프로세서(SP) 장애 시 수행됨**.

------

**2. Multipathing 주요 기능**



**① 지속적인 스토리지 접근 (Path Redundancy)**

​	•	**하드웨어 장애 발생 시에도 SAN LUN에 대한 접근을 유지할 수 있음**.

​	•	예를 들어, **SAN Switch A가 장애가 나도, SAN Switch B를 통해 정상적으로 접근 가능**.



**② 부하 분산 (Load Balancing)**

​	•	**HBA 및 스토리지 프로세서 간에 I/O 요청을 분산하여 성능을 향상**.

​	•	경로를 적절히 사용하면 병목 현상을 줄이고, 전체적인 I/O 성능을 최적화할 수 있음.

------

**3. Active-Active vs. Active-Passive Disk Array**



**스토리지 어레이의 유형에 따라 Multipathing 방식이 달라짐**.



**① Active-Active Disk Array (활성-활성)**

​	•	**모든 스토리지 프로세서(SP)가 동시에 I/O 요청을 처리 가능**.

​	•	**LUN을 동시에 여러 경로에서 접근할 수 있어 성능 저하 없이 부하 분산이 가능**.

​	•	경로 중 하나가 실패해도 즉시 다른 활성 경로를 사용하여 운영 지속.



**② Active-Passive Disk Array (활성-수동)**

​	•	**한 개의 스토리지 프로세서(SP)만 활성 상태로 LUN을 서비스**.

​	•	**다른 스토리지 프로세서는 대기 상태이며, 장애 발생 시에만 활성화됨**.

​	•	I/O 요청은 **현재 활성 상태의 SP로만 전송되며**, 만약 활성 SP가 장애를 일으키면 **대기 상태의 SP가 활성화됨(Failover)**.

**4. 그림 설명**

​	•	**각 호스트(ESXi 서버)는 두 개의 HBA(Host Bus Adapter)를 통해 두 개의 SAN 스위치(A, B)와 연결됨**.

​	•	**SAN 스위치는 각각 두 개의 스토리지 프로세서(SP1, SP2)와 연결**.

​	•	**각 스토리지 프로세서는 디스크 어레이 내의 LUN에 연결되어 있음**.

​	•	**Active-Active 환경이면 모든 SP를 통해 LUN에 접근 가능하며, Active-Passive 환경이면 특정 SP만 활성화됨**.

​	•	**SAN Switch A 또는 B의 장애, 혹은 HBA 장애가 발생해도 대체 경로를 통해 LUN에 접근 가능**.



------

------

### 기타

WWN: 구분자

- nWWN: node WWN
- pWWn: port WWN

------

fc  스위치 san 스위치

host 파이버 채널	

------

호스트를 이니시에이터, 

하드디스크를 타겟

스토리지 네트워크 : Febric

