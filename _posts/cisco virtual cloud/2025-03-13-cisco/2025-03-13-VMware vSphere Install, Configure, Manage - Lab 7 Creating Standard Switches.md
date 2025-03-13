---
title: <h0>VMware vSphere Install, Configure, Manage - Lab 7 Creating Standard Switches</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-13-cisco]
tags: [Network, Cloud]











---



# **Lab 7: 표준 스위치 생성 (Creating Standard Switches)**



------

## **목표 및 작업**

이 실습의 목표는 **표준 스위치(Standard Switch, vSwitch)**와 **가상 머신 포트 그룹(Virtual Machine Port Group)**을 생성하고, 가상 머신을 해당 포트 그룹에 연결하는 것이다.

1. **표준 스위치 구성 확인**
2. **가상 머신 포트 그룹이 포함된 표준 스위치 생성**
3. **가상 머신을 생성한 가상 머신 포트 그룹에 연결**



------



## **TASK 1: 표준 스위치 구성 확인**



sa-esxi-01.vclass.local에서 **vSphere 표준 스위치 설정을 확인**하여 기본 스위치 구성을 확인한다.



### **1. vSphere Client에서 로그인**

​	•	**vSphere Client를 사용하여** sa-vcsa-01.vclass.local에 로그인한다.

​	•	**사용자명:** administrator@vsphere.local

​	•	**비밀번호:** VMware1!



2. ### **표준 스위치 정보 확인**

   1. **메인 메뉴에서** Inventory를 선택하고 Hosts and Clusters 아이콘을 클릭한다.

   2. **ICM-Datacenter > Lab Servers > sa-esxi-01.vclass.local** 을 확장하여 선택한다.

   3. **오른쪽 패널에서** Configure 탭을 클릭한다.

   4. **Networking 섹션에서** Virtual Switches를 선택한다.

   5. vSwitch0에 대한 정보를 검토한다.



3. ### **확인할 내용**

   * **Q1. vSwitch0에 연결된 물리 어댑터는?**
     * vmnic0, vmnic4, vmnic5, vmnic6

   * **Q2. vSwitch0에 연결된 포트 그룹은?**
     * IP Storage 1, IP Storage 2, Management Network, VM Network

   * **Q3. VM Network 포트 그룹에 연결된 가상 머신 및 템플릿은?**
     * Photon-Base, Photon-HW, Linux-Template, Linux-02, Linux-04, Linux-06



------



## **Task 2: 가상 머신 포트 그룹이 포함된 표준 스위치 생성**

**sa-esxi-01.vclass.local과 sa-esxi-02.vclass.local에 표준 스위치 및 가상 머신 포트 그룹을 추가한다.**



