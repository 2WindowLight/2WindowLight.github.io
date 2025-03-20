---
title: <h0>Module 8-3 Lesson 3 Migrating VMs with vSphere Storage vMotion</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-10-cisco]
tags: [Network, Cloud,vShphere]









---

# Lesson 3: Migrating VMs with vSphere Storage vMotion

------

## About vSphere Storage vMotion

![image-20250310130114315](/assets/cisco_post_img/2025-03-10-Module 8-3 Lesson 3 Lesson 3 Migrating VMs with vSphere Storage vMotion//image-20250310130114315.png)

1. **VM 무중단 마이그레이션**
   * 전원이 켜져 있는 VM(가상 머신)을 다른 유형의 데이터스토어로 이전 가능
   * 유지보수, 업그레이드 등의 이유로 원본 스토리지에서 VM을 다른 스토리지로 옮길 수 있음

2. **디스크 프로비저닝 타입 변경**
   * 마이그레이션 과정에서 VM 디스크의 프로비저닝 타입(예: 두꺼운 프로비저닝, 얇은 프로비저닝)을 변경 가능

3. **VM 파일 이름 관리**
   * 대상 데이터스토어에 VM의 파일(구성 파일, 가상 디스크 파일 등)을 생성할 때, VM 인벤토리 이름과 동일하게 설정하여 일관성 유지 가능

4. **스토리지 트래픽 분산 및 지연 시간 감소**

   * VM을 여러 데이터스토어 간 마이그레이션하여 트래픽을 분산

   * 스토리지 I/O 경합을 줄이고 지연 시간을 최소화할 수 있음

5. **용량 또는 성능 최적화를 위한 재배치**
   * VM 또는 가상 디스크를 다른 스토리지 볼륨으로 재배치하여 용량을 균형화하거나 성능을 향상
   * 필요에 따라 특정 워크로드에 맞는 스토리지를 선택해 배포 가능



------



이와 같이 **vSphere Storage vMotion**을 활용하면, 동작 중인 VM의 스토리지 이전이 유연하며, 디스크 타입 변경 및 파일 구조를 재정비함으로써 스토리지 활용도와 운영 효율을 높일 수 있습니다.

------

------

## **vSphere Storage vMotion 동작 원리 및 과정 정리**

![image-20250310130420443](/assets/cisco_post_img/2025-03-10-Module 8-3 Lesson 3 Lesson 3 Migrating VMs with vSphere Storage vMotion//image-20250310130420443.png)

1. **I/O 미러링 아키텍처**

​	•	vSphere Storage vMotion은 I/O 미러링 방식을 사용해 소스 데이터스토어에서 대상 데이터스토어로 디스크 블록을 복사함.

2. **마이그레이션 절차**

   * **스토리지 마이그레이션 시작**
     * 관리자가 Storage vMotion을 시작하면, VMkernel 또는 Storage APIs – Array Integration(SA-AI)을 통해 데이터를 복사할 준비를 함.

   * **VMkernel Data Mover 혹은 하드웨어 가속**
     * 내부적으로는 VMkernel의 Data Mover가 블록 단위로 데이터를 복사함.
     * 스토리지 어레이가 SA-AI(하드웨어 가속)를 지원하면, 복사 작업을 스토리지 어레이에 오프로드하여 성능을 높일 수 있음.

   * **새 VM 프로세스 생성**
     * 대상 데이터스토어에 VM의 복사본을 생성하고, 기존 VM과 동기화하기 위한 준비 과정이 시작됨.

   * **I/O 미러링 시작**
     * 이미 복사된 블록에 대한 I/O 요청은 대상 데이터스토어로 전달되고, 변경된 블록은 미러 드라이버를 통해 소스와 대상에 동시 기록됨(동기화).

   * **대상 VM 프로세스로 전환**
     * 최종적으로 VM의 프로세스가 대상 데이터스토어로 전환되어 새 디스크 복사본에 접근함으로써 마이그레이션이 완료됨.

3. **단일 패스 복사(Single Pass Copy)**
   * 전체 디스크를 한 번만 스캔 및 복사하며, 이미 복사된 후 변경된 블록은 미러 드라이버를 통해 소스에서 대상으로 다시 동기화함.
   * 추가적인 복사 패스를 여러 번 수행할 필요가 없어 시간과 리소스를 절약할 수 있음.

4. **트랜잭션 무결성 보장 및 성능**
   * 미러 드라이버가 I/O를 동시 기록하기 때문에 트랜잭션 무결성이 보장됨.
   * 최종 사용자는 큰 성능 저하나 중단 없이 스토리지 마이그레이션이 수행됨.

5. **호스트 내부 처리 vs. 스토리지 어레이 오프로드**
   * **호스트 내부 처리**: ESXi 호스트 내장 VMkernel Data Mover를 통해 블록 복사를 수행
   * **어레이 오프로드**: 스토리지 어레이가 SA-AI(하드웨어 가속)를 지원할 경우, 복사 작업을 어레이에서 직접 처리해 성능을 향상



이러한 과정을 통해 **vSphere Storage vMotion**은 실시간 운영 중인 VM을 중단 없이 다른 데이터스토어로 마이그레이션하고, 변경된 블록만 효율적으로 동기화하여 안정적이고 빠른 스토리지 이전을 가능하게 합니다.

------

------

## Changing Both Compute Resource and Storage During Migration

![image-20250310131224221](/assets/cisco_post_img/2025-03-10-Module 8-3 Lesson 3 Lesson 3 Migrating VMs with vSphere Storage vMotion//image-20250310131224221.png)

**컴퓨트 리소스와 스토리지 동시에 변경하기**

* **기본 개념**
  * 마이그레이션 시, VM은 **호스트**(Compute Resource)와 **데이터스토어**(Storage)를 동시에 변경할 수 있음.
  * 네트워크도 필요에 따라 동일하거나 다른 vCenter 인스턴스 간에 변경이 가능함.

* **기술 개요**

  * *vSphere vMotion**과 **vSphere Storage vMotion**을 결합하여 한 번의 작업으로 VM을 이동시킴.
  * VM을 클러스터, 데이터센터, 혹은 vCenter 인스턴스 간에 자유롭게 이전 가능.
  * **적용 범위**
    * 스토리지 접근 범위를 넘어서는 이동(예: 다른 스토리지 어레이나 다른 데이터센터)도 가능하며, 이를 통해 호스트 간, 클러스터 간, 데이터센터 간, 그리고 여러 vCenter 간에 유연하게 VM을 옮길 수 있음.

  * **효과**
    * *운영 효율**: 컴퓨트와 스토리지를 동시에 재배치하므로 계획된 다운타임이나 운영 중단 없이 인프라 자원을 최적화할 수 있음.**
    * 유연성 확대**: 클러스터나 vCenter 인스턴스 경계를 넘어 VM을 배포하거나 재배치할 수 있으므로, 워크로드 수요 변화에 대응하기 유리함.



이와 같은 방법을 통해, 단일 마이그레이션 과정을 통해 **호스트**, **스토리지**, (옵션에 따라) **네트워크**까지 동시에 변경하여, VM이 효율적으로 원하는 위치와 환경에서 동작하도록 구성할 수 있습니다.
