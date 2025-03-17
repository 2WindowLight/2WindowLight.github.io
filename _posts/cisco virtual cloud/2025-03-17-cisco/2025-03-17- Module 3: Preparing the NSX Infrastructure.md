---
title: <h0> Module 3 Preparing the NSX Infrastructure</h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-17-cisco]
tags: [Network, Cloud]





---

#  Module 3: Preparing the NSX Infrastructure

## Lesson 1: Deploying the NSX Management Cluster

------

**NSX 인프라 배포 학습 목표**

1. **NSX 인프라 배포 워크플로우**
   * NSX 아키텍처 구성 요소(관리, 제어, 데이터 플레인) 이해
   * NSX Manager 배포 및 클러스터 구성
   * 전송 노드(ESXi, 베어메탈) 준비

2. **ESXi 호스트에 NSX Manager 배포**
   * vSphere Client 또는 OVF 템플릿을 사용해 배포
   * 네트워크, 스토리지, 관리자 계정 설정

3. **NSX Manager 및 관리 클러스터 상태 확인**
   * UI 또는 API로 상태 모니터링
   * CLI 명령어(get cluster status)로 검증

4. **인증서 가져오기 및 교체**
   * NSX Manager에서 인증서 가져오기 및 적용
   * 보안 및 신뢰성 강화



------

------



## **vSphere 환경에서 NSX 구현 절차**

![image-20250317110208455](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317110208455.png)

NSX를 vSphere 환경에 구축하기 위한 단계는 다음과 같다.



------



**1. NSX Manager 배포**

​	•	NSX의 핵심 관리 노드인 NSX Manager를 OVF 템플릿을 사용하여 배포한다.



**2. NSX UI 접속**

​	•	배포된 NSX Manager에 접근하여 웹 기반의 NSX UI를 실행한다.



**3. vCenter Server 등록**

​	•	NSX Manager에 vCenter Server를 등록하여 가상 인프라와의 통합을 수행한다.



**4. NSX 관리 클러스터 구성**

​	•	추가적인 NSX Manager 노드를 배포하여 고가용성을 위한 NSX 관리 클러스터를 구축한다.

​	•	NSX 관리 클러스터는 세 개의 NSX Manager 노드로 구성되며, 각 노드는 정책, 관리자 및 컨트롤러 역할을 수행할 수 있다.

**5. 전송 노드(Transport Node) 사전 설정**

​	•	전송 존(Transport Zone), IP 풀, 업링크 프로필을 구성하여 전송 노드를 준비한다.

​	•	전송 존(Transport Zone)은 논리적 네트워크를 구성하는 영역이며, 특정 ESXi 호스트 또는 NSX Edge 노드를 포함할 수 있다.

​	•	IP 풀은 터널 엔드포인트(TEP)에 할당할 IP 주소 범위를 설정하는 데 사용된다.

​	•	업링크 프로필을 통해 물리적 네트워크와의 연결을 위한 정책을 정의한다.



**6. ESXi 호스트를 전송 노드로 준비**

​	•	NSX 환경에서 데이터 전송을 담당하는 ESXi 호스트를 전송 노드로 구성한다.

​	•	이를 위해 NSX가 지원하는 vSphere Distributed Switch(VDS) 또는 NSX-T N-VDS를 사용하여 네트워크를 설정한다.

​	•	전송 노드가 활성화되면, 논리적 스위칭과 라우팅이 가능한 상태가 된다.



**7. NSX Edge 노드 배포**

​	•	NSX Edge 노드는 VM 또는 베어메탈 형태로 배포할 수 있다.

​	•	Edge 노드는 다음과 같은 주요 기능을 제공한다.

​	•	Tier-0 및 Tier-1 게이트웨이 구성

​	•	NAT, 방화벽 및 로드 밸런싱 서비스 제공

​	•	VPN 및 BGP 라우팅 기능 지원



**8. NSX Edge 클러스터 생성**