1. ### **sa-esxi-01.vclass.local에 Production 포트 그룹 추가**

   1. sa-esxi-01.vclass.local을 선택하고, 오른쪽 패널에서 **“ADD NETWORKING”** 클릭.

   2. Add Networking 마법사가 나타난다.

   3. **“Select Connection Type”** 페이지에서 Virtual Machine Port Group for a Standard Switch 선택 후 NEXT 클릭.

   4. **“Select Target Device”** 페이지에서 New Standard Switch 선택 후 NEXT 클릭.

   5. **“Create a Standard Switch”** 페이지에서 vmnic2를 선택하고 **MOVE DOWN**을 클릭하여 **Active Adapters**에 추가.

   ![image-20250313131035867](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 7 Creating Standard Switches//image-20250313131035867.png)

   1. NEXT 클릭.

   2. **“Connection Settings”** 페이지에서 **“Network Label”**에 "Production" 입력 후 NEXT 클릭.

   3. Ready to Complete 페이지에서 정보를 확인하고 FINISH 클릭.
   
   4. **Virtual Switches** 패널에서 vSwitch1을 확장하고, "Production" 포트 그룹과 vmnic2가 있는지 확인.

------

### production 포트 그룹이 포함된 표준 스위치를 생성한 이유

**네트워크 트래픽 분리 가능**

* Switch1 에는 vmnic3가 연결돼 있고, 이로 인해 표준 스위치를 여러 개 생성하면 네트워크를 논리적으로 분리가 가능하다.
* 예를 들어 Swtich0 은 Management Traffic, Swtich1 은 Production Traffic을 담당하도록 설정 할 수 있다.
* **이렇게 하면 트래픽이 한쪽에 집중되지 않고, 각 네트워크 유형에 따라 적절한 리소스를 할당할 수 있다.**
* vmnic3이 10Gbps 속도를 지원하는 물리 어댑터이므로, 고성능이 필요한 가상 머신 트래픽을 vSwitch1로 이동시킬 수도 있다.

**네트워크 확장성**

* 단일 vSwitch 에서 포트 그룹을 계속 추가하면 과부하가 걸릴 수 있다.
* vSwitch를 분리하여 네트워크 리소스를 효율적으로 관리하고, 더 많은 가상 머신과 네트워크 구성을 지원할 수 있다.





2. ### **sa-esxi-02.vclass.local에도 동일하게 설정**

   1. sa-esxi-02.vclass.local을 선택하고, **“ADD NETWORKING”** 클릭.
2. Add Networking 마법사 실행.
   3. **“Select Connection Type”** 페이지에서 Virtual Machine Port Group for a Standard Switch 선택 후 NEXT 클릭.
4. **“Select Target Device”** 페이지에서 New Standard Switch 선택 후 NEXT 클릭.
   5. **“Create a Standard Switch”** 페이지에서 vmnic2를 선택하고 **MOVE DOWN**을 클릭하여 **Active Adapters**에 추가.

   ![image-20250313131226273](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 7 Creating Standard Switches//image-20250313131226273.png)

   1. NEXT 클릭.
2. **“Connection Settings”** 페이지에서 **“Network Label”**에 "Production" 입력 후 NEXT 클릭.
   3. Ready to Complete 페이지에서 정보를 확인하고 FINISH 클릭.
4. **Virtual Switches** 패널에서 vSwitch1을 확장하고, "Production" 포트 그룹과 vmnic2가 있는지 확인.
   
![image-20250313131252820](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 7 Creating Standard Switches//image-20250313131252820.png)



------



## **Task 3: 가상 머신을 가상 머신 포트 그룹에 연결**



생성한 **Production 포트 그룹**에 가상 머신을 연결하여 vSwitch1과 vmnic2를 통해 네트워크 통신을 가능하게 한다.



1. ### **Linux-02 VM을 Production 포트 그룹에 연결**

   1. **VMs and Templates** 아이콘을 선택하고 **Lab VMs**을 확장.

   2. **Linux-02** 가상 머신 선택.

   3. 마우스 우클릭 후 Edit Settings 선택.

   4. **“Edit Settings”** 창에서 **Network adapter 1**을 찾는다.

   5. VM Network 옆 **아래 화살표(▼)** 클릭 후 Browse 선택.

   6. **“Select Network”** 창에서 "Production" 선택 후 OK 클릭.

   7. **Network adapter 1**을 확장하여 **“Connect At Power On”** 옵션이 체크되어 있는지 확인.

   8. OK 클릭하여 변경 사항 저장.



2. ### **변경 사항 확인**

   1. **오른쪽 패널에서** Summary 탭 클릭.

   2. **VM Hardware** 패널에서 "Production" 포트 그룹이 있는지 확인.
      * VM이 꺼져 있어서 "Disconnected" 상태일 수 있음.



3. ### **가상 머신 전원 켜기**

   1. **Linux-02** 선택 후 **마우스 우클릭** → Power > Power On 선택.

   2. **Summary** 탭에서 "LAUNCH WEB CONSOLE" 클릭.

   3. 부팅이 완료되면 localadmin 계정으로 자동 로그인됨.



4. ### 가상 머신 네트워크 확인

   1. **터미널 열기 (왼쪽 아이콘 클릭)**

      ![image-20250313131503472](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 7 Creating Standard Switches//image-20250313131503472.png)
   
   2. **IP 주소 확인**
      * ip a 명령어 입력 → **ens192 인터페이스의 IP 주소가 172.20.11.x 대역인지 확인**
   
      ![image-20250313131431960](/assets/cisco_post_img/2025-03-13-VMware vSphere Install, Configure, Manage - Lab 7 Creating Standard Switches//image-20250313131431960.png)
   
   3. **네트워크 연결 확인**
   
      * ping 172.20.11.2 입력하여 **기본 게이트웨이(Production 네트워크)** 와 연결되는지 확인
      * 정상적으로 응답이 오면 성공



5. **다른 VM에도 동일하게 적용**

   1. **Linux-04** VM에도 위와 같은 과정으로 "Production" 포트 그룹 연결.

   2. ping 테스트까지 정상적으로 완료.



6. **가상 머신 종료**

   1. **Linux-02 종료**

      * Linux-02 마우스 우클릭 → Power > Shut Down Guest OS

      * YES 클릭하여 종료 확인

   2. **Linux-04 종료**
      * Linux-04도 동일하게 종료



------



**요약**

1. **vSphere 표준 스위치 설정을 확인**
2. **Production 포트 그룹이 포함된 새로운 표준 스위치를 생성**
3. **Linux-02, Linux-04 가상 머신을 Production 포트 그룹에 연결**
4. **VM의 네트워크 설정을 확인하고, 정상적으로 연결되었는지 테스트**
5. **작업 완료 후 VM 종료**



------





