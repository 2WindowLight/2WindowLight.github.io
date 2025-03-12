---
title: <h0>VMware psc feature</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-12-cisco]
tags: [Network, Cloud]

---

## VMware psc feature

------

**PSC(Platform Services Controller)** - VMware vSphere 환경에서 SSO(Single Sign-On), 라이선스 관리, 인증서 관리 등의 공통 서비스를 제공하는 구성 요소이다.

vSphere 6.0부터 도입되었으며, vSphere 7.0부터는 vCenter Server와 통합되었다.

------

**1. PSC의 주요 기능**

**(1) SSO(Single Sign-On)**

* 하나의 **SSO 도메인**에서 여러 vCenter Server에 한 번의 로그인으로 접근 가능.
* LDAP, AD(Active Directory), vCenter 사용자 인증 지원.
* **vSphere Web Client 및 vSphere Client 인증** 관리.

------

**(2) 라이선스 관리**

* vCenter, ESXi, NSX 등 VMware 제품의 **라이선스를 중앙에서 관리**.
* 여러 vCenter Server 환경에서도 **통합된 라이선스 정책 적용 가능**.

------

**(3) 인증서 관리 (VMware Certificate Authority, VMCA)**

* vSphere 환경에서 인증서를 관리하는 역할.
* **ESXi 호스트 및 vCenter Server의 SSL 인증서 발급 및 갱신 지원**.
* 자체 서명된 인증서 또는 CA에서 발급한 인증서를 사용 가능.

------

**(4) 글로벌 권한 관리**

* 여러 vCenter Server 인스턴스 간 **공통 사용자 및 그룹 권한 관리**.
* vSphere Client에서 하나의 PSC를 통해 전체 인프라 관리 가능.

------

**(5) Enhanced Linked Mode (ELM)**

* **여러 vCenter Server를 하나의 관리 인터페이스에서 제어**.
* **단일 SSO 도메인 내에서 여러 vCenter Server의 리소스를 공유**.
* **vCenter 간 VM 마이그레이션(vMotion, Storage vMotion) 가능**.



------

**2. PSC 배포 방식**



**(1) 임베디드(Embedded) PSC (vSphere 6.7 이후 권장)**

* vCenter Server와 PSC가 **같은 VM에 통합됨**.
* 관리가 단순하고 별도의 PSC VM이 필요 없음.
* **vSphere 7.0부터는 PSC가 완전히 통합되어 별도 배포 불가능**.



**(2) 외부 PSC (External PSC, vSphere 6.5/6.7)**

* PSC를 vCenter Server와 **별도의 VM으로 분리**.
* 여러 vCenter Server에서 **공유 PSC**를 사용할 수 있어 확장성이 높음.
* vSphere 7.0부터는 지원 중단(Deprecated).



------



**3. vSphere 7.0부터 PSC 통합**

* **vSphere 7.0에서는 PSC가 제거되고 vCenter Server에 완전히 통합됨**.
* 이전 버전(6.5, 6.7)에서 **외부 PSC를 사용 중이라면 vSphere 7.0으로 업그레이드할 때 임베디드 PSC로 변환해야 함**.
* Linked Mode 기능은 여전히 지원되지만, **SSO 도메인을 공유하는 vCenter Server 간에서만 가능**.



------

**4. 정리**

| **기능**                       | **설명**                                                     |
| ------------------------------ | ------------------------------------------------------------ |
| **SSO(Single Sign-On)**        | 하나의 계정으로 여러 vCenter Server에 로그인 가능            |
| **라이선스 관리**              | 중앙에서 vSphere 라이선스를 관리                             |
| **VMCA(인증서 관리)**          | vSphere 환경의 SSL 인증서 발급 및 갱신                       |
| **글로벌 권한 관리**           | 여러 vCenter Server 간 사용자 및 그룹 권한 공유              |
| **Enhanced Linked Mode (ELM)** | 하나의 인터페이스에서 여러 vCenter 관리                      |
| **배포 방식**                  | vSphere 6.7까지는 **임베디드(Embedded) / 외부(External) PSC 지원**, vSphere 7.0부터는 **PSC가 vCenter Server에 통합됨** |

vSphere 7.0 이상을 사용하는 경우, **PSC는 더 이상 독립적으로 배포할 필요가 없으며, 기본적으로 vCenter Server에 포함되어 있다**.