​	•	여러 개의 NSX Edge 노드를 그룹화하여 클러스터를 생성한다.

​	•	Edge 클러스터를 통해 게이트웨이, 로드 밸런서, 방화벽 등의 서비스를 고가용성(HA) 상태로 제공할 수 있다.

​	•	NSX-T에서는 Edge 클러스터를 이용해 물리 네트워크와 NSX 네트워크 간의 연결을 관리할 수 있다.



------



이 과정이 완료되면 NSX 환경이 구축되어 가상 머신, 컨테이너 및 물리적 서버에 대한 네트워크 및 보안 정책을 적용할 수 있다. NSX는 자동화된 네트워크 운영과 마이크로 세그멘테이션을 제공하여 보안성을 강화하고, 여러 클라우드 환경을 아우르는 멀티클라우드 네트워크를 지원한다.

------

------

## **NSX Manager 배포 시 고려사항**



NSX Manager를 배포하기 전에 다음과 같은 요소를 고려해야 한다.



------



1. **NSX Manager 배포 환경**
   * NSX Manager는 **vCenter Server에서 관리하는 ESXi 호스트** 또는 **독립 실행형 ESXi 호스트(Standalone ESXi Host)** 에 배포할 수 있다.
   * 그러나, vCenter Server에서 관리되지 않는 독립 실행형 ESXi 환경에서는 일부 자동화 기능이 제한될 수 있다.



2. **자동화된 NSX Manager 배포 지원 여부**
   * **vCenter Server에서 관리되는 ESXi 호스트**에서만 **NSX Manager의 추가 인스턴스를 자동 배포**할 수 있다.
   * 자동 배포는 **NSX UI 또는 API**를 통해 가능하다.
   * 독립 실행형 ESXi 호스트에서는 추가 인스턴스 자동 배포 기능을 사용할 수 없다.



3. **KVM 지원 중단**
   * 과거에는 KVM 하이퍼바이저에서도 NSX Manager를 지원했으나, 현재는 **KVM 환경에서의 NSX Manager 배포가 지원되지 않는다**.
   * 따라서, NSX Manager는 **ESXi 하이퍼바이저에서만 실행할 수 있는 가상 어플라이언스(VM Appliance) 형태로 제공**된다.



4. **NSX Manager의 역할 통합**
   * NSX Manager는 **Policy, Manager, Controller 역할을 단일 노드에서 수행**하는 통합된 구조를 갖는다.
   * 이전 버전에서는 개별적인 Manager 및 Controller 노드가 존재했지만, 최신 NSX 버전에서는 이 모든 기능이 하나의 NSX Manager 노드에 포함되어 있다.



5. **ESXi 하이퍼바이저 버전 호환성**
   * NSX Manager를 배포하기 전에 **지원되는 ESXi 버전**을 확인해야 한다.
   * VMware 공식 제품 호환성 매트릭스(Product Interoperability Matrix)를 참고하여 현재 사용 중인 ESXi 버전이 NSX와 호환되는지 검토해야 한다.

------



이러한 고려사항을 검토한 후, 환경에 적합한 NSX Manager 배포 방식을 결정하는 것이 중요하다. NSX Manager는 네트워크 가상화 및 보안 정책 관리를 위한 핵심 구성 요소이므로, 올바른 환경에서 배포하여 최적의 성능과 안정성을 확보해야 한다.

------

------

**NSX Manager 노드 크기 선택 (Sizing Guide)**



NSX Manager는 **소규모(Small), 중간(Medium), 대규모(Large) 크기의 폼 팩터(Form Factor)** 를 지원한다.

각 크기는 배포 환경의 요구사항에 따라 선택할 수 있으며, 사용 사례에 맞춰 적절한 크기를 결정해야 한다.



------



**1. NSX Manager 노드 크기별 리소스 요구사항**

