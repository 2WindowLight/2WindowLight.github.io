---
title: <h0>hol-2530 Practice Module 6</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-12-cisco]
tags: [Network, Cloud]



---

# Module 6 - Advanced vMotion Techniques Intermediate

------

## **vCenter vMotion 개요**

vMotion은 VMware의 **라이브 마이그레이션(Live Migration)** 기술로, 

**가상 머신(VM)** 을 실행 중인 상태에서 **다른 ESXi 호스트** 또는 **vCenter Server 인스턴스**로 이동할 수 있도록 한다.

------

## **vMotion의 주요 기능**

1. **무중단 마이그레이션**
   * VM을 종료하지 않고 **동일한 데이터스토어 내에서 ESXi 호스트 간 이동** 가능
   * 애플리케이션과 사용자에 대한 영향을 최소화

2. **스토리지 vMotion**
   * VM을 실행한 상태에서 **다른 데이터스토어로 이동 가능**
   * 공유 스토리지를 사용하지 않는 경우에도 마이그레이션 가능

3. **크로스 vCenter vMotion**
   * 서로 다른 **vCenter Server** 인스턴스 간에 VM을 이동 가능
   * **향상된 연결 모드(Enhanced Linked Mode)** 를 사용하면 **단일 콘솔**에서 관리 가능
   * **고급 크로스 vCenter vMotion(Advanced Cross vCenter vMotion, XVM)** 을 활용하면 **vCenter Single Sign-On(SSO) 도메인이 다를 경우에도 마이그레이션 가능**

4. **네트워크 vMotion**
   * VM이 이동하는 동안 **네트워크 연결이 유지**됨
   * VM의 IP 주소가 변경되지 않아 **사용자는 이동을 인식하지 못함**

------

------

## **고급 vCenter vMotion 기술 요구 사항 정리**

------

### **1. 기본 요구 사항**

* vCenter Server 인스턴스에서 vMotion을 사용하려면 특정 시스템 요구 사항을 충족해야 합니다.

------

### **2. 크로스 vCenter vMotion 없이 향상된 연결 모드에서 마이그레이션**

* 두 vCenter Server 인스턴스는 **동일한 vCenter Single Sign-On(SSO) 도메인**에 있어야 한다.
* 향상된 링크 모드를 사용하면 소스 vCenter Server가 대상 vCenter Server에 인증할 수 있다.
* **vCenter Server 및 ESXi 호스트 버전**: 6.0 이상
* **시간 동기화**: 올바른 vCenter SSO 토큰 확인을 위해 두 vCenter Server 인스턴스의 시간이 동기화되어야 한다.
* **스토리지 공유**: 컴퓨팅 리소스만 마이그레이션하는 경우, 두 vCenter Server 인스턴스가 동일한 가상 머신 스토리지에 연결되어야 한다.

------

### 3. Advanced Cross vCenter vMotion을 사용할 경우 추가 요구 사항

* 서로 다른 **vCenter SSO 도메인 간 마이그레이션 가능**
* vCenter Server 인스턴스 버전: **7.0 업데이트 1c 이상**
* 전원이 꺼진 가상 머신 마이그레이션 시 **vSphere Standard 라이선스 필요**

------

### **4. 소스 vCenter Server에서 가상 머신 마이그레이션 시 고려 사항**

* 네트워크 라우팅, 스토리지 등 **사전 구성 요소가 검증되어 있어야 함**
* 실험 가능하나, **규정된 단계에서 벗어나면 예상치 못한 결과 발생 가능**
* 중첩된 랩 환경으로 인해 **마이그레이션 성능은 현재 활성 세션에 따라 달라질 수 있음**

------

------

## **vMotion 동작 방식**

1. **VM 상태 캡처**
   * vMotion은 **VM의 메모리 상태, 네트워크 연결, 디스크 상태**를 캡처
   * 실행 중인 애플리케이션이 영향을 받지 않도록 **CPU 상태 동기화**

2. **네트워크를 통한 데이터 전송**
   * VM의 메모리와 실행 상태를 **대상 ESXi 호스트로 전송**
   * 원본과 대상 호스트가 **공유 스토리지를 사용할 경우, 디스크 데이터는 이동하지 않음**
   * 스토리지 vMotion이 함께 사용되면 **디스크까지 다른 스토리지로 이동 가능**

3. **최종 동기화 및 전환(Switchover)**
   * 전송이 완료되면 **VM의 실행을 대상 호스트에서 시작**
   * 기존 소스 호스트의 VM 프로세스를 종료하고, **사용자는 다운타임 없이 계속 VM을 사용할 수 있음**

------

------

### Step 1

![image-20250312170637161](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312170637161.png)

------

### Step 2

![image-20250312171041752](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312171041752.png)

**정리**

| **옵션**                                     | **설명**                                                | **활용 사례**                                  |
| -------------------------------------------- | ------------------------------------------------------- | ---------------------------------------------- |
| **Change compute resource only**             | ESXi 호스트만 변경, 스토리지는 유지                     | VM의 로드 밸런싱, 호스트 유지보수              |
| **Change storage only**                      | 스토리지만 변경, ESXi 호스트는 유지                     | 데이터스토어 용량 부족 해결, 스토리지 유지보수 |
| **Change both compute resource and storage** | ESXi 호스트와 스토리지를 동시에 변경                    | 클러스터 변경, 새로운 인프라로 VM 이동         |
| **Cross vCenter Server export**              | vCenter 간 마이그레이션 (SSO 도메인 다를 경우에도 가능) | 데이터센터 간 VM 이동, 클라우드 마이그레이션   |

------

### Step 3

![image-20250312171317618](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312171317618.png)

![image-20250312171412266](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312171412266.png)

![image-20250312171435159](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312171435159.png)

------

### Step 4

![image-20250312171630427](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312171630427.png)

------

### Step 5

![image-20250312171822214](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312171822214.png)

------

### Step 6

![image-20250312171904694](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312171904694.png)

------

### Step 7

![image-20250312172011759](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312172011759.png)

------

### Step 8

![image-20250312172053683](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312172053683.png)

------

### Step 9

![image-20250312172636572](/assets/cisco_post_img/2025-03-12-hol-2530 Practice Module 6//image-20250312172636572.png)

**다른 호스트로 로그인 하여 acct-web01 VM이 마이그레이션 되어 다른 서버에 있는 것을 확인**