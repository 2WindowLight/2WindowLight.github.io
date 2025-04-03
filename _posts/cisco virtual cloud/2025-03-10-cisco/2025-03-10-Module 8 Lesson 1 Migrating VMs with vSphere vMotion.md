---
title: <h0>Module 8 Lesson 1 Migrating VMs with vSphere vMotion</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-10-cisco]
tags: [Network, Cloud,vShphere]







---

# Module 8: Managing Virtual Machines



> [!IMPORTANT]
>
> ### Module Lessons
>
> ------
>
> **1.Migrating VMs with vSphere vMotion**
>
> **2.Configuring Enhanced vMotion Compatibility**
>
> **3.Migrating VMs with vSphere Storage vMotion**
>
> **4.Cross vCenter Migrations**
>
> **5.Creating Virtual Machine Snapshots**
>
> **6.Virtual CPU and Memory Concepts**
>
> **7.Resource Controls**
>
> ------
>
> 



# Lesson 1: Migrating VMs with vSphere vMotion

------

## **VM Migration(가상 머신 마이그레이션)**



**가상 머신(VM) 마이그레이션은 VM을 한 호스트 또는 데이터스토어에서 다른 호스트 또는 데이터스토어로 이동하는 과정이다. vSphere 환경에서는 다양한 마이그레이션 옵션을 제공하며, 마이그레이션 방식은 사용자의 요구 사항과 인프라 구성에 따라 달라진다.**

------

**VM 마이그레이션 유형**

| **마이그레이션 유형**     | **설명**                                                    |
| ------------------------- | ----------------------------------------------------------- |
| **vMotion**               | VM의 실행 상태를 유지하면서 호스트 간 이동                  |
| **Storage vMotion**       | VM을 중단 없이 한 데이터스토어에서 다른 데이터스토어로 이동 |
| **Cold Migration**        | VM이 꺼진 상태에서 호스트 또는 데이터스토어 이동            |
| **Cross vCenter vMotion** | 서로 다른 vCenter 간에 실행 중인 VM을 이동                  |

------



**vMotion(실행 중인 VM 마이그레이션)**

* VM을 실행 상태에서 유지하면서 한 호스트에서 다른 호스트로 이동 가능
* 공유 스토리지를 사용할 경우, VM의 스토리지는 변경되지 않음
* VM의 CPU 및 네트워크 상태를 유지하며 마이그레이션 수행

------



**Storage vMotion(스토리지 마이그레이션)**

* VM을 종료하지 않고 한 데이터스토어에서 다른 데이터스토어로 이동 가능
* VM의 가상 디스크(VMDK) 파일을 변경 없이 다른 데이터스토어로 이동
* NFS, VMFS, vSAN과 같은 다양한 스토리지 간 이동 가능

------



**Cold Migration(종료된 VM 마이그레이션)**

* 라이브 마이그레이션이라고도 한다.
* 실행 중이 아닌 VM을 다른 호스트 또는 데이터스토어로 이동하는 방법
* vMotion이나 Storage vMotion을 사용할 수 없는 환경에서 사용
* VM이 꺼진 상태이므로 다운타임이 발생할 수 있음

------



**Cross vCenter vMotion(다른 vCenter 간 VM 이동)**

* 서로 다른 vCenter 인스턴스 간 실행 중인 VM을 이동 가능
* 동일한 vSphere Single Sign-On(SSO) 도메인 내에서 수행 가능
* vMotion 및 Storage vMotion을 활용하여 원격 마이그레이션 지원

------

**결론**

VM 마이그레이션은 vSphere 환경에서 VM을 효율적으로 관리하고 인프라를 최적화하는 필수 기능이다. vMotion, Storage vMotion, Cold Migration, Cross vCenter vMotion 등의 옵션을 활용하면 다운타임 없이 유연한 마이그레이션이 가능하며, 유지보수 및 운영 효율성을 극대화할 수 있다.

------

------

## Migration Types