| **Form Factor** | **CPU 개수** | **메모리 (GB)** | **디스크 공간 (GB)** |
| --------------- | ------------ | --------------- | -------------------- |
| **Small**       | 4            | 16              | 300                  |
| **Medium**      | 6            | 24              | 300                  |
| **Large**       | 12           | 48              | 300                  |





------



2. **NSX Manager 크기별 사용 사례**

   * **Small (소규모 배포)**
     * **사용 사례:** 실험실(Lab) 환경, 개념 증명(Proof-of-Concept, PoC)
     * **특징:** 최소한의 리소스 요구사항으로 배포 가능
     * **권장 환경:** 테스트 및 연구 목적

   * **Medium (중간 규모 배포)**
     * **사용 사례:** **최대 64개 호스트(ESXi Hosts)** 를 포함하는 환경
     * **특징:** 기업 환경에서 일반적으로 사용됨
     * **권장 환경:** 소규모 또는 중간 규모의 데이터센터

   * **Large (대규모 배포)**
     * **사용 사례:** **64개 이상의 ESXi 호스트를 운영하는 대규모 배포 환경**
     * **특징:** 확장성이 필요한 환경에서 사용
     * **권장 환경:** 엔터프라이즈급 환경, 멀티사이트(Multi-Site) 배포



------



**4. 결론**



NSX Manager는 환경에 맞는 크기를 선택하여 배포해야 한다.

테스트 및 PoC 환경에서는 **Small**, 일반적인 데이터센터에서는 **Medium**,

대규모 엔터프라이즈 환경에서는 **Large** 크기를 권장한다.



PoC: proof of concept (테스트 환경)

------

------

## Deploying NSX Manager from an OVF Template

![image-20250317111630610](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317111630610.png)

------

------

## **NSX CLI 접속 및 사용 방법**



NSX 환경을 관리하려면 **NSX CLI**를 활용하여 네트워크 구성을 조회하고 변경할 수 있다.

NSX CLI는 **NSX Manager 및 모든 트랜스포트 노드(Transport Nodes)에서 제공**되며, 환경 전반의 일관된 관리가 가능하다.



------



**1. NSX CLI 접속 방법**

1. **SSH를 사용한 접속**
   * NSX Manager에 **SSH 세션을 열고** 관리자(admin) 계정으로 로그인
   * PuTTY와 같은 터미널 프로그램을 사용하여 원격 접속 가능
   * **SSH 접속이 가능하려면 NSX Manager에서 SSH가 활성화되어 있어야 함**

2. **NSX Manager 가상 머신 콘솔을 통한 접속**
   * vSphere Client를 사용하여 **NSX Manager 가상 머신(Console)에서 직접 접속 가능**



------



**2. CLI 사용자 역할 및 권한**



NSX CLI에는 3가지 접근 레벨이 존재하며, **역할에 따라 명령어 실행 권한이 다름**

| **역할(Role)** | **설명**                             | **권한 수준**                   |
| -------------- | ------------------------------------ | ------------------------------- |
| **Root**       | 시스템 전반을 배포하고 설정 가능     | 가장 높은 권한 (기술 지원 필요) |
| **Admin**      | NSX 환경을 조회, 배포, 구성 가능     | 관리자 권한                     |
| **Audit**      | 설정 및 이벤트 조회 가능 (읽기 전용) | 가장 낮은 권한                  |

​	•	**Admin 모드 접속 방법**

​	•	SSH 또는 콘솔에서 admin 계정으로 로그인

​	•	**Audit 모드 접속 방법**

​	•	audit 계정으로 로그인하여 환경 조회



------



**3. 기본적인 NSX CLI 명령어**

| **명령어**            | **설명**                       |
| --------------------- | ------------------------------ |
| list                  | 사용 가능한 모든 명령어를 조회 |
| get services          | 활성화된 서비스 목록 확인      |
| get cluster status    | NSX 클러스터 상태 확인         |
| show configuration    | 현재 NSX 설정 확인             |
| clear logging-servers | 로깅 서버 목록 초기화          |
| del name-server <IP>  | 특정 네임서버 삭제             |





