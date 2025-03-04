---
title: <h0>Module 4-3 Managing vCenter Inventory</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-28-cisco]
tags: [Network, Cloud]












---

# Managing vCenter Inventory



## Navigating the Inventory

![image-20250228175149446](/assets/cisco_post_img/2025-02-28-Module 4-3 Managing vCenter Inventory A//image-20250228175149446.png)

------

## Views for Hosts, Clusters, VMs, and Templates

![image-20250228175159621](/assets/cisco_post_img/2025-02-28-Module 4-3 Managing vCenter Inventory A//image-20250228175159621.png)

**vSphere Client의 호스트, 클러스터, VM 및 템플릿 인벤토리 뷰**



**1. Host and Clusters Inventory View (호스트 및 클러스터 인벤토리 뷰)**

​	•	데이터센터 내 모든 **호스트 및 클러스터 객체**를 한눈에 볼 수 있음

​	•	CPU, 메모리, 스토리지 사용량을 확인 가능

​	•	호스트 및 클러스터를 폴더로 구성하여 관리 가능



**2. VMs and Templates Inventory View (VM 및 템플릿 인벤토리 뷰)**

​	•	데이터센터 내 **모든 가상 머신(VM) 및 템플릿 객체** 표시

​	•	VM의 전원 상태, OS 유형, VMware Tools 버전 등 세부 정보 확인 가능

​	•	VM 및 템플릿을 폴더로 구성하여 효율적으로 관리 가능

------

## Views for Storage and Networks(**vSphere Client의 스토리지 및 네트워크 인벤토리 뷰**)

![image-20250228175221123](/assets/cisco_post_img/2025-02-28-Module 4-3 Managing vCenter Inventory A//image-20250228175221123.png)

**1. Storage Inventory View (스토리지 인벤토리 뷰)**

​	•	데이터센터 내 모든 데이터스토어 정보를 표시

​	•	VMFS, NFS, iSCSI 등의 스토리지 유형 확인 가능

​	•	용량 및 사용량을 한눈에 파악 가능



**2. Networks Inventory View (네트워크 인벤토리 뷰)**

​	•	표준 스위치 및 분산 스위치의 포트 그룹 정보 제공

​	•	데이터센터 내 네트워크 토폴로지 및 연결된 호스트 확인

​	•	네트워크 상태 및 설정 관리 가능

------

**vSphere 분산 스위치 (vSphere Distributed Switch, VDS) 개념**

vSphere **분산 스위치(VDS)** 는 여러 ESXi 호스트에 걸쳐 **중앙에서 네트워크 구성을 관리**할 수 있는 가상 스위치로 **기본적인 표준 가상 스위치(VSS)와 달리**, 분산 스위치는 vCenter에서 중앙 집중식으로 관리되며, 여러 호스트에 걸쳐 **일관된 네트워크 정책을 적용**할 수 있다.



------

**vSwitch port 종류**

- **port group**
- **uplink**
- **VMkernel port**



------

### **포트 그룹(Port Group) 개념**

**포트 그룹(Port Group)** 은 **vSphere에서 가상 머신(VM) 및 서비스들이 네트워크를 사용할 수 있도록 설정하는 논리적 네트워크 그룹**이다.  포트 그룹을 사용하면 **일관된 네트워크 정책(예: VLAN, 트래픽 쉐이핑, 보안 정책 등)을 적용**할 수 있으며, 표준 스위치(VSS) 및 분산 스위치(VDS)에서 모두 사용된다.



**포트 그룹의 역할**

​	•	가상 머신이 물리적 네트워크와 연결될 수 있도록 함

​	•	VLAN 태깅 및 보안 정책 적용 가능

​	•	트래픽 쉐이핑(대역폭 관리) 설정 가능

​	•	여러 VM이 같은 네트워크를 공유할 수 있도록 설정



------

### **Uplink 포트란**

**Uplink 포트(Uplink Port)**는 **물리적인 네트워크 어댑터(NIC, Network Interface Card)**와 **가상 스위치(vSwitch)**를 연결하는 역할

**Uplink 포트의 역할**

1. **ESXi 호스트의 물리 NIC와 vSwitch 연결**
   *  ESXi 호스트에는 하나 이상의 물리 NIC가 있으며, 이를 **업링크 포트**를 통해 vSwitch에 연결합니다.
   * 가상 머신(VM) 및 VMkernel 트래픽이 외부 네트워크와 통신할 수 있도록 지원합니다.

2. **네트워크 트래픽 전달**
   * VM과 외부 네트워크 간의 트래픽을 처리합니다.
   * 업링크를 통해 vSwitch가 물리 네트워크와 통신하여 패킷을 송수신합니다.

3. **NIC 팀(NIC Teaming) 구성 가능**
   * **다중 업링크**를 구성하여 **고가용성(HA)** 및 **로드 밸런싱**을 지원합니다.
   * 여러 개의 물리 NIC를 vSwitch에 연결하면 네트워크 장애 시 자동으로 대체 NIC가 사용됩니다.

------

**Uplink 포트 구성**

**✅ 표준 가상 스위치(Standard vSwitch)에서 Uplink**

​	•	기본적으로 ESXi 호스트의 **물리 NIC을 vSwitch에 바인딩**하여 업링크로 사용합니다.

​	•	예시:

​	•	vmnic0, vmnic1을 vSwitch0의 Uplink로 설정



**✅ 분산 가상 스위치(VDS, vSphere Distributed Switch)에서 Uplink**

​	•	VDS에서는 **업링크 포트 그룹(Uplink Port Group)**을 사용하여 업링크를 관리합니다.

​	•	업링크는 모든 ESXi 호스트에서 **일관되게 구성**되며, VDS 정책을 적용할 수 있습니다.

------

**Uplink 포트의 주요 기능**

| **기능**                 | **설명**                                                     |
| ------------------------ | ------------------------------------------------------------ |
| **외부 네트워크와 연결** | 업링크를 통해 vSwitch가 물리 네트워크와 통신                 |
| **로드 밸런싱**          | 여러 개의 NIC를 묶어 네트워크 부하를 분산                    |
| **고가용성(HA)**         | 한 개의 물리 NIC에 장애가 발생하면 다른 NIC가 자동으로 트래픽을 처리 |
| **VLAN 태깅**            | VLAN ID를 설정하여 트래픽을 논리적으로 구분 가능             |

**4️⃣ Uplink 포트 구성 예시**



**(1) 표준 스위치에서 업링크 추가**

​	1.	**vSphere Client 로그인**

​	2.	**ESXi 호스트 → Configure → Networking → Virtual Switches** 선택

​	3.	**vSwitch0 선택 후 “Edit” 클릭**

​	4.	**Uplink 추가(vmnic1, vmnic2 등 선택)**

​	5.	**Save** 클릭하여 적용



**(2) 분산 스위치에서 업링크 추가**

​	1.	**vSphere Client → Networking → Distributed Switch 선택**

​	2.	**“Uplink Port Group” 설정**

​	3.	**Uplink1, Uplink2 등 추가**

​	4.	**ESXi 호스트의 물리 NIC을 업링크에 매핑**

​	5.	**저장 후 적용**

------

**5️⃣ Uplink vs. 다른 vSwitch 포트 비교**

| **포트 타입**     | **역할**                                        |
| ----------------- | ----------------------------------------------- |
| **Port Group**    | VM들이 속하는 가상 네트워크 그룹                |
| **Uplink**        | 물리 NIC과 vSwitch를 연결하는 포트              |
| **VMkernel Port** | vMotion, 관리 트래픽, 스토리지 통신을 위한 포트 |

**✅ 결론**

​	•	**Uplink 포트**는 **ESXi 호스트의 물리 NIC과 vSwitch를 연결하는 포트**

​	•	**외부 네트워크와의 연결, NIC 팀, VLAN 태깅, HA/로드 밸런싱** 등을 제공

​	•	**표준 가상 스위치(vSwitch) 및 분산 가상 스위치(VDS)** 모두에서 사용 가능



 **vSphere 네트워크 아키텍처에서 필수적인 요소이며, 올바른 Uplink 설정은 가상 환경의 성능과 안정성을 보장합니다.** 

------

------

## Viewing Object Information

![image-20250228175240214](/assets/cisco_post_img/2025-02-28-Module 4-3 Managing vCenter Inventory A//image-20250228175240214.png)

vSphere Client에서 **객체(Object) 정보를 조회하는 기능**을 통해 **VM, 호스트, 네트워크, 스토리지 등의 상태를 한눈에 파악하고 관리**할 수 있다.

**1. 주요 기능**

**객체 정보 보기**

​	•	VM의 현재 상태, 실행 중인 호스트, 네트워크 및 데이터스토어 정보 확인

​	•	CPU, 메모리, 디스크 사용량 등의 리소스 상태 모니터링

**관련 객체(Related Objects) 조회**

​	•	특정 VM이 연결된 **ESXi 호스트, 네트워크, 스토리지 정보**를 빠르게 확인 가능

​	•	트러블슈팅 및 성능 모니터링에 유용

**웹 콘솔 및 원격 접속**

​	•	Launch Remote Console 또는 Launch Web Console을 이용하여 VM에 원격으로 접근 가능



------

**2. 사용 사례**

​	•	VM의 **CPU, 메모리, 스토리지 사용량 확인**

​	•	VM이 실행 중인 **호스트(ESXi) 및 연결된 네트워크, 데이터스토어 조회**

​	•	**리소스 할당 문제 진단 및 성능 최적화**

​	•	VM의 **네트워크 트래픽 및 스토리지 사용량 분석**



vSphere Client를 활용하면 VM과 관련된 모든 객체 정보를 통합적으로 확인하고 효율적으로 관리할 수 있습니다.

------

------

## About Data Center Objects(**vCenter Data Center 객체 개요**)

![image-20250228175322218](/assets/cisco_post_img/2025-02-28-Module 4-3 Managing vCenter Inventory A//image-20250228175322218.png)

**vCenter Data Center(데이터 센터) 객체**는 **ESXi 호스트, 가상 머신(VM), 네트워크, 데이터스토어 등을 논리적으로 그룹화하는 단위**입니다. 이를 통해 기업은 다양한 가상 환경을 **체계적으로 구성하고 관리**할 수 있다.

------

**1. 주요 개념**

**가상 데이터 센터(Virtual Data Center)**

​	•	vCenter 내에서 가상 인프라 리소스를 조직화하는 논리적 단위

​	•	물리적인 데이터 센터와는 독립적으로, 여러 데이터 센터 객체를 생성하여 다양한 환경을 운영 가능

**데이터 센터 내 포함된 주요 인벤토리 객체**

​	•	**ESXi 호스트(Hosts)** → 가상 머신을 실행하는 물리적 서버

​	•	**가상 머신(VMs)** → 데이터 센터 내에서 실행되는 가상 환경

​	•	**템플릿(Templates)** → 새로운 VM을 빠르게 배포하기 위한 미리 구성된 VM

​	•	**데이터스토어(Datastores)** → 가상 머신 디스크(VMDK) 및 기타 파일 저장 공간

​	•	**네트워크(Networks)** → 표준 스위치 및 분산 스위치를 통해 VM 간 통신을 관리



------

**2. 데이터 센터 객체의 장점**

​	**다중 데이터 센터 구성 가능**

​	•	다양한 물리적 위치(예: 토론토, LA, 뮌헨, 파리 등)에 데이터 센터를 생성 가능

​	•	각 데이터 센터는 독립적인 리소스를 관리하며, 필요에 따라 공유 가능

​	**인프라 리소스를 효율적으로 조직화**

​	•	개발, 테스트, 운영 환경을 별도로 구성 가능

​	•	리소스를 논리적으로 분리하여 보안 및 관리 용이

​	**중앙 집중식 관리**

​	•	vCenter를 통해 모든 데이터 센터 객체를 한 곳에서 모니터링 및 제어

​	•	여러 데이터 센터 간 리소스 공유 및 통합 가능



------

**3. 사용 사례**

✅ **기업 내 글로벌 데이터 센터 관리**

→ 여러 지역(토론토, LA, 뮌헨, 파리 등)에 있는 가상 환경을 하나의 vCenter에서 통합 관리

✅ **운영 환경과 테스트 환경 분리**

→ 운영 환경과 개발/테스트 환경을 각각 별도의 데이터 센터 객체로 구성하여 격리

✅ **대규모 인프라 리소스 관리**

→ 대기업이나 클라우드 서비스 제공업체가 여러 고객의 리소스를 논리적으로 분리하여 관리



**결론**

**vCenter의 데이터 센터 객체**는 **ESXi 호스트, VM, 네트워크, 스토리지를 논리적으로 그룹화**하여 **효율적인 IT 운영 및 관리를 가능하게 하는 핵심 요소**이다.

------

------

## Organizing Inventory Objects into Folders(**vSphere 인벤토리 객체 구성 및 폴더 관리**)

![image-20250228175335723](/assets/cisco_post_img/2025-02-28-Module 4-3 Managing vCenter Inventory A//image-20250228175335723.png)

**vSphere 환경에서는 데이터 센터 내의 다양한 인벤토리 객체(ESXi 호스트, VM, 네트워크, 스토리지)를 논리적으로 구성하고 관리하기 위해 폴더(Folders)를 활용할 수 있습니다.** 폴더를 사용하면 관리가 용이해지고, 규모가 큰 환경에서도 효율적인 리소스 조직이 가능합니다.



------

**1. 인벤토리 객체의 주요 폴더 구조**

vSphere는 **각 인벤토리 뷰(Inventory View)별로 고유한 폴더 구조**를 가질 수 있습니다.

✅ **VM 및 템플릿 인벤토리 뷰 (VMs and Templates Inventory View)**

​	•	가상 머신(VMs) 및 템플릿(Templates) 객체를 관리하는 폴더

​	•	예: “DB” 폴더에 데이터베이스 VM 그룹 배치, “File and Print” 폴더에 파일/프린트 서버 배치

✅ **호스트 및 클러스터 인벤토리 뷰 (Hosts and Clusters Inventory View)**

​	•	물리적 ESXi 호스트와 클러스터(Cluster) 객체를 관리하는 폴더

​	•	예: “Intel” 폴더에는 Intel 기반 서버, “AMD” 폴더에는 AMD 기반 서버 배치

✅ **스토리지 인벤토리 뷰 (Storage Inventory View)**

​	•	데이터스토어(Datastore) 객체를 관리하는 폴더

​	•	VMFS, NFS 등의 데이터스토어를 논리적으로 구분하여 배치

✅ **네트워크 인벤토리 뷰 (Networks Inventory View)**

​	•	네트워크 및 포트 그룹을 관리하는 폴더

​	•	표준 스위치(standard switch) 및 분산 스위치(distributed switch)와 연결된 포트 그룹을 구성



------

**2. 인벤토리 폴더 구성의 필요성**

​	•	**관리 효율성 증가** → 많은 VM과 호스트를 체계적으로 그룹화하여 관리 용이

​	•	**리소스 조직화** → 업무별, 부서별, 프로젝트별로 폴더를 나누어 인프라 구성 가능

​	•	**보안 및 권한 설정** → 폴더 단위로 사용자 권한을 적용하여 접근 제어 가능

​	•	**대규모 환경 지원** → 다중 데이터 센터와 클러스터를 관리하는 대기업 및 클라우드 환경에서 필수적



------

**3. 인벤토리 폴더 구성 방법**

📌 **1) 데이터 센터 생성**

📌 **2) 클러스터 및 호스트 추가**

📌 **3) VM 및 템플릿 구성**

📌 **4) 폴더를 활용한 논리적 그룹화**

📌 **5) 네트워크 및 스토리지 설정**



------

**4. 사용 사례**

✅ **대기업의 글로벌 데이터 센터 관리**

​	•	“Americas”, “EMEA”, “APAC” 등 지역별 폴더 생성

​	•	각 폴더에 해당 지역의 데이터 센터, VM, 호스트를 배치

✅ **부서별 가상 환경 분리**

​	•	“Development”, “Production”, “QA” 등 부서별 폴더 구성

​	•	부서별 VM 및 네트워크를 개별 관리

✅ **하드웨어 아키텍처별 클러스터 구분**

​	•	“Intel Servers”, “AMD Servers” 등 CPU 아키텍처별 폴더 배치



**결론**

**vSphere 인벤토리 폴더는 대규모 가상 환경을 효율적으로 관리하는 핵심적인 기능입니다.** 폴더를 활용하면 논리적인 구성 및 권한 관리가 용이해지고, 데이터 센터 내 리소스를 효과적으로 조직화할 수 있습니다.

------

------

## Adding a Data Center and Organizational Objects to vCenter

![image-20250228175351756](/assets/cisco_post_img/2025-02-28-Module 4-3 Managing vCenter Inventory A//image-20250228175351756.png)

------

## Adding ESXi Hosts to vCenter

![image-20250228175359997](/assets/cisco_post_img/2025-02-28-Module 4-3 Managing vCenter Inventory A//image-20250228175359997.png)

vSphere Client를 사용하여 ESXi 호스트를 vCenter에 추가할 수 있다.

------

### 기타

vShpere vSwitch

- standard switch
- distributed switch