---
title: <h0>Module 3 Installing and Configuring ESXi</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-27-cisco]
tags: [Network, Cloud]








---

# Module 3: Installing and Configuring ESXi



------

## **ESXi의 중요성 (Importance of ESXi)**



**ESXi는 가상 머신(VM)을 생성하고 실행하는 핵심 가상화 플랫폼으로, 안정적이고 성능이 뛰어난 가상 환경을 제공한다.**

**1. ESXi의 주요 역할**



✅ **가상 머신 실행 환경 제공**

​	•	**하드웨어 자원을 가상화하여 여러 VM이 동시에 실행 가능**

​	•	**CPU, 메모리, 스토리지, 네트워크 리소스를 효율적으로 분배 및 관리**



✅ **안정적인 성능 유지**

​	•	하이퍼바이저 기반으로 **물리적 하드웨어 성능을 최적화**

​	•	VM 간 리소스 경합을 조정하여 **일관된 성능 제공**



✅ **보안 강화**

​	•	**격리된 환경에서 VM 실행**하여 보안 위협 최소화

​	•	**ESXi 보안 설정 및 업데이트 관리**로 안정적인 운영 가능



✅ **확장성과 유연성 제공**

​	•	물리 서버 추가 없이 **VM 확장을 통해 유연한 리소스 배포 가능**

​	•	클러스터링 및 로드 밸런싱을 활용하여 **워크로드 분산 및 가용성 증가**

**2. ESXi 환경의 올바른 구성의 중요성**



✔ **신뢰성(Reliability)** → 올바른 설정을 통해 **VM의 원활한 실행 및 장애 최소화**

✔ **보안(Security)** → 가상화 환경에서 **데이터 보호 및 접근 제어 강화**

✔ **성능(Performance)** → **리소스 최적화 및 부하 분산을 통해 시스템 성능 유지**

**3. 핵심 개념 요약**



✅ **ESXi는 가상 머신을 실행하는 VMware의 핵심 하이퍼바이저 플랫폼**

✅ **올바른 ESXi 구성은 신뢰성, 보안, 성능 최적화에 필수적**

✅ **기업 및 데이터센터에서 VM 환경을 효율적으로 운영하기 위해 필수적인 가상화 솔루션**

------

## About ESXi