![image-20250310180851643](/assets/cisco_post_img/2025-03-10-Module 8 Lesson 1 Migrating VMs with vSphere vMotion//image-20250310180851643.png)

------

------

## **vSphere vMotion(가상 머신 이동)**

![image-20250310180955547](/assets/cisco_post_img/2025-03-10-Module 8 Lesson 1 Migrating VMs with vSphere vMotion//image-20250310180955547.png)

vSphere vMotion은 실행 중인 **VM(가상 머신)을 한 ESXi 호스트에서 다른 ESXi 호스트로 이동**하는 기능이다. 이를 통해 하드웨어 유지보수 또는 부하 분산을 수행할 수 있으며, VM의 가용성을 유지할 수 있다.



------



1. **vSphere vMotion의 주요 기능**
   * **하드웨어 활용 최적화** → 실행 중인 VM을 적절한 리소스가 있는 호스트로 이동 가능
   * **ESXi 호스트 유지보수 지원** → VM을 중단 없이 다른 호스트로 이동하여 유지보수 수행
   * **vSphere DRS(Distributed Resource Scheduler) 연동** → 클러스터 내에서 부하를 분산하며 자동으로 VM 이동
   * **스토리지는 변경 없이 유지됨** → VM의 데이터는 동일한 데이터스토어에 남아 있음



------



2. **vSphere vMotion 동작 방식**



**(1) VM의 상태 이동**



vMotion은 VM의 **메모리 상태 및 실행 상태**를 복사하여 대상 호스트에서 동일한 상태로 실행되도록 한다.

​	•	VM의 **메모리 데이터** 및 **CPU 상태**가 네트워크를 통해 이동

​	•	VM을 식별하는 모든 정보(BIOS, EFI, MAC 주소 등)가 함께 전송됨

<img src="attachment://B8B13B3E-238E-43CA-8D8E-65CEEC2622F4.png" width="600"/>





------



3. **vSphere vMotion의 이점**

   * **서비스 중단 없이 VM 이동** → VM이 실행되는 동안에도 이동 가능

   * **리소스 최적화** → 가용 리소스가 많은 호스트로 VM을 재배치 가능

   * **유지보수 작업 지원** → 하드웨어 유지보수 중에도 VM을 다른 호스트로 이동하여 중단 없이 운영 가능



------



4. **vSphere vMotion의 제한 사항**
   * **vMotion을 수행하려면 네트워크 연결 필요** (VMkernel 네트워크 사용)
   * **두 호스트가 동일한 데이터스토어를 공유해야 함** (Storage vMotion을 사용하면 데이터스토어 간 이동 가능)
   * **vMotion을 사용하려면 호환되는 CPU가 필요함** (EVC 또는 CPU 호환성 설정 필요)



------



**5. vSphere vMotion과 Storage vMotion 차이점**

| **기능**        | **vSphere vMotion**          | **Storage vMotion**       |
| --------------- | ---------------------------- | ------------------------- |
| **이동 대상**   | ESXi 호스트 간 이동          | 데이터스토어 간 이동      |
| **VM 상태**     | 실행 중인 VM 이동 가능       | 실행 중인 VM 이동 가능    |
| **데이터 위치** | 기존 데이터스토어 유지       | 다른 데이터스토어로 이동  |
| **주요 목적**   | 하드웨어 유지보수, 부하 분산 | 스토리지 최적화, 유지보수 |





------

**결론**

vSphere vMotion은 **가상 머신을 중단 없이 이동할 수 있는 기능**으로, **하드웨어 유지보수 및 리소스 최적화**에 필수적이다. 이를 활용하면 **가용성을 유지하면서 운영 효율성을 극대화**할 수 있다.



------

------

## Configuring vSphere vMotion Networks

![image-20250310115319773](/assets/cisco_post_img/2025-03-10-Module 8 Lesson 1 Migrating VMs with vSphere vMotion//image-20250310115319773.png)

------

------

## **vSphere vMotion 마이그레이션 워크플로우**

![image-20250310115836643](/assets/cisco_post_img/2025-03-10-Module 8 Lesson 1 Migrating VMs with vSphere vMotion//image-20250310115836643.png)



위 그림은 vSphere vMotion을 통한 VM 마이그레이션 과정을 나타낸다.

* **ESXi01(소스 호스트)**에서 실행 중인 **VM A**가 **ESXi02(대상 호스트)**로 이동하는 과정이 표시되어 있다.
* VM의 **가상 디스크 및 구성 파일**은 공유 스토리지에 유지되며, vMotion을 통해 **메모리 상태가 전송**된다.
* **vSphere vMotion 네트워크**를 통해 메모리 복사가 수행되며, **Production Network**는 운영 트래픽을 처리한다.
* 마지막 단계에서 VM의 MAC 주소가 대상 호스트로 전환되며, 사용자는 중단 없이 VM을 사용할 수 있다.



------



**vSphere vMotion 마이그레이션 단계**

1. **섀도우 VM 생성**
   * 대상 호스트(ESXi02)에 섀도우 VM이 생성된다.

2. **메모리 상태 전송**
   * vSphere vMotion 네트워크를 통해 소스 호스트(ESXi01)에서 대상 호스트(ESXi02)로 VM의 메모리 상태가 복사된다.
   * 사용자는 계속 VM을 사용할 수 있으며, 변경된 메모리 페이지는 메모리 비트맵에 저장된다.
   * 메모리 비트맵

3. **반복적 메모리 복사 진행**
   * 초기 메모리 복사가 완료되면, 변경된 페이지를 복사하는 과정이 반복된다.
   * 변경된 페이지가 500밀리초 이내에 복사될 수 있는 수준으로 감소할 때까지 반복된다.

4. **VM 정지(Quiesce) 및 상태 전송**
   * 대부분의 메모리가 복사되면 VM이 정지(Quiesce)되며, 더 이상 변경이 발생하지 않는다.
   * VM의 장치 상태 및 메모리 비트맵이 대상 호스트로 전송된다.

5. **대상 호스트에서 VM 시작**
   * 소스 호스트에서 VM이 중지되고, 대상 호스트에서 VM이 시작된다.
   * Gratuitous ARP(GARP) 요청을 통해 VM의 MAC 주소가 다른 물리적 스위치 포트에서 접근 가능하도록 업데이트된다.

6. **자원 해제 및 사용자 접근 전환**
   * 대상 호스트에서 VM의 파일을 잠그고, 소스 호스트는 해당 파일을 해제한다.
   * 사용자는 대상 호스트에서 실행되는 VM에 접근하게 된다.
   * 소스 호스트에서 VM이 사용했던 메모리 페이지가 해제된다.

------

------

## **vSphere vMotion을 위한 VM 요구사항**



vSphere vMotion을 사용하여 VM을 마이그레이션하려면 VM이 다음 요구사항을 충족해야 한다.

------



1. RDM(Raw Device Mapping) 디스크 관련 요구사항
   * VM이 **RDM 디스크**를 사용하는 경우, **RDM 파일 및 해당 LUN**이 대상 호스트에서 접근 가능해야 한다.
   * 그렇지 않으면 마이그레이션이 실패할 수 있다.



------



2. 가상 장치 연결 제한
   * VM이 **CD/DVD 또는 플로피 드라이브**에 **호스트 로컬 이미지가 마운트된 상태**이면 vMotion을 수행할 수 없다.
   * 따라서, 마이그레이션 전에 가상 장치의 연결을 해제해야 한다.



------



3. 원격 콘솔을 통한 장치 연결 가능
   * vMotion을 사용하여 **원격 콘솔을 통해 연결된 장치**(예: 물리적 장치 또는 디스크 이미지)가 있는 VM을 마이그레이션할 수 있다.
   * 하지만 **마운트된 ISO 파일**과 같은 **호스트에 종속적인 장치는 지원되지 않는다.**



위 요구사항을 충족하지 않으면 vSphere vMotion 마이그레이션이 실패할 수 있으므로, 사전에 확인이 필요하다.

------

------

## **Host Requirements for vSphere vMotion Migration(호스트 요구사항: vSphere vMotion 마이그레이션)**



vSphere vMotion 마이그레이션을 위해 소스와 대상 호스트는 다음 요구사항을 충족해야 한다.

1. vSphere vMotion 마이그레이션 (요구사항 1)
   * **스토리지 접근성**: 소스와 대상 호스트 모두 VM의 모든 스토리지에 접근할 수 있어야 하며, 데이터스토어 당 최대 128개의 동시 마이그레이션이 가능하다.
   * 대상 호스트의 스왑 파일 위치가 소스와 다르면 스왑 파일이 새 위치로 복사된다.
   * **vMotion 활성화된 VMkernel 포트**가 필요하다.
   * **관리 네트워크의 IP 주소 패밀리 일치**: 소스와 대상 호스트 모두 IPv4 또는 IPv6 중 동일한 주소 체계를 사용해야 한다.
   * IPv4로 등록된 호스트에서 IPv6로 등록된 호스트로 마이그레이션은 불가능하다.
   * **스왑 파일 복사 관련 고려사항**: 대상 호스트가 지정된 스왑 파일 위치에 접근할 수 없으면, VM 구성 파일과 함께 스왑 파일이 저장되며 이로 인해 마이그레이션 속도가 느려질 수 있다.

2. vSphere vMotion 마이그레이션 (요구사항 2)
   * 각 vMotion 프로세스는 vMotion 네트워크에서 최소 250 Mbit/second의 처리량을 필요로 한다.
   * **동시 마이그레이션 제한**: 1 Gbps 네트워크에서는 최대 4개, 10 Gbps(또는 그 이상의 속도) 네트워크에서는 최대 8개의 동시 마이그레이션이 가능하다.
   * 성능 향상을 위해 vMotion 트래픽 전용으로 최소 2개의 VMkernel 포트 그룹을 할당하는 것이 좋다.
   * **CPU 호환성**: 소스와 대상 호스트의 CPU 기능 집합이 호환되어야 한다.
   * 필요에 따라 Enhanced vMotion Compatibility(EVC)나 호환성 마스크를 사용하여 일부 기능을 숨길 수 있다.



이러한 요구사항을 충족해야만 vSphere vMotion을 통해 VM을 원활하게 마이그레이션할 수 있다.

------

------

## Performing a vSphere vMotion Migration

![image-20250310122012143](/assets/cisco_post_img/2025-03-10-Module 8 Lesson 1 Migrating VMs with vSphere vMotion//image-20250310122012143.png)

------

------

## **Migrating Encrypted VMs(암호화된 VM 마이그레이션)**

![image-20250310122322349](/assets/cisco_post_img/2025-03-10-Module 8 Lesson 1 Migrating VMs with vSphere vMotion//image-20250310122322349.png)

전원이 켜지면, 암호화된 VM은 자동으로 암호화된 vSphere vMotion을 사용하여 이동됩니다. 암호화되지 않은 VM의 경우, 다음의 암호화된 vSphere vMotion 메뉴 항목 중 하나를 선택하십시오:



**Disabled**

**Opportunistic (기본값): 소스 및 대상 호스트가 지원할 경우 암호화된 vSphere vMotion 사용**

**Required: 소스 또는 대상 호스트가 암호화된 vSphere vMotion을 지원하지 않으면 마이그레이션이 실패합니다**

------

1. 암호화된 VM의 마이그레이션
   * **암호화된 VM**이 켜져 있으면, 자동으로 **암호화된 vSphere vMotion**을 사용하여 마이그레이션이 수행된다.
   * 별도의 설정 없이 암호화된 VM은 보안이 유지된 채로 이동된다.

2. 암호화되지 않은 VM의 옵션
   * 암호화되지 않은 VM의 경우, 암호화된 vSphere vMotion을 적용할지 여부를 선택해야 한다.
   * **옵션에는 다음이 있다.**
   * *Disabled**: 암호화 기능을 사용하지 않음.**
   * **Opportunistic (기본값)**: 소스와 대상 호스트가 암호화를 지원하는 경우에만 암호화된 vSphere vMotion을 사용한다.
   * **Required**: 소스나 대상 호스트 중 하나라도 암호화를 지원하지 않으면 마이그레이션이 실패된다.

3. 암호화된 vSphere vMotion의 보안 효과
   * **암호화된 vSphere vMotion**은 마이그레이션 과정에서 전송되는 데이터의 **기밀성, 무결성, 진위**를 보장한다.
   * 이 기능은 모든 vSphere vMotion 변형(예: 호스트 간 마이그레이션, vCenter 간 마이그레이션)을 지원하지만, **암호화된 vSphere Storage vMotion은 지원되지 않는다.**

4. 암호화된 VM에 대한 제약
   * 이미 암호화된 VM의 경우, 암호화된 vSphere vMotion 기능을 끌 수 없다. 즉, 암호화된 VM은 항상 암호화된 방식으로 마이그레이션된다.
