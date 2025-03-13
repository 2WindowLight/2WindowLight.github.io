---
title: <h0>VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere Distributed Switches</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-13-cisco]
tags: [Network, Cloud]












---

# Lab 8 Configuring vSphere Distributed Switches

------

## **분산 스위치(Distributed Switch) 생성 및 설정 절차 정리**



### **Task 1: 분산 스위치(Distributed Switch) 생성**

vSphere 환경에서 단일 가상 스위치로 작동하는 분산 스위치를 생성하는 과정이다.

1. **vSphere Client 로그인**
   * sa-vcsa-01.vclass.local에 로그인 (사용자: administrator@vsphere.local, 비밀번호: VMware1!)

2. **분산 스위치 생성**

   * **네트워킹(Navigation) 패널에서** sa-vcsa-01.vclass.local을 확장하고 ICM-Datacenter를 우클릭

   ![image-20250313160331335](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313160331335.png)

   * Distributed Switch > New Distributed Switch 선택하여 새 분산 스위치 마법사 실행
   * **이름 설정:** vds-Lab 입력 후 NEXT 클릭
   * **버전 선택:** 8.0.0 - ESXi 8.0 and later 유지 후 NEXT 클릭
   * **포트 그룹 설정:**
     * 포트 그룹 이름: pg-SA-Production 입력
     * 기타 기본 설정 유지 후 NEXT 클릭

   * **설정 검토 후 생성:** Ready to complete 페이지에서 설정 확인 후 FINISH 클릭
   * **생성 확인:** ICM-Datacenter를 확장하여 vds-Lab이 추가되었는지 확인

   ![image-20250313162105557](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313162105557.png)

3. **pg-SA-Production 포트 그룹 설정 (Uplink 3 전용 사용)**

   * vds-Lab을 확장 후 pg-SA-Production 우클릭 → Edit Settings 선택
   * **Teaming and Failover** 항목에서 **Failover Order** 수정
   * Uplink 1, Uplink 2, Uplink 4를 **Unused uplinks** 섹션으로 이동
   * Uplink 3만 **Active Uplink**로 유지

   ![image-20250313160418798](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313160418798.png)

   * OK 클릭하여 변경사항 저장

4. **이 설정이 의미하는 것 (추측)**

   * **모든 네트워크 트래픽이 Uplink 3을 통해서만 전달됨**
     * 만약 Uplink 3에 문제가 생기거나 네트워크 단절이 발생하면, 대체 경로가 없어 네트워크가 끊길 가능성이 있음
     * Standby Uplink가 설정되지 않았기 때문이다.
   * **특정 물리 네트워크 인터페이스만 사용하기 위해**
     * 속도가 다를경우 성능 최적화를 위해 특정 uplink 만 사용하도록 한 것
   * **보안 및 네트워크 분리 목적**
     * Uplink 3이 특정 VLAN 또는 트래픽 셰이핑 설정이 Uplink 3에만 적용될 수 있음.





------

### **Task2: ESXi 호스트를 분산 스위치에 추가**

ESXi 호스트를 분산 스위치에 추가하고 물리적 네트워크 어댑터를 할당하는 과정이다.

1. **호스트 추가**

   * vds-Lab을 우클릭 → Add and Manage Hosts 선택
   * Add hosts 유지 후 NEXT 클릭
   * **호스트 선택:**
     * sa-esxi-01.vclass.local, sa-esxi-02.vclass.local 체크 후 NEXT

   ![image-20250313170428720](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313170428720.png)

2. **물리적 네트워크 어댑터 할당**

   * vmnic3를 Uplink 3에 할당
   * **확인 방법:**
     * vmnic3 옆 화살표 클릭하여 **할당할 호스트 목록** 확인
     * Assign uplink 드롭다운에서 Uplink 3 선택
     * NEXT 클릭

   ![image-20250313170454668](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313170454668.png)

3. **VMkernel 어댑터 관리**
   * VMkernel 어댑터 설정 유지 후 NEXT 클릭

