---
title: <h0>Module 4 Deploying and Configuring vCenter</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-28-cisco]
tags: [Network, Cloud, vShphere]








---

# Module 4 : Deploying and Configuring vCenter



------

## **vCenter의 중요성(Importance)**

✅ **vCenter는 여러 ESXi 호스트와 가상 머신을 중앙에서 관리하는 역할을 수행**

✅ **적절한 배포, 구성, 관리가 이루어지지 않으면 ESXi 호스트 및 VM 관리 효율성이 저하될 수 있음**

✅ **vCenter를 통해 확장성, 보안, 자동화된 워크플로우 및 리소스 최적화를 제공**

------

#  Centralized Management with vCenter



------

## About the vCenter Management Platform

![image-20250228105006363](/assets/cisco_post_img/2025-02-28-Module 4 Deploying and Configuring vCenter//image-20250228105006363.png)

**vCenter 관리 플랫폼 개요** 	

✅ **vCenter는 ESXi 호스트 및 가상 머신(VM)을 중앙에서 관리하는 핵심 플랫폼**

✅ **ESXi 호스트 및 VM의 작업을 제어하고 관리 기능을 제공**

✅ **Linux 기반 어플라이언스로 실행됨**

✅ **네트워크에 연결된 ESXi 호스트 및 VM을 하나의 통합 환경에서 관리 가능**

------

## About vCenter Server Appliance

**vCenter Server Appliance(VCSA) 개요**

✅ **vCenter Server Appliance(VCSA)는 Linux 기반의 사전 패키징된 VM으로, vCenter 및 관련 서비스를 실행하도록 최적화됨**

 **포함된 소프트웨어 구성**

​	•	**Photon OS** (경량 Linux 배포판)

​	•	**PostgreSQL 데이터베이스** (내장 DB)

​	•	**vCenter 서비스**

**배포 시 환경에 맞게 vCenter의 크기 및 데이터베이스 스토리지 크기 선택 가능**

 **ESXi 호스트 및 가상 환경을 중앙 집중식으로 관리하는 핵심 역할 수행**

------

## vCenter Services

![image-20250228105551388](/assets/cisco_post_img/2025-02-28-Module 4 Deploying and Configuring vCenter//image-20250228105551388.png)

**vCenter 서비스 구성 요소**

✅ **vCenter Server Appliance(VCSA) 배포 시 자동으로 포함되는 주요 서비스**

​	•	**vCenter Server** → ESXi 호스트 및 VM 중앙 관리

​	•	**vSphere Client** → 웹 기반 관리 인터페이스

​	•	**License Service** → 라이선스 관리 및 적용

​	•	**Content Library** → VM 템플릿 및 ISO 파일 중앙 저장소

​	•	**vSphere Lifecycle Manager** → 패치 및 업그레이드 관리

✅ **이 서비스들은 ESXi 환경의 효율적 운영과 관리 자동화를 지원**

------

## vCenter Architecture

![image-20250228105734454](/assets/cisco_post_img/2025-02-28-Module 4 Deploying and Configuring vCenter//image-20250228105734454.png)

**vCenter 아키텍처 개요**

vCenter는 **ESXi 호스트와 가상 머신(VM)을 중앙에서 관리**하며, 다양한 가상화 기능을 제공하는 핵심 관리 시스템이다.

**1️⃣ vCenter 아키텍처의 주요 구성 요소**

**📌 vSphere Client**

​	•	vCenter 및 ESXi 호스트에 연결하여 가상 인프라를 관리하는 **웹 기반 인터페이스**

​	•	가상 머신 생성, 호스트 설정, 스토리지 및 네트워크 관리 등의 기능 수행

​	•	vCenter가 ESXi 호스트를 관리하는 경우, **항상 vSphere Client를 통해 관리하는 것이 권장됨**

**📌 vCenter Database (데이터베이스)**

​	•	**vCenter에서 관리하는 모든 데이터 저장소 역할** 수행

​	•	주요 저장 데이터:

​	•	**인벤토리 항목** (ESXi 호스트, VM, 데이터스토어 등)

​	•	**보안 역할 및 사용자 권한 정보**

​	•	**성능 데이터 및 이벤트 로그**

​	•	**vCenter의 설정 및 정책**

**📌 Managed Hosts (관리 대상 호스트)**

​	•	vCenter에서 **관리하는 ESXi 호스트 및 가상 머신**

​	•	vCenter를 통해 VM 배포, 리소스 할당, 상태 모니터링 등을 중앙에서 수행

**2️⃣ vCenter의 역할 및 중요성**

✅ **ESXi 호스트 및 VM을 중앙에서 관리** → 개별 호스트 관리보다 효율성 향상

✅ **리소스 최적화** → vSphere DRS(분산 리소스 스케줄러) 및 HA(고가용성)와 연동

✅ **보안 및 역할 기반 관리** → 사용자 권한 및 정책을 중앙에서 설정

✅ **자동화 및 확장성 지원** → vSphere Lifecycle Manager를 통해 패치 및 업그레이드 관리

**3️⃣ vCenter 없이 ESXi 호스트를 개별 관리하는 경우 단점**



❌ 개별 ESXi 호스트를 각각 설정해야 하므로 **운영 관리 복잡도 증가**

❌ VM 이동(vMotion), 고가용성(HA), 분산 리소스(DRS) 같은 **중앙 집중식 기능 사용 불가**

❌ **보안 정책 및 사용자 관리가 분산됨** → 개별 호스트마다 설정 필요

**🔹결론**



vCenter는 **대규모 가상화 환경에서 필수적인 관리 솔루션**으로, ESXi 호스트 및 가상 머신을 통합 운영, 보안 강화, 자동화 및 확장성 지원 등의 기능을 제공하여 IT 인프라를 효율적으로 관리할 수 있도록 한다.

------

## About vCenter Single Sign-On

**vCenter Single Sign-On (SSO) 개요**

> [!IMPORTANT]
>
> **vCenter SSO**는 **보안 토큰 기반 인증 시스템**으로, 사용자가 **단일 로그인(Single Sign-On)으로 여러 vSphere 서비스에 접근 가능**하도록 지원한다.
>
> SSO는 **한 번 인증된 사용자에게 반복 로그인 없이 모든 암호 보호 리소스에 대한 접근 권한을 부여**하여 보안성과 사용자 경험을 동시에 향상시킨다.
>
> 조직은 **브라우저 기반 애플리케이션 액세스 증가에 따라 보안 및 사용자 경험 개선을 위한 액세스 관리 전략**을 우선시하고 있으며, SSO는 이를 효과적으로 지원하는 솔루션이다.



**1️⃣ vCenter SSO의 주요 기능**

✅ **보안 토큰 기반 인증** → 사용자 로그인 후 인증된 토큰을 통해 vSphere 구성 요소 간 보안 통신 제공

✅ **중앙 집중식 인증 관리** → 여러 ESXi 호스트 및 vCenter 인스턴스를 단일 계정으로 관리 가능

✅ **다양한 인증 방식 지원** → 내부 및 외부 ID 공급자를 활용하여 사용자 인증 수행

**2️⃣ vCenter의 인증 방식**



**📌 1. 기본 내장 ID 제공자 (Built-in Identity Providers)**

​	•	**vsphere.local 도메인**을 기본적으로 사용

​	•	LDAP 또는 LDAPS를 통해 **Active Directory(AD)** 연동 가능

​	•	OpenLDAP 및 OpenLDAPS를 통한 인증 지원

![The Ultimate Guide to Active Directory Best Practices - DNSstuff](https://www.dnsstuff.com/wp-content/uploads/2019/06/active-directory-best-practices-checklist-1024x536.png)

**Active Directory(AD) - 신원 확인을 위한 소스**



**📌 2. 외부 ID 제공자 (Federated Authentication)**

​	•	**Active Directory Federation Services (AD FS) 지원**

​	•	기존 Windows 통합 인증(IWA) 설정 가능하지만, **VMware는 LDAP 또는 AD FS 기반 연동을 권장**

**3️⃣ vCenter SSO의 장점**



🔹 **단일 로그인(SSO) 지원** → 한 번 로그인하면 여러 vSphere 서비스 접근 가능

🔹 **보안 강화** → 인증 정보가 암호화된 토큰으로 관리됨

🔹 **유연한 인증 방식 제공** → AD, LDAP, OpenLDAP, AD FS 등 다양한 인증 지원

🔹 **관리 용이성** → 중앙에서 사용자 인증 및 접근 제어 가능



**결론**

vCenter SSO는 **vSphere 환경의 보안과 인증을 중앙에서 관리**할 수 있도록 설계되었으며, **LDAP, AD FS 등의 다양한 인증 방식**을 통해 유연한 사용자 관리를 가능하게 한다.

특히 **AD FS 및 LDAP 기반의 인증 구성이 권장**되며, 단일 로그인 환경을 제공하여 IT 관리의 효율성을 높인다.

------

## vCenter Single Sign-On with Built-In Identity Provider

![image-20250228111937173](/assets/cisco_post_img/2025-02-28-Module 4 Deploying and Configuring vCenter//image-20250228111937173.png)

**vCenter Single Sign-On 로그인 흐름 (Built-In Identity Provider 사용)**

1. **사용자가 vSphere Client에 로그인**

​	•	사용자는 vSphere 환경에 접근하기 위해 vSphere Client에 로그인 요청을 보낸다.

2. **vCenter Single Sign-On이 사용자 인증 수행**

​	•	vCenter SSO는 사용자의 입력된 자격 증명(아이디, 비밀번호 등)을 **디렉터리 서비스(예: Active Directory, LDAP)** 와 비교하여 인증을 진행한다.

3. **SAML 토큰 발급 및 브라우저로 전송**

​	•	인증이 성공하면 vCenter SSO는 **SAML(Security Assertion Markup Language) 토큰을 생성**하여 사용자 브라우저로 반환한다.

4. **SAML 토큰을 vCenter에 제출하고 접근 권한 부여**

​	•	브라우저는 받은 SAML 토큰을 vCenter에 제출한다.

​	•	vCenter는 해당 토큰을 검증한 후, **사용자에게 접근 권한을 부여**하고 vSphere 환경에 접속할 수 있도록 한다.



**SSO에서 vCenter의 역할**

​	•	vCenter는 **ID 제공자(IdP) 역할**을 수행하며, 내장된 **Kerberos, CA(Certificate Authority), VMware Directory Services** 등을 활용해 인증 과정을 처리한다.

​	•	사용자는 한 번 로그인하면 동일한 SSO 환경 내에서 추가적인 로그인 없이 여러 서비스에 접근할 수 있다.

​	•	SSO를 통해 보안이 강화되며, 반복적인 로그인 없이 효율적인 계정 관리가 가능하다.



------

##  About Enhanced Linked Mode - Enhanced Linked Mode (ELM) 개요



**Enhanced Linked Mode(ELM)**를 사용하면 **vSphere Client**에 한 번 로그인하여 **여러 vCenter 인스턴스를 통합 관리**할 수 있다.



**주요 기능**

1. **최대 15개 vCenter 인스턴스 연결 가능**

​	•	하나의 **vCenter Single Sign-On(SSO) 도메인 내에서 최대 15개의 vCenter 인스턴스를 연결**하여 **단일 인터페이스에서 관리 가능**.

2. **중앙 집중식 관리**

​	•	여러 vCenter 인스턴스를 개별적으로 로그인할 필요 없이 **하나의 로그인만으로 모든 인스턴스를 관리**할 수 있음.

​	•	vSphere Client에서 **연결된 모든 vCenter 인스턴스의 인벤토리, 설정, 태스크를 확인하고 제어 가능**.

3. **배포 시점 선택 가능**

​	•	**vCenter Server Appliance(VCSA) 배포 중** 또는 **배포 후에도 Enhanced Linked Mode 그룹을 생성 가능**.

​	•	기존에 독립적으로 운영되던 vCenter 인스턴스를 **추후 ELM 그룹으로 연결 가능**.



**활용 예시**

​	•	대규모 환경에서 **여러 vCenter 인스턴스를 운영하는 기업**이 단일 관리 인터페이스를 통해 **효율적인 인프라 관리** 가능.

​	•	데이터 센터 간 **vCenter를 연동하여 중앙 집중식 제어**를 원할 때 유용.

​	•	**하나의 인증 정보(SSO)만으로 여러 vCenter에 로그인**할 수 있어 보안성과 사용자 편의성 증가.



1. **host and cluster**
2. **vm and template**
3. **storage**
4. **network**

------

## ESXi and vCenter Communication

ESXi and vCenter Communication- manage 네트워크와 통신

**ESXi와 vCenter 간의 통신 구조**

![image-20250228114853568](/assets/cisco_post_img/2025-02-28-Module 4 Deploying and Configuring vCenter//image-20250228114853568.png)

vSphere 환경에서 **ESXi 호스트**와 **vCenter**는 특정 에이전트와 데몬을 통해 상호작용하며, 이를 통해 **가상 머신(VM) 관리, 마이그레이션, 전원 제어 등의 작업이 수행**된다.

**1. vSphere Client를 통한 관리**

​	•	**vSphere Client**는 ESXi 및 vCenter를 관리하는 **주요 인터페이스**이다.

​	•	**vCenter가 실행 중일 경우**, vSphere Client는 vCenter를 통해 ESXi를 관리한다.

​	•	**vCenter가 다운되었을 경우**, vSphere Client 대신 **VMware Host Client**를 사용하여 ESXi에 직접 접속할 수 있다.

**2. vCenter와 ESXi의 통신 방식**



**(1) vCenter 에이전트(vpxa)와 데몬(hostd) 역할**

​	•	vCenter는 **vpxa (vCenter Provisioning X Agent)**를 통해 ESXi 호스트와 연결됨.

​	•	**vpxa 프로세스는 ESXi에 자동 설치되며**, vCenter에서 ESXi를 인벤토리에 추가하면 활성화됨.

​	•	vCenter의 **vpxd 서비스**는 **vpxa를 통해 ESXi의 hostd 데몬과 통신**하여 명령을 전달함.



**(2) hostd (ESXi 호스트 데몬) 역할**

​	•	**hostd는 ESXi에서 직접 실행되는 핵심 관리 프로세스**이며, **VM 상태, 저장소 볼륨, 네트워크 인터페이스 등의 정보를 관리**함.

​	•	**vCenter를 거치지 않고 ESXi에 직접 연결하는 경우**, hostd가 직접 클라이언트 요청을 처리함.

​	•	**VM 생성, 마이그레이션, 전원 관리 등의 명령은 vpxa를 통해 hostd에 전달**됨.

**3. vCenter를 통한 명령 수행 흐름**

​	1.	사용자가 **vSphere Client에 로그인**하여 VM 생성 또는 변경 요청 수행.

​	2.	vSphere Client는 **vCenter Server(vpxd)에 명령을 전달**.

​	3.	vpxd는 **vpxa(ESXi에 설치된 vCenter 에이전트)로 명령을 전송**.

​	4.	vpxa는 **hostd(ESXi에서 실행 중인 핵심 데몬)로 명령을 전달**.

​	5.	hostd가 **ESXi에서 명령을 실행**하고, 작업 결과를 vpxa → vpxd → vSphere Client 순으로 전달.

​	6.	실행된 작업 내역이 **vCenter 데이터베이스에 업데이트됨**.

**4. vCenter 없이 ESXi 직접 관리 시**

​	•	**vCenter가 비활성화되었을 경우**, vSphere Client를 통한 vCenter 연결이 불가능함.

​	•	이 경우 **VMware Host Client**를 이용해 ESXi에 **직접 연결**하여 관리 가능.

​	•	**Host Client는 ESXi의 hostd와 직접 통신**하며, 이때 **vCenter 데이터베이스에는 기록이 남지 않음**.

**5. 주요 활용 사례**

​	•	**일반적인 ESXi 관리**

→ vSphere Client를 이용하여 vCenter를 통해 ESXi 관리.

​	•	**vCenter 장애 발생 시**

→ VMware Host Client로 ESXi에 직접 연결하여 긴급 작업 수행.

​	•	**vCenter를 통해 다수의 ESXi를 중앙 관리**

→ vCenter와 vpxa, hostd를 활용한 안정적인 VM 및 호스트 관리 가능.

**6. 핵심 요약**



✅ **vCenter(vpxd) ↔ vpxa ↔ hostd ↔ ESXi** 구조로 명령 수행

✅ **vSphere Client → vCenter → ESXi 통신 가능** (vCenter 활성 상태)

✅ **VMware Host Client → hostd로 직접 통신 가능** (vCenter 비활성 상태)

✅ **대부분의 VM 관리 작업은 vpxa를 통해 수행됨**

✅ **vCenter가 다운되면 hostd를 통해 직접 ESXi 관리 가능하지만, DB는 업데이트되지 않음**





------

## vCenter Scalability

**vCenter 8.0 확장성(Scalability)**

vCenter 8.0은 대규모 가상 환경을 효과적으로 관리할 수 있도록 설계되었으며, 다음과 같은 확장성을 지원한다.

| **Metric**                                | **vCenter 8.0** |
| ----------------------------------------- | --------------- |
| **vCenter당 관리 가능한 ESXi 호스트 수**  | 2,500           |
| **vCenter당 실행 중인(VM 전원 ON) VM 수** | 40,000          |
| **vCenter당 등록 가능한 VM 수**           | 45,000          |
| **클러스터당 최대 ESXi 호스트 수**        | 96              |
| **클러스터당 최대 VM 수**                 | 8,000           |

**🔹 요약**

​	•	**vCenter 8.0은 최대 2,500개의 ESXi 호스트 및 40,000개의 실행 중인 VM을 관리 가능**

​	•	**클러스터당 최대 96개 호스트, 8,000개 VM 지원**

​	•	**대규모 데이터센터 및 엔터프라이즈 환경에서 사용 가능**

------

### 기타

**vCenter - ESXI가 설치된 호스트를 관리하는 플랫폼**

**AAA**

**authentication : who**

**authorization: what**

**account: how**