------

HOST(ESXi): esxcli

HOST(ESXi + NSX): esxcli + nsxcli

------

------

## Registering vCenter Server with NSX Manager

![image-20250317112433159](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317112433159.png)

------

------

## Verifying vCenter Server Registration with NSX Manager

![image-20250317112832072](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317112832072.png)

------

------

## Deploying Additional NSX Manager Instances (1)

![image-20250317112949996](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317112949996.png)

------

------

## Deploying Additional NSX Manager Instances (2)

![image-20250317113154801](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317113154801.png)

------

------

## Management Cluster Status: GUI 

![image-20250317113347694](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317113347694.png)

------

------

## Configuring the Virtual IP Address

![image-20250317113555444](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317113555444.png)

------

------

## Management Cluster Status: CLI (1)

![image-20250317120150061](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317120150061.png)

```bash
get cluster status
```

------

------

## Management Cluster Status: CLI (2)

![image-20250317120512762](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317120512762.png)

```bash
get services 
```

![image-20250317120540450](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317120540450.png)

```bash
get interfaces 
```



------

------

## Replacing Self-Signed Certificates (**NSX Manager에서 자체 서명된 인증서 교체**)

![image-20250317120842318](/assets/cisco_post_img/2025-03-17- Module 3: Preparing the NSX Infrastructure//image-20250317120842318.png)

NSX를 설치하면 기본적으로 **자체 서명된(Self-Signed) 인증서**가 사용된다.

보안 요구사항이 자체 서명된 인증서를 허용하지 않는 경우, **CA(Certificate Authority)에서 서명된 인증서로 교체**해야 한다.



------



**1. 자체 서명된 인증서 교체 필요성**

​	•	NSX Manager는 **웹 서비스의 신원을 인증하고, 웹 서버로 전송되는 정보를 암호화**하기 위해 서명된 인증서를 필요로 한다.

​	•	보안 강화를 위해 **자체 서명된 인증서 대신 CA 서명된 인증서 사용을 권장**한다.

​	•	각 NSX Manager 노드마다 **고유한 CA 서명된 인증서를 사용**해야 한다.



------



**2. 자체 서명된 인증서를 CA 서명된 인증서로 교체하는 과정**



**1) 인증서 서명 요청(CSR) 생성**

​	1.	**관리자(admin) 계정으로 NSX Manager에 로그인**

​	2.	**System > Settings > Certificates** 메뉴로 이동

​	3.	**CSRs 탭 클릭**

​	4.	**“GENERATE CSR” 클릭** → “Generate CSR” 선택

​	5.	**CSR 세부 정보를 입력** (공통 이름, 조직명, 키 크기 등)

​	6.	**“SAVE” 버튼 클릭**하여 CSR 생성



**2) 인증서 신청 및 가져오기**

​	1.	생성된 **CSR 파일을 CA(Certificate Authority)에 제출**하여 인증서를 발급받음

​	2.	발급된 **CA 서명된 인증서를 NSX Manager에 가져오기**



**3) CA 서명된 인증서 가져오기**

​	1.	**관리자(admin) 계정으로 NSX Manager에 로그인**

​	2.	**System > Settings > Certificates** 메뉴로 이동

​	3.	**“Import” > “Certificate” 선택**

​	4.	**발급받은 인증서 세부 정보 입력**

​	5.	**“SAVE” 버튼 클릭**하여 적용



------



**3. 결론**

​	•	**자체 서명된 인증서는 보안 위험이 존재하므로 CA 서명된 인증서로 교체해야 한다.**

​	•	**각 NSX Manager 노드별로 다른 인증서를 사용해야 하며, CSR을 생성하여 CA에 제출한 후 인증서를 가져와야 한다.**

​	•	**올바르게 설정된 CA 서명된 인증서는 NSX Manager의 신뢰성과 보안성을 강화하는 중요한 요소이다.**

------

------

