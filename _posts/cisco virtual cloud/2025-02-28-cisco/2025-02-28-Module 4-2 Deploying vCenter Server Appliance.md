---
title: <h0>Module 4-2 Deploying vCenter Server Appliance</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-28-cisco]
tags: [Network, Cloud]










---

# Deploying vCenter Server Appliance



------

**vCenter Server Appliance 배포 준비**

vCenter Server Appliance(VCSA)를 배포하기 전에 다음과 같은 필수 작업을 완료해야 합니다.

**✅ 사전 준비 사항**

1. **시스템 요구 사항 확인**

​	•	vCenter Server Appliance의 **하드웨어 및 소프트웨어 요구 사항**을 검토

2. **FQDN 및 IP 주소 확보**

​	•	**VCSA가 설치될 호스트의 정규화된 도메인 이름(FQDN) 또는 정적 IP 주소 확보**

​	•	**VCSA에 할당할 FQDN 및 IP 주소 준비**

3. **시간 동기화 확인**

​	•	**vSphere 네트워크 내 모든 VM의 날짜 및 시간이 동기화되어 있는지 확인**

------

## vCenter Server Appliance Native GUI Installer

![image-20250228121004635](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228121004635.png)

**vCenter Server Appliance GUI 설치 프로그램**



vCenter Server Appliance(VCSA) **네이티브 GUI 설치 프로그램**은 다음과 같은 기능을 제공합니다.



**✅ GUI 설치 프로그램의 주요 특징**

1. **운영체제 지원**

​	•	Windows, Linux, macOS에서 실행 가능

2. **대화형(Interactive) 설치 지원**

​	•	**직관적인 GUI를 통해 단계별로 VCSA 배포 가능**

3. **사전 검증 및 호환성 검사 수행**

​	•	**설치 중 환경 검증 및 사전 체크 수행**

​	•	호환성 문제 방지 및 안정적인 배포 보장

4. **설치 중 자동 오류 감지**

​	•	**잘못된 설정을 자동으로 감지하여 경고 제공**

------

## vCenter Server Appliance Installation

![image-20250228121248125](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228121248125.png)

**vCenter Server Appliance(VCSA) 설치 과정**

VCSA 설치는 **두 단계(Stage) 과정**으로 진행됩니다.

**✅ 설치 단계**

1. **Stage 1: OVF 배포 (Deployment)**

​	•	OVF(Open Virtualization Format) 템플릿을 사용하여 VCSA를 배포

​	•	가상 머신 생성 및 기본 네트워크 설정 수행

OVF(Open Virtualization Format) - OVF는 여러 파일을 패키지로 포함하는 개방형 표준이다. 예를 들어 .ovf, .wmdk, nvram 등이 있다.

또한 . OVF는 제품 및 플랫폼 간의 가상 장치 교환을 지원한다.

OVA() - OVA는 OVF 파일 패키지의 단일 파일 배포로 가상 시스템을 OVF 파일로 내보내는 경우 OVF 파일 및 가상 디스크 파일을 포함하는 디렉토리를 생성한다.



2. **Stage 2: 구성 (Configuration)**

​	•	vCenter SSO(Single Sign-On) 설정

​	•	네트워크 및 데이터베이스 구성

​	•	서비스 초기화 및 최종 설정 적용

**✅ 자동화 배포 (CLI + JSON)**

​	•	**JSON 템플릿을 활용한 자동화 설치 지원**

​	•	CLI 설치 프로그램을 사용하여 **Windows, Linux, macOS 환경에서 자동 배포 가능**

------

## vCenter Server Appliance Installation: Stage 1

​	![image-20250228121923781](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228121923781.png)

1️⃣ **UI 단계**

​	•	EULA 동의

​	•	대상 ESXi 호스트 또는 vCenter 연결

​	•	VCSA 이름 및 root 비밀번호 설정

​	•	컴퓨팅(cpu)/스토리지(disk) 크기 및 데이터스토어 선택

​	•	네트워크 설정

thin disk - overcommitment 가능

2️⃣ **배포 단계**

​	•	**OVF 템플릿을 사용하여 VCSA 배포**

​	•	**디스크 및 네트워크 구성 완료**

------

## vCenter Server Appliance Installation: Stage 2

![image-20250228122203845](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228122203845.png)

**구성 단계**

​	•	시간 동기화 설정 (ESXi 또는 NTP 서버 사용)

​	•	SSH 접근 설정 (기본적으로 비활성화)

​	•	**vCenter Single Sign-On(SSO) 설정** (새 도메인 생성 또는 기존 도메인 가입)

​	•	**CEIP(Customer Experience Improvement Program) 가입 여부 선택**

------

## Configuring vCenter Using the vSphere Client

![image-20250228124627571](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228124627571.png)

**vSphere Client를 사용한 vCenter 구성**

​	•	**구성 가능 항목**:

​	•	라이선스 설정

​	•	통계 수집

​	•	로그 관리

**접속 방법**:

​	1.	vSphere Client에서 vCenter 시스템 선택

​	2.	**Configure** 탭 클릭

​	3.	**Settings** 확장 후 설정 변경

------

## vCenter Management Interface

![image-20250228124526837](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228124526837.png)

**vCenter Management Interface 개요**

**기능**: vCenter 인스턴스의 설정 및 모니터링을 위한 HTML 기반 인터페이스

**주요 작업**:

​	•	리소스 사용량 모니터링

​	•	vCenter 서비스 상태 확인

​	•	백업 수행

​	•	네트워크 어댑터 추가

​	•	**접속 URL**: https://<FQDN_or_IP>:5480 (포트 5480 사용)



------

## Multi-homing the vCenter Server Appliance

![image-20250228124414336](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228124414336.png)

**vCenter Server Appliance Multi-Homing 개요**

​	•	**Multi-Homing**: 여러 개의 네트워크 인터페이스(NIC)를 사용하여 트래픽을 분산 관리하는 기능

​	•	**최대 4개의 NIC 지원**

​	•	**업그레이드, 백업, 복원 과정에서도 네트워크 설정 유지됨**

​	•	다양한 네트워크 분리를 위해 사용 가능 (예: 관리, VM 트래픽, 스토리지 등)

------

## Adding License Keys to vCenter

![image-20250228162013085](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228162013085.png)

------

## Assigning the License to the vSphere Component

![image-20250228162028499](/assets/cisco_post_img/2025-02-29-Module 4-2 Deploying vCenter Server Appliance//image-20250228162028499.png)