4. **가상 머신 네트워크 마이그레이션**

   * Migrate virtual machine networking 체크 후 NEXT
   * Configure per network adapter에서 현재 가상 머신 수 기록
   * ASSIGN PORT GROUP 클릭 → pg-SA-Production 선택 후 ASSIGN
   * NEXT 클릭

   ![image-20250313170539300](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313170539300.png)

5. **설정 완료**
   * Ready to complete 페이지에서 설정 확인 후 FINISH 클릭
   * **Recent Tasks** 창에서 작업 완료 여부 확인



------

### **Task 3: 분산 스위치 설정 검증**



분산 스위치의 구성 및 설정이 정상적으로 적용되었는지 확인하는 과정이다.

1. **기본 설정 확인**
   * vds-Lab 선택 → Configure 탭 → Settings > Topology 선택
   * Uplink 3 확장하여 vmnic3가 sa-esxi-01.vclass.local, sa-esxi-02.vclass.local에 할당되었는지 확인

![image-20250313172018936](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313172018936.png)

2. **포트 그룹 설정 확인**
   * pg-SA-Production이 설정한 가상 머신 수와 일치하는지 확인
   * Configure 탭 → Settings > Properties에서 다음 값 확인
   * **업링크 수:** 4
   * **MTU 크기:** 1500 Bytes
   * **Discovery Protocol Type:** Cisco Discovery Protocol, 모드 Listen

![image-20250313172458570](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313172458570.png)

3. **포트 그룹 속성 확인**

   * pg-SA-Production 선택 → Configure → Properties 확인
   * **포트 바인딩:** Static binding
   * **포트 할당:** Elastic
   * **포트 개수:** 8

   ![image-20250313172205284](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313172205284.png)

4. **가상 머신 연결 확인**
   * VMs 탭에서 Linux02, Linux04, Linux06이 리스트에 있는지 확인
   * Ports 탭에서 pg-SA-Production이 Port Group 컬럼에 표시되는지 확인

![image-20250313172706201](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 8 Configuring vSphere  Distributed Switches//image-20250313172706201.png)



------

### 이 과정을 수행한 이유 (Task 2 ~ 3)

**ESXi 호스트의 네트워크를 분산 스위치(vDS)로 통합하여 중앙 집중식으로 관리하고, 네트워크 트래픽을 최적화하며, 가상 머신이 안정적인 네트워크 연결을 유지하도록 보장하기 위해서다.**

------

### 왜 ESXi 호스트를 분산 스위치(vDS)에 추가하는가?

1. **네트워크 관리 중앙화**
   * 개별 ESXi 호스트마다 로컬 가상 스위치를 설정하면 관리가 복잡해지므로, 분산 스위치를 사용하면 vCenter에서 네트워크 구성을 중앙에서 관리할 수 있음
   * 모든 ESXi 호스트가 동일한 vDS를 공유하므로 일관된 네트워크 정책을 유지 가능하다.
2. **가상 머신 네트워크 마이그레이션 용이**
   * 분산 스위치를 사용하면 호스트 간 네트워크 구성이 동일하므로 원활한 vMotion이 가능
3. **트래픽 로드 벨런싱 및 고가용성**
   * **Active/Standby 업링크를 설정하면 네트워크 장애 발생 시 자동으로 다른 업링크로 전환됨(Failover 가능)**
4. **ESXi 호스트 간 VM 통신 최적화**
   * VM 간 통신이 동일한 분산 스위치 내에서 이루어지면 데이터 최적 경로를 통해 이동



이 과정을 수행하는 이유는 **ESXi 호스트의 네트워크를 중앙 집중화하고, VM 간 원활한 통신을 보장하며, 트래픽 로드 밸런싱과 보안 정책을 일관되게 적용하기 위해서다.**

즉, **네트워크 안정성을 확보하고 관리 부담을 줄이며, 가상 머신 이동(vMotion) 및 네트워크 장애 대응을 원활하게 하기 위한 설정 과정**이다.

------



**결론**



이 과정은 **ESXi 환경에서 분산 스위치를 생성하고 ESXi 호스트 및 가상 머신 네트워크를 마이그레이션하는 방법**을 설명한다.

이를 통해 네트워크 관리를 단순화하고, 보다 안정적인 가상 네트워크 환경을 구축할 수 있다.