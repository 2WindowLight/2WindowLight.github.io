---
title: <h0>Module 6 Configuring vSphere Storage</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-06-cisco]
tags: [Network, Cloud]














---

# Module 6: Configuring vSphere Storage

------

## Lesson 1: Storage Concepts

storage protocol

* Fibre channel (FC)
* iSCSI (TCP/IP)
* NFS (Network file system): 공유 폴더, tcp/ip
* FCoE (FC over Ethernet): TCP/IP

------

## About Datastores( **Datastore 개요 정리**)

![image-20250306100440561](/assets/cisco_post_img/2025-03-06-Module 6 Configuring vSphere Storage//image-20250306100440561.png)

**Datastore(데이터스토어)**는 가상 환경에서 **VM(가상 머신), VM 템플릿, ISO 이미지** 등의 데이터를 저장하는 **논리적 스토리지 단위**임.

**Datastore의 특징**

* 하나 이상의 **물리적 스토리지 장치**를 사용하여 논리적인 저장 공간을 제공함.
* **파일 시스템과 유사한 논리적 컨테이너** 역할을 하며, 기저 스토리지의 세부 사항을 숨기고 일관된 스토리지 모델을 제공함.
* 각 VM은 **자신만의 디렉토리 또는 객체 그룹**으로 저장됨.

**vSphere에서 지원하는 Datastore 유형**

1. **VMFS (Virtual Machine File System)**

   * VMware의 **클러스터 파일 시스템**으로, 여러 호스트가 동시에 접근 가능.

   * 고가용성 및 락킹 메커니즘 제공.

2. **NFS (Network File System)**

   * NAS(Network Attached Storage)를 통해 **파일 기반 스토리지**로 사용.

   * VMFS보다 설정이 간단하고 공유가 용이함.

3. **vSAN (VMware vSAN)**

   * 로컬 디스크를 **소프트웨어 정의 스토리지(SDS)**로 활용.

   * 고가용성을 위해 **클러스터 전체에서 데이터 복제**.

4. **vSphere Virtual Volumes (vVols)**

   * **스토리지 어레이의 네이티브 기능**을 활용하여 VM별 스토리지 정책을 적용.

   * 개별 VM의 성능 및 가용성을 보다 세밀하게 관리 가능.

**Datastore 관리**

* vSphere에서 사용 가능한 모든 데이터스토어를 확인하고, **속성을 분석하여 적절한 관리가 가능**.
* VM의 파일 저장 방식은 **디렉토리 기반(파일 시스템) 또는 객체 기반(스토리지 정책)**으로 운영됨.

**결론**

Datastore는 가상 환경에서 **스토리지를 추상화하여 VM 데이터를 저장하는 핵심 요소**이며, 필요에 따라 다양한 유형의 데이터스토어를 선택하여 활용할 수 있음.

------

------

## **Datastore 액세스 방식 정리**



vSphere 데이터스토어는 데이터를 **블록 기반(Block-backed) 또는 파일 기반(File-backed)** 방식으로 저장하고 액세스함.

**1. 블록 기반 스토리지 (Block-backed storage)**

* 데이터를 **연속된 바이트(Block) 단위**로 저장함.
* 일반적으로 **로컬 스토리지(Local Storage)** 또는 **스토리지 네트워크(SAN, Storage Area Network)**에서 사용됨.
* **액세스 방식:**
  *  *iSCSI** (IP 기반 SAN 연결)
  * **Fibre Channel (FC)** (고속 SAN 연결)

* **사용되는 데이터스토어 유형:**
  * *VMFS (Virtual Machine File System)
  * vSAN (Virtual SAN)
  * vSphere Virtual Volumes (vVols)**


**2. 파일 기반 스토리지 (File-backed storage)**

* 데이터를 **파일 및 폴더 구조**로 저장함.
* **네트워크 스토리지(NAS, Network Attached Storage)**에서 사용됨.

* **액세스 방식:**
  * **NFS (Network File System)** (네트워크 공유 방식)
  * **사용되는 데이터스토어 유형:**
  * *NFS (Network File System) 데이터스토어
  * vSphere Virtual Volumes (vVols)**

**결론**

* **Block-backed storage**는 **VMFS, vSAN, vVols**에서 사용되며, **SAN 및 로컬 스토리지**를 통해 액세스함.
* **File-backed storage**는 **NFS, vVols**에서 사용되며, **NAS 환경**에서 파일 및 폴더 단위로 데이터를 관리함.
* **vVols는 블록과 파일 기반 스토리지를 모두 지원**하여 유연한 스토리지 활용이 가능함.

------

------

## **Datastore 유형 및 내용 정리**



데이터스토어는 **파일 기반(File-based)** 또는 **오브젝트 기반(Object-based)** 방식으로 데이터를 저장함.

**1. 파일 기반 데이터스토어 (File-based Datastore)**

* **VM은 하나의 디렉터리로 구성됨.**
* 각 VM은 고유한 디렉터리를 가짐.
* 디렉터리 내부에 VM 구성 파일, 가상 디스크 파일, 스왑 파일 등이 저장됨.

* **사용되는 데이터스토어 유형:**
  * **VMFS (Virtual Machine File System)**
  * *NFS (Network File System)**

**그림 설명 (위쪽)**

​	•	파일 기반 데이터스토어는 **각 VM이 하나의 폴더(디렉터리)**로 존재.

​	•	폴더 안에 VM의 주요 파일(설정 파일, 가상 디스크 파일 등)이 포함됨.

**2. 오브젝트 기반 데이터스토어 (Object-based Datastore)**

* **VM은 개별 오브젝트(객체) 단위로 저장됨.**

* 각 VM은 VM 설정 오브젝트, 가상 디스크 오브젝트, 스왑 공간 오브젝트 등으로 구성됨.

* 오브젝트는 **데이터 컨테이너**이며, 각 오브젝트는 **메타데이터 및 고유 ID**를 포함함.

* **사용되는 데이터스토어 유형:**

  * **vSAN (Virtual SAN)**

  * **vSphere Virtual Volumes (vVols)**



**그림 설명 (아래쪽)**

* 오브젝트 기반 데이터스토어에서는 **각 VM이 여러 개의 개별 오브젝트로 구성됨.**
* VM 관련 파일이 각각 독립적인 오브젝트로 저장되며, vSAN 및 vVols 환경에서 관리됨.

**🔎 파일 기반 vs 오브젝트 기반 차이점**

| **구분**             | **파일 기반 데이터스토어**                   | **오브젝트 기반 데이터스토어**       |
| -------------------- | -------------------------------------------- | ------------------------------------ |
| **구성 방식**        | VM은 하나의 디렉터리 안에 모든 파일이 저장됨 | VM은 여러 개의 오브젝트로 구성됨     |
| **데이터 저장 방식** | 파일 시스템 기반 (VMFS, NFS)                 | 오브젝트 스토리지 기반 (vSAN, vVols) |
| **데이터 관리**      | 디렉터리 내 파일 단위 관리                   | 오브젝트 단위 관리                   |
| **유형**             | VMFS, NFS                                    | vSAN, vVols                          |

**결론**

* **파일 기반 스토리지**는 **VMFS, NFS** 환경에서 사용되며, **각 VM이 하나의 디렉터리로 관리**됨.
* **오브젝트 기반 스토리지**는 **vSAN, vVols** 환경에서 사용되며, **각 VM이 여러 개의 독립적인 오브젝트로 저장**됨.
* **vSAN 및 vVols는 스토리지를 더욱 유연하고 확장 가능하게 관리할 수 있는 방식**임.

------

------

## Storage Overview(**vSphere 스토리지 개요**)

![image-20250306101252366](/assets/cisco_post_img/2025-03-06-Module 6 Configuring vSphere Storage//image-20250306101252366.png)

vSphere 환경에서 **ESXi 호스트는 다양한 스토리지 기술을 지원**하며, 데이터스토어는 **VMFS** 또는 **NFS** 파일 시스템으로 포맷될 수 있음.

### **1. 스토리지 유형 및 데이터스토어**



스토리지 유형에 따라 ESXi에서 사용할 수 있는 데이터스토어 포맷이 다름.

| **스토리지 유형**                      | **데이터스토어 포맷** |
| -------------------------------------- | --------------------- |
| **Direct-attached storage (DAS)**      | VMFS                  |
| **Fibre Channel (FC)**                 | VMFS                  |
| **FCoE (Fibre Channel over Ethernet)** | VMFS                  |
| **iSCSI**                              | VMFS                  |
| **NAS (Network-Attached Storage)**     | NFS                   |
| **vSAN**                               | vSAN 데이터스토어     |
| **vSphere Virtual Volumes (vVols)**    | 스토리지 컨테이너     |

**2. 주요 스토리지 기술 설명**

1. **DAS (Direct-attached storage)**
   * 물리적으로 **ESXi 호스트에 직접 연결된 스토리지**.
   * 네트워크를 거치지 않고 **로컬 디스크 또는 외부 스토리지 어레이**를 연결.
   * VMFS 파일 시스템을 사용.

2. **Fibre Channel (FC)**

   * **고속 SAN(Storage Area Network) 프로토콜**.

   * **SCSI 명령을 Fibre Channel 프레임에 캡슐화**하여 전송.

   * 스위치를 통해 여러 호스트와 스토리지를 연결하는 **SAN 환경**에서 사용.

3. **FCoE (Fibre Channel over Ethernet)**

   * **Fibre Channel 트래픽을 이더넷 프레임으로 캡슐화**하여 전송.

   * 기존 이더넷 네트워크에서 **Fibre Channel 스토리지를 사용할 수 있도록 지원**.
   * **네트워크 트래픽과 스토리지 트래픽을 통합 가능**.

4. **iSCSI (Internet Small Computer System Interface)**
   * **TCP/IP 네트워크를 통해 SCSI 명령을 전송**하여 원격 블록 스토리지를 제공.
   * 별도의 **전용 SAN 네트워크 없이도** 저비용으로 SAN 환경을 구축 가능.
   * **iSCSI HBA 또는 소프트웨어 이니시에이터**를 사용하여 연결.

5. **NAS (Network-attached storage)**

   * **NFS(Network File System) 프로토콜을 사용**하여 파일 시스템 단위로 공유 스토리지 제공.

   * SCSI 명령을 지원하지 않으며, 파일 기반 접근 방식 사용.

6. **vSAN (Virtual SAN)**
   * ESXi 호스트의 **로컬 디스크를 클러스터링하여 공유 스토리지를 제공**하는 VMware 기술.
   * **하이퍼컨버지드 인프라(HCI)** 구축에 사용됨.
   * 데이터는 **분산 저장되며, 고가용성과 성능 최적화를 지원**.

7. **vSphere Virtual Volumes (vVols)**

   * 기존 VMFS/NFS와 다르게 **VM 단위로 스토리지를 관리**할 수 있도록 설계된 솔루션.

   * 스토리지 벤더의 관리 기능을 활용하여 **정책 기반 스토리지 관리(Storage Policy-Based Management, SPBM) 제공**.

**3. 그림 설명**

**🔹 상단 부분 (가상 머신 & 호스트)**

* 여러 개의 가상 머신(VM)이 **ESXi 호스트**에 배포됨.
* 각 ESXi 호스트는 **스토리지 기술에 따라 다른 유형의 데이터스토어**를 사용함.



**🔹 중간 부분 (데이터스토어 포맷)**

* **VMFS**: 블록 스토리지 기반 데이터스토어.
* **NFS**: 파일 시스템 기반 스토리지.
* **vSAN**: 분산 클러스터 스토리지.
* **vSphere Virtual Volume (vVols)**: 정책 기반 가상 볼륨 스토리지.



**🔹 하단 부분 (스토리지 연결 방식)**

* 스토리지는 **다양한 연결 방식(Direct Attached, FC, iSCSI, NAS 등)**을 통해 ESXi 호스트와 연결됨.
* **iSCSI, FCoE, NAS는 이더넷을 사용**하여 연결되며, **FC는 전용 Fibre Channel 네트워크를 사용**.

**4. 결론**

* vSphere는 다양한 스토리지 기술을 지원하며, **워크로드 유형과 인프라 요구사항에 따라 적절한 데이터스토어를 선택**해야 함.
* **고성능 및 확장성**이 필요한 경우 **FC, vSAN, vVols**와 같은 스토리지가 적합.
* **비용 절감 및 유연성**이 필요할 경우 **iSCSI, NFS** 등의 스토리지를 활용 가능.

------

------

## **스토리지 장치 명명 규칙 (Storage Device Naming Conventions)**



ESXi 호스트에서 **SCSI 스토리지 장치는 여러 식별자를 사용하여 관리됨**. 각각의 식별자는 특정한 목적을 가짐.

1. ------

   *주요 스토리지 식별자**

| **식별자**                    | **설명**                                                     |
| ----------------------------- | ------------------------------------------------------------ |
| **Runtime Name**              | ESXi 호스트에서 생성하는 경로 기반의 이름으로, **vmhbaN:C:T:L** 형식을 따름. 부팅 후 동적으로 생성되며 **재부팅 시 변경될 수 있음**. |
| **Target**                    | iSCSI의 경우 **IQN(이니시에이터/타겟 식별자)** 또는 **EUI(확장 고유 식별자)** 사용. |
| **LUN (Logical Unit Number)** | **SCSI 프로토콜 및 SAN 프로토콜**(iSCSI, Fibre Channel 등)에서 개별 스토리지 장치를 식별하는 번호. |

2. ------

   **Runtime Name 설명**

   * 형식: **vmhbaN:C:T:L**
   * vmhbaN**: 호스트 버스 어댑터(HBA) 번호.**
   * C**: 컨트롤러 번호.**
   * T**: 타겟 번호 (스토리지 장치 주소).**
   * L: LUN 번호 (논리 유닛 식별자).



​	⚠ **주의:** Runtime Name은 **재부팅 시 변경될 가능성이 높기 때문에** 영구적인 식별자로 사용하기에는 적절하지 않음.

3. ------

   **Target (타겟) 식별자**

   * 스토리지 장치를 고유하게 식별하는 이름.
   * **iSCSI의 경우 IQN(이니시에이터/타겟 이름) 사용**:
   * IQN 형식: **iqn.yyyy-mm.naming-authority:unique name**
   * 예시: iqn.2005-10.org.freenas.ctl:iqn-target:172.20.10.15:3260
   * Fibre Channel의 경우 **WWPN(World Wide Port Name) 사용**.

4. ------

   **LUN (Logical Unit Number)**

   * LUN은 **하나의 논리적 스토리지 장치를 식별하는 번호**.
   * 동일한 타겟(스토리지) 내에서 여러 개의 LUN이 존재할 수 있음.

예제:

| **Runtime Name** | **Target**                                    | **LUN** | **상태**     |
| ---------------- | --------------------------------------------- | ------- | ------------ |
| vmhba65:C0:T0:L0 | iqn.2005-10.org.freenas.ctl:172.20.10.15:3260 | 0       | Active (I/O) |
| vmhba65:C0:T0:L2 | iqn.2005-10.org.freenas.ctl:172.20.10.15:3260 | 2       | Active (I/O) |
| vmhba65:C0:T0:L3 | iqn.2005-10.org.freenas.ctl:172.20.10.15:3260 | 3       | Active (I/O) |
| vmhba65:C0:T0:L4 | iqn.2005-10.org.freenas.ctl:172.20.10.15:3260 | 4       | Active (I/O) |

5. ------

   **그림 설명**

   * **상단**: ESXi의 **Storage Adapters 패널**을 통해 iSCSI 어댑터(vmhba65)가 여러 개의 타겟과 LUN을 감지한 것을 확인할 수 있음.

   * **하단**: Paths 탭에서 특정 iSCSI 타겟이 여러 개의 LUN을 제공하고 있으며, 모두 **Active (I/O)** 상태로 연결되어 있음.

6. ------

   **결론**

   * **Runtime Name은 영구적인 식별자가 아니므로 UUID 또는 LUN ID를 기반으로 스토리지를 관리하는 것이 권장됨.**
   * *iSCSI에서는 IQN을, Fibre Channel에서는 WWPN을 사용하여 스토리지 타겟을 식별함.
   * LUN을 통해 동일한 타겟(스토리지) 내에서 여러 개의 논리적 스토리지 장치를 구분할 수 있음.**



------

------

## **스토리지 프로토콜 개요(Storage Protocol Overview)**

ESXi 환경에서는 다양한 **스토리지 프로토콜**을 사용하여 데이터스토어를 구성할 수 있음. 각 프로토콜은 기능 및 지원 여부가 다름.

1. ------

   **스토리지 프로토콜별 지원 기능**

| **Datastore Type**          | **Storage Protocol**     | **Boot from SAN 지원** | **vSphere vMotion 지원** | **vSphere HA 지원** | **vSphere DRS 지원** |
| --------------------------- | ------------------------ | ---------------------- | ------------------------ | ------------------- | -------------------- |
| **VMFS**                    | Fibre Channel            | Yes                    | Yes                      | Yes                 | Yes                  |
|                             | FCoE                     | Yes                    | Yes                      | Yes                 | Yes                  |
|                             | iSCSI                    | Yes                    | Yes                      | Yes                 | Yes                  |
|                             | iSER/NVMe-oF (RDMA)      | No                     | Yes                      | Yes                 | Yes                  |
|                             | DAS (SAS, SATA, NVMe)    | N/A                    | Yes*                     | No                  | No                   |
| **NFS**                     | NFS                      | No                     | Yes                      | Yes                 | Yes                  |
| **vSphere Virtual Volumes** | FC/Ethernet (iSCSI, NFS) | No                     | Yes                      | Yes                 | Yes                  |
| **vSAN Datastore**          | vSAN                     | No                     | Yes                      | Yes                 | Yes                  |

🔹 **DAS(vMotion 지원 조건)**: DAS는 단독으로는 vMotion을 지원하지 않지만, **vSphere Storage vMotion과 함께 사용할 경우 vMotion 가능**

🔹 **Boot from SAN**: 일부 프로토콜(Fibre Channel, iSCSI 등)은 SAN에서 직접 부팅 가능하지만, NVMe-oF, NFS 등은 부팅을 지원하지 않음.

2. Direct-attached Storage (DAS) vs. Shared SAN Storage

   **DAS (Direct-attached Storage) 특징**

   * SAN이 아닌 **로컬 디스크에 ESXi를 설치**하는 방식.

   * **소규모 환경**에서 사용되며, **비용 절감**이 가능.

     * **주요 용도**:
       * CD/DVD ISO 이미지 저장.
       * 더 이상 사용되지 않는 VM(폐기된 VM) 저장.
       * VM 템플릿 저장.

     * **단점**:
       * **VM 간 공유 스토리지 기능이 없음** → HA(고가용성), DRS(리소스 분산), vMotion 등의 핵심 기능 사용 불가.



**SAN 기반 공유 스토리지 (Shared Storage)**

* **모든 ESXi 호스트에서 공유 가능**한 스토리지 풀을 제공.
* **vSphere 주요 기능 지원**:
  * **vMotion**: VM을 한 호스트에서 다른 호스트로 이동.
  * **HA (High Availability)**: 장애 발생 시 다른 호스트에서 자동 복구.
  * **DRS (Distributed Resource Scheduler)**: 리소스를 균등하게 분배.

* **SAN 스토리지의 장점**:
  * **VM 파일과 템플릿을 중앙에서 관리 가능**.
  * **ESXi 호스트 간 클러스터링 가능**.
  * **대용량 스토리지 (테라바이트급) 할당 가능**.

3. ESXi SAN 부팅 지원

   * **SAN에서 직접 부팅할 경우, 로컬 디스크 불필요** (디스크리스 환경, 예: 블레이드 서버).

   * **SAN 부팅 가능 프로토콜**:
     * **Fibre Channel**
     * **FCoE**
     * *iSCSI
     * **iSCSI 부팅을 위한 조건:**
       * **소프트웨어 iSCSI, 독립형 하드웨어 iSCSI 어댑터, 종속형 하드웨어 iSCSI 어댑터** 지원.
       * **네트워크 어댑터가 iBFT(iSCSI Boot Firmware Table) 형식을 지원해야 함**.

4. 그림 설명
   * **스토리지 프로토콜별 지원 기능을 비교하는 표**.
   * **DAS는 직접 vMotion을 지원하지 않지만, Storage vMotion을 활용하면 가능**.
   * **SAN 기반 스토리지는 ESXi의 주요 기능을 모두 지원**.
   * **SAN 부팅을 사용하면 ESXi 호스트의 로컬 스토리지가 필요 없음**.

**5. 결론**

​	•	**대부분의 엔터프라이즈 환경에서는 SAN 기반 공유 스토리지를 사용**.

​	•	**DAS는 비용 절감이 가능하지만, 주요 vSphere 기능을 사용할 수 없다는 단점이 있음**.

​	•	**SAN 부팅을 활용하면 디스크리스 서버에서도 ESXi를 운영할 수 있음**.

------

------

## **vSphere Virtual Machine File System (VMFS) 개요**



![image-20250306105456555](/assets/cisco_post_img/2025-03-06-Module 6 Configuring vSphere Storage//image-20250306105456555.png)

VMFS는 **클러스터 파일 시스템**으로, 여러 ESXi 호스트가 동시에 동일한 스토리지 디바이스에 **동시 접근**하여 읽고 쓸 수 있도록 지원함. 이를 통해 가상화 환경에서 여러 ESXi 호스트 간 VM 관리를 효율적으로 수행할 수 있음.

**1. VMFS5 vs. VMFS6 비교**

| **기능**                                         | **VMFS5** | **VMFS6** |
| ------------------------------------------------ | --------- | --------- |
| **공유 스토리지 동시 접근**                      | ✅ 지원    | ✅ 지원    |
| **스토리지 확장 (Dynamic Expansion)**            | ✅ 지원    | ✅ 지원    |
| **온디스크 락킹 (On-disk Locking)**              | ✅ 지원    | ✅ 지원    |
| **4K 네이티브 스토리지 지원**                    | ❌ 미지원  | ✅ 지원    |
| **자동 공간 회수 (Automatic Space Reclamation)** | ❌ 미지원  | ✅ 지원    |
| **데이터스토어당 최대 ESXi 호스트 수**           | 64        | 128       |

**2. VMFS 특징 및 장점**

* **동시 접근**: 여러 ESXi 호스트가 동시에 하나의 VMFS 데이터스토어에 접근 가능.
* **가상 머신 라이프사이클 관리 용이**:
* **vMotion**: 실행 중인 VM을 다운타임 없이 다른 ESXi 호스트로 이동 가능.
* **HA(High Availability)**: 장애 발생 시 다른 호스트에서 자동 재시작 가능.
* **DRS(Distributed Resource Scheduler)**: VM을 여러 물리 서버에 균등하게 배치.
* **VM 프로비저닝 간소화**:
  * 모든 VM 파일(VMDK, 설정 파일 등)을 **중앙에서 관리**.
  * VMFS 데이터스토어는 대형 파일(VMDK)과 소형 파일을 **효율적으로 저장**.

* **스토리지 확장 가능**:
  * 실행 중인 VM이 있는 상태에서도 **데이터스토어 크기 동적 확장 가능**.
  * **서버 충돌 시 데이터 보호**:
  * **온디스크 락킹 (On-disk Locking)** 사용 → 동일 VM이 여러 서버에서 동시에 실행되는 것을 방지.
  * ESXi 호스트 장애 시 **잠금 해제 후 다른 호스트에서 VM 자동 복구 가능**.

**3. VMFS 데이터스토어 구성 요소**

* **VMFS 데이터스토어는 하나 이상의 LUN(Logical Unit Number)으로 구성됨**.
* **VM에서 보는 스토리지 구조**:
  * VM 내부에서는 **VMFS가 아닌 네이티브 SCSI 디스크처럼 보임**.
  * 즉, VM은 VMFS를 직접 인식하지 않고, 운영체제(OS)에서 볼 때는 **일반적인 디스크로 인식**됨.

* **VMFS 데이터스토어에 저장되는 주요 파일**:
  * **VM 구성 파일 (VMX)**
  * *가상 디스크 파일 (VMDK)
  * 스왑 파일 (VSWP)
  * 로그 파일 등**.

**4. VMFS 지원 스토리지 유형**

VMFS는 **SCSI 기반 스토리지**에서만 사용 가능하며, 아래 세 가지 스토리지 방식에서 지원됨:

​	1.	**DAS (Direct-attached Storage)**: 개별 호스트에 직접 연결된 스토리지.

​	2.	**Fibre Channel (FC) Storage**: 고속 SAN(스토리지 영역 네트워크) 기반의 FC 프로토콜 사용.

​	3.	**iSCSI Storage**: TCP/IP 기반으로 SCSI 명령을 전송하는 스토리지 방식.



​	**⚠️ 주의: NFS 데이터스토어는 VMFS가 아님!**

​		•	NFS는 네트워크 파일 시스템 방식이며, VMFS는 블록 스토리지 기반의 파일 시스템.

**5. 그림 설명**

​	•	**각 ESXi 호스트에는 2개의 VM이 실행됨**.

​	•	**VM 디스크(VMDK)는 VMFS 데이터스토어에 저장됨**.

​	•	**VM에서 스토리지를 접근할 때 SCSI 디바이스처럼 인식하지만, 실제로는 VMFS 파일 시스템을 사용하여 관리됨**.

​	•	**VMFS 데이터스토어는 여러 LUN으로 구성될 수 있으며, 여러 호스트가 동시에 접근 가능**.

**6. 결론**

​	•	**VMFS는 여러 ESXi 호스트가 공유하는 고성능 파일 시스템으로, vSphere 환경에서 핵심적인 역할을 수행**.

​	•	**VMFS6는 4K 스토리지 지원, 자동 공간 회수 기능 등 VMFS5보다 개선된 기능을 제공**.

​	•	**VMFS 데이터스토어를 사용하면 vMotion, HA, DRS 등 vSphere의 핵심 기능을 활용할 수 있음**.



------

------

**vSAN 개요**

![image-20250306110347615](/assets/cisco_post_img/2025-03-06-Module 6 Configuring vSphere Storage//image-20250306110347615.png)

vSAN은 **하이퍼컨버지드 인프라(HCI)** 환경을 위한 **소프트웨어 정의 스토리지(SDS)** 솔루션으로, 기존의 외부 SAN/NAS 스토리지 없이 **ESXi 호스트의 로컬 디스크(SSD 및 HDD)를 활용하여 공유 데이터스토어를 구성**한다.

**그림 설명**

* **가상 머신(VM)**
  * 그림의 최상단에는 여러 개의 VM이 존재하며, 이들은 vSAN 데이터스토어를 통해 스토리지를 공유한다.
  * 각 VM은 개별 물리적 디스크에 직접 저장되지 않고, **가상화된 단일 스토리지 풀**을 사용한다.

* **vSAN 계층(파란색)**
  * 가상 머신과 물리적 디스크 간의 인터페이스 역할을 한다.
  * 스토리지 정책을 적용하고, 가상 머신의 스토리지를 자동으로 관리한다.

* **vSphere 계층(연두색)**
  * vSphere는 가상 머신과 물리적 호스트를 관리하는 역할을 한다.
  * vSphere와 vSAN이 통합되어, vSphere 환경에서 네이티브하게 스토리지를 운영할 수 있다.

* **디스크 계층(스토리지 풀, 점선 박스 내부)**
  * 각 ESXi 호스트의 **SSD 및 HDD가 하나의 공유 데이터스토어로 묶인다**.
  * **SSD는 캐시(Read Cache/Write Buffer), HDD는 저장소(Capacity Tier)** 로 사용된다.
  * **공유 데이터스토어(가장 아래 원통형 아이콘)**
  * vSAN이 제공하는 **단일 논리 데이터스토어**를 의미한다.
  * 개별 호스트의 로컬 디스크를 묶어 **공유 스토리지처럼 활용**할 수 있다.

**📌 1️⃣ vSAN 데이터스토어**

​	•	클러스터 내 모든 **ESXi 호스트가 공유하는 단일 데이터스토어**가 생성된다.

​	•	개별 호스트의 디스크를 **논리적으로 묶어 하나의 분산 데이터스토어로 제공**한다.

**📌 2️⃣ vSAN 스토리지 구성 방식**



vSAN은 **하이브리드(Hybrid) 스토리지와 올-플래시(All-Flash) 스토리지**의 두 가지 방식으로 구성할 수 있다.

1. **하이브리드 스토리지(Hybrid)**
   * **SSD(플래시)** → 캐시(Read Cache/Write Buffer) 역할.
   * **HDD(자기 디스크)** → 대용량 데이터 저장.
   * **SSD 캐시를 통해 성능을 가속화**하며, 비용 절감이 가능함.

2. **올-플래시 스토리지(All-Flash)**
   * **모든 디스크를 SSD로 구성**하여 빠른 응답 속도를 제공.
   * **고성능 SSD → 캐시 계층, 비용 효율적인 SSD → 용량 계층** 으로 구분하여 비용 대비 성능을 최적화.

**📌 3️⃣ vSAN의 주요 기능**

* **스토리지 가상화 및 자동화 지원**
  * 개별 디스크를 논리적으로 묶어 **가상화된 스토리지 풀**을 제공.
  * 특정 호스트에 장애가 발생해도 데이터 복제 기능을 통해 **가용성을 유지**.

* **스토리지 정책 기반 관리(SPBM)**
  * 가상 머신(VM)마다 스토리지 요구사항을 개별적으로 설정 가능.
  * QoS(서비스 품질) 보장을 통해 **워크로드에 맞는 성능 최적화**.

* **유연한 확장성**
  * *호스트를 추가하면 자동으로 vSAN 클러스터의 용량과 성능이 확장**됨.

**📌 4️⃣ vSAN의 장점**



✅ **SAN/NAS 없이 공유 스토리지 운영 가능**

✅ **비용 절감** – 외부 SAN 장비 불필요

✅ **성능 최적화** – SSD 기반 캐시, 데이터 중복 제거, 압축 지원

✅ **관리 용이성** – vSphere와 통합된 단순한 관리 인터페이스

**📌 결론**



vSAN은 기존 SAN/NAS 스토리지 없이 **소프트웨어 정의 스토리지(SDS) 기반으로 가상화 환경을 최적화하는 강력한 솔루션**이다. **스토리지와 컴퓨팅 자원을 통합하여 비용 효율성과 성능을 동시에 제공**하며, **하이퍼컨버지드 인프라(HCI) 구축을 위한 핵심 기술**로 활용된다.

------

------

## Raw Device Mapping (RDM)

![image-20250306110659937](/assets/cisco_post_img/2025-03-06-Module 6 Configuring vSphere Storage//image-20250306110659937.png)

**RDM 개념**

Raw Device Mapping(RDM)은 VM이 물리적 LUN(Logical Unit Number)에 직접 접근할 수 있도록 하는 기능이다. 이는 일반적인 VMFS 데이터스토어를 사용하는 방식과는 다르게 VM의 가상 디스크 파일(.vmdk)을 거치지 않고, 물리적인 스토리지 디바이스에 직접 연결할 수 있도록 한다. 그러나 RDM을 사용하려면 VM을 LUN에 연결하는 매핑 파일(-rdm.vmdk)이 **VMFS 데이터스토어**에 저장되어야 한다.



**RDM의 동작 방식**

​	•	RDM은 **VMFS 데이터스토어에 저장된 작은 매핑 파일**을 통해 물리적 LUN을 가리킨다.

​	•	VM은 매핑 파일을 통해 물리적인 디스크를 직접 접근하는 것처럼 작동한다.

​	•	스토리지의 물리적 특성을 유지해야 하는 애플리케이션이나, 스냅샷 생성이 필요한 경우 유용하게 활용된다.

**RDM이 필요한 경우**

​	•	**SAN 스냅샷 기능 사용**: VM에서 SAN(Storage Area Network) 스냅샷을 직접 활용하려면 RDM이 필요하다.

​	•	**P2V(Physical to Virtual) 변환에서 대량의 데이터 이동 방지**: 기존 물리 서버의 데이터를 VM으로 변환할 때, 데이터를 이동하지 않고 직접 접근하는 방식으로 활용할 수 있다.

​	•	**SAN 기반 클러스터링 사용**: MSCS(Microsoft Cluster Service)와 같은 SAN 기반 클러스터링 솔루션을 실행할 때 RDM이 필수적이다.

**RDM의 두 가지 모드**

1. ------
   
   **가상 호환 모드 (Virtual Compatibility Mode)**
   * RDM을 일반적인 가상 디스크처럼 동작하게 만들어, **스냅샷, 복제, VMotion**과 같은 가상화 기능을 사용할 수 있음.
   * 확장자가 -rdm.vmdk 로 저장됨.
   
2. ------
   
   **물리 호환 모드 (Physical Compatibility Mode)**
   
   * RDM을 통해 VM이 물리적인 디스크에 직접 접근하도록 함.
   * VM의 게스트 OS가 물리 디스크의 **SCSI 명령어를 직접 활용**할 수 있어 SAN-aware 애플리케이션 실행에 유리함.
   * 그러나 **스냅샷, 복제 기능을 지원하지 않음**.
   * 확장자가 -rdmp.vmdk 로 저장됨.

**그림 설명**

​	•	**왼쪽 그림**: VM이 직접 LUN에 연결되어 데이터를 읽고 쓰며, RDM을 통해 디스크에 접근하는 모습을 보여준다.

​	•	**오른쪽 그림**: VM이 RDM을 사용하여 스토리지 간 데이터를 복제하는 모습이다. 이는 SAN 기능을 활용한 스냅샷 및 백업 환경을 나타낸다.

**결론**



RDM은 가상 환경에서 **물리적 디스크의 특성을 유지하면서도, 가상 머신이 직접 LUN에 접근해야 할 때** 유용하게 사용된다. 가상 환경에서 SAN 스토리지를 활용하는 애플리케이션이 있거나, VM이 대량의 데이터를 관리할 때 적절한 솔루션이 될 수 있다.

------

------

## **Physical Storage Considerations (물리적 스토리지 고려사항)**



vSphere 환경을 구축하기 전에 **스토리지 관리팀과 협의하여** 적절한 스토리지 설계를 수립하는 것이 중요하다. 아래의 주요 요소들을 고려해야 한다.

------

**1. LUN 크기 (LUN Sizes)**

​	•	LUN(Logical Unit Number)은 스토리지 배열에서 논리적으로 할당된 디스크 영역이다.

​	•	**애플리케이션이 요구하는 스토리지 크기에 맞게 적절한 LUN 크기를 결정해야 한다.**

​	•	너무 큰 LUN은 성능 저하 및 관리의 어려움을 초래할 수 있으며, 너무 작은 LUN은 확장성과 유연성이 제한될 수 있다.

------

**2. 애플리케이션별 I/O 대역폭 (I/O Bandwidth Requirements)**

​	•	애플리케이션이 **얼마나 많은 데이터 전송을 필요로 하는지 분석**하여 **스토리지와 네트워크의 대역폭을 충분히 확보해야 한다.**

​	•	데이터베이스, 가상 데스크톱 인프라(VDI), 빅데이터 분석과 같은 워크로드는 높은 대역폭을 필요로 할 수 있다.

------

**3. LUN의 초당 입출력 요청 처리 능력 (I/O Requests Per Second, IOPS)**

​	•	스토리지가 초당 처리할 수 있는 **I/O 요청(Reads/Writes per second, IOPS)을 평가해야 한다.**

​	•	애플리케이션이 요구하는 **IOPS보다 낮은 성능의 스토리지를 사용하면 병목현상이 발생**할 수 있음.

​	•	SSD, NVMe 등의 고성능 디스크를 사용하면 높은 IOPS를 지원할 수 있음.

------

**4. 디스크 캐시 매개변수 (Disk Cache Parameters)**

​	•	스토리지의 디스크 캐싱 설정이 성능에 큰 영향을 미칠 수 있다.

​	•	**쓰기 캐시(Write Cache)**, **읽기 캐시(Read Cache)** 설정을 최적화하여 I/O 성능을 향상할 수 있다.

​	•	RAID 컨트롤러 및 SAN 스토리지의 캐시 정책을 적절하게 조정해야 한다.

------

**5. Zoning 및 Masking 설정**

​	•	**Zoning:** SAN 환경에서 **특정 호스트만 특정 스토리지를 볼 수 있도록 하는 설정** (FC 스위치에서 구성).

​	•	**LUN Masking:** 특정 호스트가 **특정 LUN에만 접근 가능하도록 설정하는 보안 기능** (스토리지 어레이에서 설정).

​	•	**적절한 Zoning 및 Masking을 설정하지 않으면, 보안 문제 및 충돌이 발생할 수 있음.**

------

**6. 다중 경로(Multipathing) 설정**

​	•	**스토리지 배열의 다중 경로(Multipathing) 정책을 설정하여 고가용성을 보장**해야 한다.

​	•	스토리지가 **Active-Active** 또는 **Active-Passive** 환경인지 확인해야 한다.

​	•	**Active-Active**: 두 경로가 동시에 사용 가능하며, 성능이 향상됨.

​	•	**Active-Passive**: 하나의 경로만 활성화되며, 장애 발생 시 다른 경로가 활성화됨.

​	•	적절한 **Path Selection Policy** (MRU, Fixed, Round Robin) 설정이 필요함.

------

**7. NFS 데이터스토어의 내보내기 속성 (Export Properties for NFS Datastores)**

​	•	NFS 데이터스토어를 사용할 경우, **엑세스 제어 및 성능 튜닝을 위한 속성을 확인해야 한다.**

​	•	**NFS v3**와 **NFS v4.1**의 차이를 고려해야 하며, vSphere 환경에 따라 적절한 버전을 선택해야 한다.

​	•	ESXi 호스트에서 **NFS 서버의 내보내기(export) 설정**이 올바르게 구성되었는지 확인해야 함.

**결론**



vSphere 환경에서 **스토리지 성능과 안정성을 보장하려면**, 위와 같은 **LUN 크기, I/O 요구사항, 다중 경로 설정, 캐시 정책, 보안 설정** 등을 철저하게 검토해야 한다. 특히 **워크로드에 맞는 최적의 스토리지 구성을 선택하는 것이 중요**하며, SAN, NAS, DAS 환경에서 각각의 특징을 이해하고 적절한 설정을 적용해야 한다.



------

------

### 기타

NIC - network interface card (이더넷용 adapter)

HBA - host bus adapter (스토리지용 adapter)

CNA - converged network adapter (NIC + HBA)