![image-20250227144333307](/assets/cisco_post_img/2025-02-27-Installing and Configuring ESXi//image-20250227144333307.png)

**ESXi는 VMware vSphere의 일부로 제공되는 베어메탈 하이퍼바이저(Bare-metal Hypervisor)이며, 독립 실행형 서버로도 사용 가능하다.**

**1. ESXi 주요 특징**

✅ **고급 보안 기능 (High Security)**

​	•	**호스트 기반 방화벽(Host-based Firewall)**: 네트워크 접근을 제어하여 보안 강화

​	•	**메모리 보호(Memory Hardening)**: 메모리 보호 기법 적용으로 침입 방지

​	•	**커널 모듈 무결성(Kernel Module Integrity)**: 승인되지 않은 코드 실행 방지

​	•	**신뢰할 수 있는 플랫폼 모듈(TPM 2.0) 지원**: 보안 부팅 및 무결성 검증

​	•	**UEFI 보안 부팅(Secure Boot)**: 부팅 프로세스에서 서명된 코드만 실행

​	•	**코어 덤프 암호화(Encrypted Core Dumps)**: 시스템 충돌 데이터 보호

✅ **소형 디스크 공간 사용 (Small Disk Footprint)**

​	•	최소한의 스토리지 공간만 필요하여 리소스 절약

✅ **빠른 부팅(Quick Boot) 지원**

​	•	**패치 및 업그레이드 속도 향상**

✅ **유연한 설치 옵션**

​	•	**HDD, SAN LUN, SSD, SATADOM, 디스크 없는(Diskless) 호스트 등에 설치 가능**

**2. 핵심 개념 요약**

✅ **ESXi는 독립 실행형으로도 사용 가능하며, vSphere의 핵심 하이퍼바이저**

✅ **강력한 보안 기능(방화벽, TPM 2.0, UEFI Secure Boot 등) 제공**

✅ **빠른 부팅 및 소형 디스크 공간 사용으로 운영 효율성 향상**

✅ **다양한 스토리지 환경(HDD, SSD, SAN 등)에서 설치 가능**



이 설명은 **ESXi의 주요 기능과 장점을 요약한 것**으로, 보안 및 성능 최적화 측면에서의 강점을 강조하고 있다.

------

**ESXi 호스트 구성**

![image-20250227145421106](/assets/cisco_post_img/2025-02-27-Installing and Configuring ESXi//image-20250227145421106.png)

ESXi 설치 후 기본적으로 DHCP를 통해 IP 주소가 할당된다. 이를 통해 웹 브라우저에서 **vSphere Client**를 사용하여 ESXi 관리 인터페이스에 접속할 수 있다.



**DCUI(Direct Console User Interface)**는 로컬 콘솔에서 ESXi의 초기 설정을 관리하는 텍스트 기반 인터페이스다. 키보드만을 사용하여 조작하며, **F2 키를 눌러 시스템 설정(Customize System)**을 변경할 수 있다.



**DCUI에서 설정할 수 있는 주요 항목**

​	•	**네트워크 설정 변경**: IP 주소, 호스트 이름, DNS 구성

​	•	**루트 비밀번호 변경**

​	•	**호스트 유지보수 모드 설정**

​	•	**로그 및 시스템 상태 확인**



이러한 설정을 통해 **ESXi 호스트를 구성하고 관리할 수 있으며, 원격으로 접속하기 전에 필수적인 설정을 완료할 수 있다.**



------

## Configuring an ESXi Host: Management Network (**ESXi 호스트 관리 네트워크 구성**)



![image-20250227145605397](/assets/cisco_post_img/2025-02-27-Installing and Configuring ESXi//image-20250227145605397.png)



ESXi 호스트를 운영하기 전에 **관리 네트워크(Management Network)**를 설정해야 한다. 기본적으로 **DHCP를 통해 IP 주소가 자동 할당**되지만, 운영 환경에 맞게 수동 설정이 필요할 수 있다.

**1. DCUI(Direct Console User Interface)에서 관리 네트워크 설정**



**F2 키를 눌러 DCUI에 접속한 후 다음 설정을 변경할 수 있다.**

​	•	**네트워크 어댑터 선택**: 관리 네트워크를 사용할 NIC(vmnic) 지정

​	•	**VLAN ID 설정(선택 사항)**: 특정 VLAN에 호스트를 연결할 경우 사용

​	•	**IPv4 및 IPv6 주소 구성**:

​	•	정적 IP 주소 할당 (IP, 서브넷 마스크, 기본 게이트웨이 설정)

​	•	DHCP 사용 여부 결정

​	•	**호스트 이름 설정**: DNS 기반 관리 시 필수

​	•	**DNS 서버 및 서픽스(DNS Suffix) 설정**

**2. 추가적인 관리 네트워크 작업**

​	•	**VLAN 설정 변경 가능**

​	•	**IPv4 및 IPv6 주소 수정 가능**

​	•	**DNS 서버 및 도메인 서픽스 변경 가능**

​	•	**관리 네트워크 재시작(호스트 재부팅 없이 적용 가능)**

​	•	**네트워크 연결 테스트(핑 및 DNS 요청 사용)**

​	•	**네트워크 설정 초기화(잘못된 설정 복구 시 유용함)**

**3. 핵심 요약**



✅ ESXi 호스트는 기본적으로 DHCP로 IP를 할당받으며, 수동 설정 가능

✅ DCUI에서 네트워크 어댑터, IP 주소, VLAN, DNS 등을 설정 가능

✅ 네트워크 변경 후 관리 네트워크를 재시작하여 적용

✅ 연결 테스트 및 네트워크 설정 초기화 기능 제공



이러한 설정을 통해 **ESXi 호스트의 네트워크를 안정적으로 구성하고 관리할 수 있다.**

------

## Methods for Synchronizing Time

**시간 동기화 방법 (Methods for Synchronizing Time)**

ESXi 호스트의 정확한 시간 설정은 가상화 환경의 **로그, 인증, 및 네트워크 동기화**를 위해 필수적이다. VMware는 다음과 같은 **시간 동기화 옵션**을 제공한다.



**1. 시간 동기화 옵션**



✅ **수동 설정(Manual Configuration)**

​	•	사용자가 직접 ESXi 호스트의 시간을 설정

​	•	재부팅 시 시간 오류 가능성이 있음



✅ **NTP(Network Time Protocol)** – **밀리초(ms) 단위 정확도**

​	•	외부 NTP 서버와 동기화하여 일정한 시간 유지

​	•	대부분의 환경에서 권장되는 방식



✅ **PTP(Precision Time Protocol)** – **마이크로초(μs) 단위 정확도**

​	•	고정밀 시간 동기화가 필요한 경우 사용

​	•	주로 금융, 산업 자동화, 통신 인프라에서 활용

​	•	**NTP와 PTP는 동시에 실행할 수 없음**

------

## Configuring NTP

**ESXi 호스트의 NTP 클라이언트 설정**

![image-20250227152034578](/assets/cisco_post_img/2025-02-27-Installing and Configuring ESXi//image-20250227152034578.png)

✅ **ESXi 호스트는 NTP(Network Time Protocol) 클라이언트로 동작할 수 있으며, 인터넷 NTP 서버 또는 사내 NTP 서버와 동기화 가능**

✅ **NTP 클라이언트는 UDP 123번 포트를 사용하여 시간 데이터를 동기화**

✅ **정확한 시간 동기화를 통해 시스템 로그, 인증, 클러스터 운영 등의 일관성을 유지**

------

## **Configuring PTP(Precision Time Protocol) **

![image-20250227152331697](/assets/cisco_post_img/2025-02-27-Installing and Configuring ESXi//image-20250227152331697.png)

✅ **PTP는 하드웨어 기반 타임스탬프를 제공하여 네트워크 내 가상 머신과 호스트의 시간 동기화 정확도를 향상**

✅ **PTP 클라이언트는 UDP 포트 319, 320을 사용하여 시간 데이터를 전송**

✅ **PTP가 동작하지 않을 경우, NTP를 대체(fallback) 방식으로 설정 가능**

✅ **vSphere Client를 사용하여 PTP를 구성할 수 있으며, VMkernel 어댑터 또는 PCI 패스스루 방식을 선택 가능**

------

## Controlling Remote Access to an ESXi Host

![image-20250227161204607](/assets/cisco_post_img/2025-02-27-Installing and Configuring ESXi//image-20250227161204607.png)

✅ **vSphere Client 또는 Host Client를 사용하여 보안 설정을 사용자 지정 가능**

✅ **ESXi 방화벽(Firewall)은 기본적으로 활성화되어 있으며, 명시적으로 허용된 트래픽만 통과 가능**

✅ **NTP, SSH 등 주요 서비스는 관리자 권한을 가진 사용자만 관리 가능**

✅ **Lockdown 모드 활성화 시 원격 로그인 차단, DCUI 또는 vCenter를 통한 접속만 허용**



**좌측 상단 아이콘**

1. **host and cluster**
2. **vm and template**
3. **storage**
4. **network**

![image-20250227161418767](/assets/cisco_post_img/2025-02-27-Installing and Configuring ESXi//image-20250227161418767.png)

위 그림의 아이콘은 Vcenter server appliance 의 아이콘이다.

**sa : site - a**

------

## **Managing User Accounts: Best Practices (ESXi 사용자 계정 관리 모범 사례)**

✅ **루트(root) 계정 접근을 엄격히 제한**

✅ **강력한 비밀번호 사용(최소 8자 이상, 특수 문자, 대소문자, 숫자 포함) 및 주기적 변경**

✅ **ESXi 개별 관리 대신, vCenter Server를 통해 중앙 집중식 관리 권장**

✅ **로컬 사용자 계정 최소화**

✅ **ESXi 호스트를 도메인에 추가하고, 관리 사용자를 “ESX Admins” 도메인 그룹에 포함하여 루트 권한 부여**

**EX) https://sa-vcsa-01.vclass.local/ui**

------

### 기타

**LUN : Logical unit unber**

**vCenter Server : ESXi 관리**

**NSX Manager: NSX 관리**