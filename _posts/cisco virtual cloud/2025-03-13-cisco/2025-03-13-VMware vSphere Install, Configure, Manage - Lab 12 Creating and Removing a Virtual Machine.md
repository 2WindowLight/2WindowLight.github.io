---
title: <h0>VMware vSphere Install, Configure, Manage - Lab 12 Creating and Removing a Virtual Machine</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-13-cisco]
tags: [Network, Cloud]



---

# Lab 12 Creating and Removing a Virtual Machine (**Lab 12: 가상 머신 생성 및 삭제**)



## **목표 및 작업 개요**



이 실습에서는 vSphere Client를 사용하여 가상 머신(VM)을 생성하고, vCenter 인벤토리에서 제거한 후, 다시 등록하거나 완전히 삭제하는 방법을 학습한다.



**수행할 작업**

1. **가상 머신 생성**
2. **vCenter 인벤토리에서 가상 머신 제거**
3. **제거된 가상 머신을 다시 등록**
4. **가상 머신을 데이터스토어에서 완전히 삭제**



------



**Task 1: 가상 머신 생성**



지정된 요구 사항을 기반으로 가상 머신을 생성하는 과정이다.

1. **vSphere Client 로그인**
   * sa-vcsa-01.vclass.local에 접속
   * 사용자: administrator@vsphere.local
   * 비밀번호: VMware1!

2. **새 가상 머신 생성**
   * **메뉴에서** Inventory 선택 → VMs and Templates 아이콘 클릭
   * Lab VMs 우클릭 → New Virtual Machine 선택

3. **가상 머신 마법사 설정**

   * Create a new virtual machine 선택 → NEXT

   * **VM 이름 및 폴더 선택:**
     * Photon-Empty 입력
     * Lab VMs 선택 → NEXT

   * **컴퓨팅 리소스 선택:**
     * ICM-Datacenter > Lab Servers > sa-esxi-02.vclass.local 선택 → NEXT

   * **스토리지 선택:**
     * iSCSI-Datastore 선택 → NEXT

   * **게스트 OS 선택:**
     * Guest OS Family: Linux
     * Guest OS Version: VMware Photon OS (64-bit) → NEXT

4. **하드웨어 구성**
   * **CPU**: 1개 유지
   * **메모리**: 1GB 설정
   * **하드 디스크**: 12GB 설정
   * New Hard Disk 확장 후 Thin Provisioning 선택
   * **네트워크**: Production 선택
   * **CD/DVD 드라이브**: Datastore ISO File 선택
   * ICM-Datastore에서 photon-3.0-a0f216d.iso 선택 후 OK
   * Connect At Power On 체크

5. **설정 검토 및 완료**
   * Ready to complete 페이지에서 설정 확인 후 FINISH
   * Lab VMs 폴더에서 Photon-Empty VM이 생성되었는지 확인
   * Summary 탭에서 VM의 하드웨어 및 스토리지 설정 확인



**추가 확인 사항**

* VM의 하드 디스크 및 설정 파일이 **iSCSI-Datastore**에 저장됨.
* **왜 두 개의 데이터스토어가 표시되는가?**
* ICM-Datastore: ISO 이미지가 마운트됨
* iSCSI-Datastore: VM의 가상 디스크 및 설정 파일이 저장됨



**참고:** 실전 환경에서는 이 단계에서 운영체제(OS)를 설치해야 하지만, 실습에서는 생략됨.



------



**Task 2: vCenter 인벤토리에서 가상 머신 제거**



가상 머신을 **vCenter에서만 제거(언레지스터)**하지만, 데이터스토어에는 여전히 존재한다.

1. **Photon-Empty VM 제거**
   * **VM을 우클릭 → Remove from Inventory 선택**
   * YES 클릭하여 제거 확인

2. **제거 여부 확인**
   * Photon-Empty가 **vSphere Client의 네비게이션 패널에서 사라졌는지 확인**
   * **VM의 파일이 데이터스토어에 남아 있는지 확인**
   * Storage 아이콘 클릭
   * iSCSI-Datastore 선택 후 Files 탭 클릭
   * Photon-Empty 폴더가 존재하고 .vmx, .vmdk 파일이 남아 있는지 확인



------



**Task 3: 가상 머신을 다시 vCenter에 등록**



vCenter에서 제거된 VM을 다시 등록하여 관리할 수 있도록 한다.

1. **VM 등록**
   * iSCSI-Datastore에서 Photon-Empty 폴더 열기
   * Photon-Empty.vmx 파일 선택 → REGISTER VM 클릭

2. **VM 등록 마법사 실행**
   * **VM 이름 및 폴더 선택:**
   * Lab VMs 폴더 선택 → NEXT
   * **컴퓨팅 리소스 선택:**
   * Lab Servers > sa-esxi-02.vclass.local 선택 → NEXT
   * **설정 검토 후 등록 완료:**
     * Ready to complete 페이지에서 FINISH 클릭
     * VMs and Templates 아이콘 클릭 후 **Photon-Empty가 다시 Lab VMs 폴더에 나타나는지 확인**



------



**Task 4: 데이터스토어에서 가상 머신 완전 삭제**



가상 머신을 **vCenter뿐만 아니라 데이터스토어에서도 영구적으로 삭제**하는 과정이다.

1. **VM 삭제**
   * Photon-Empty 우클릭 → Delete from Disk 선택
   * YES 클릭하여 삭제 확인

2. **삭제 여부 확인**
   * **VM이 vSphere Client에서 사라졌는지 확인**
   * **iSCSI-Datastore에서 삭제되었는지 확인**
   * Storage 아이콘 클릭
   * iSCSI-Datastore 선택 후 Files 탭 클릭
   * Photon-Empty 폴더가 삭제되었는지 확인



------



**결론**

이 과정에서는 **가상 머신을 생성, 제거, 다시 등록, 삭제하는 전체를 수행했다.**

* **가상 머신 생성:** VM을 설정하고 필요한 리소스를 할당.
* **vCenter에서 VM 제거:** VM을 언레지스터하여 관리 목록에서 제외하지만, 데이터스토어에는 파일이 남아 있음.
* **VM 다시 등록:** 기존 VM을 vCenter로 다시 불러와 관리 가능.
* **VM 완전 삭제:** VM의 모든 파일을 데이터스토어에서 삭제하여 완전히 제거.



**실전 환경에서는 VM을 삭제하기 전에 백업을 수행하는 것이 일반적이다.**