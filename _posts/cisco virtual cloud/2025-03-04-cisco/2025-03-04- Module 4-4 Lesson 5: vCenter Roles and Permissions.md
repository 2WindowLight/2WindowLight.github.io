---
title: <h0>Module 4-4 Lesson 5 vCenter Roles and Permissions</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-04-cisco]
tags: [Network, Cloud]











---

# 

# Lesson 5: vCenter Roles and Permissions



------

## About vCenter Permissions (**vCenter 권한 시스템에 대한 상세 설명**)

![image-20250304112923627](/assets/cisco_post_img/2025-03-04-temp//image-20250304112923627.png)





vCenter의 권한 시스템은 사용자가 vSphere 환경 내에서 특정 작업을 수행할 수 있도록 제어하는 액세스 관리 기능입니다. 이를 통해 관리자는 **사용자가 어떤 객체에서 어떤 작업을 수행할 수 있는지 정의**할 수 있습니다.

**1. Privilege (권한)**

권한(Privilege)은 **사용자가 수행할 수 있는 개별 작업**을 의미합니다. vCenter에는 다양한 권한이 존재하며, 각 권한은 특정 작업을 수행할 수 있도록 허용합니다.

​	예시:

​	•	**Virtual Machine > Power On** → 가상 머신 전원 켜기

​	•	**Datastore > Browse** → 데이터스토어 탐색

​	•	**Network > Assign** → 네트워크 할당

​	•	**Host > Maintenance Mode** → 호스트 유지보수 모드로 변경



권한은 개별적으로 직접 부여되지 않으며, **역할(Role)에 포함되어 사용자에게 할당**됩니다.

------

**2. Role (역할)**

역할(Role)은 **여러 개의 권한(Privileges)의 집합**입니다. vCenter에서는 역할을 생성하고 필요한 권한을 추가하여 **사용자 또는 그룹에게 쉽게 할당**할 수 있도록 합니다.

​	•	**기본 제공 역할**

​	•	**Administrator** → 모든 권한 보유 (최고 관리자)

​	•	**Read-Only** → 보기 권한만 있음 (수정 불가)

​	•	**Virtual Machine User** → VM 관련 작업만 가능

​	•	**No Access** → 객체에 대한 접근 차단

​	•	**사용자 정의 역할**

​	•	관리자는 필요에 따라 특정 권한을 가진 **사용자 정의 역할**을 만들 수 있습니다.

​	•	예를 들어, “스토리지 관리자(Storage Admin)” 역할을 만들고, 데이터스토어 관리와 관련된 권한만 부여할 수 있습니다.

------

**3. Object (객체)**

객체(Object)는 **사용자가 작업을 수행할 대상**입니다. vSphere 환경에서는 여러 가지 유형의 객체가 존재합니다.

​	•	가상 머신 (VM)

​	•	ESXi 호스트

​	•	데이터스토어 (Datastore)

​	•	네트워크 (Network)

​	•	vCenter 자체

각 객체에 대해 **다른 권한을 할당**할 수 있습니다. 예를 들어, 사용자 A는 **VM에 대한 관리 권한을 가질 수 있지만, 데이터스토어는 수정할 수 없도록 설정**할 수 있습니다.

------

**4. User or Group (사용자 또는 그룹)**

vCenter에서는 개별 사용자(User) 또는 그룹(Group)에게 권한을 부여할 수 있습니다.

​	•	**사용자(User):** 개별 계정 (예: admin, vm_user1)

​	•	**그룹(Group):** 여러 사용자를 포함하는 그룹 (예: IT_팀, 개발자 그룹)



Active Directory(AD)와 연동할 경우, **AD 사용자 또는 그룹을 vCenter에 추가하여 사용 권한을 관리**할 수도 있습니다.

------

**5. Permission (권한 부여)**



권한(Permission)은 특정 객체(Object)에 대해 특정 사용자(User) 또는 그룹(Group)에게 **특정 역할(Role)**을 부여하는 것을 의미합니다.

**권한 할당 과정**

​	1.	**객체 선택:** VM, 호스트, 데이터스토어 등 권한을 적용할 객체 선택

​	2.	**사용자 또는 그룹 선택:** 권한을 부여할 사용자 또는 그룹 선택

​	3.	**역할(Role) 지정:** 해당 사용자 또는 그룹에게 적절한 역할(Role)을 할당

​	4.	**권한 적용:** 권한이 즉시 적용되며, 사용자는 해당 역할(Role)의 범위 내에서만 작업 가능



------

**예제**

| **객체 (Object)** | **사용자 또는 그룹** | **역할 (Role)**         |
| ----------------- | -------------------- | ----------------------- |
| VM1               | dev_user1            | Virtual Machine User    |
| Datastore1        | storage_admins       | Datastore Administrator |
| Network1          | network_team         | Network Administrator   |

**vCenter 권한 시스템 작동 방식**

​	1.	사용자가 vSphere Client에 로그인

​	2.	vCenter는 해당 사용자의 권한 확인 (AD 또는 로컬 인증)

​	3.	사용자가 특정 객체에 대해 작업 요청

​	4.	vCenter는 해당 객체에서 사용자의 역할(Role)을 확인

​	5.	사용자가 가진 권한(Privileges) 내에서만 작업 수행 가능

**결론**

​	•	vCenter의 **권한 시스템은 Privilege, Role, Object, User/Group, Permission으로 구성**됩니다.

​	•	**역할(Role)**을 생성하여 **여러 개의 권한(Privileges)**을 묶어서 관리할 수 있습니다.

​	•	사용자는 개별 객체(Object)에 대해 특정 역할(Role)을 할당받아야 해당 객체에서 작업을 수행할 수 있습니다.

​	•	AD와 연동하면 조직의 계정 시스템과 vCenter 권한을 통합하여 더 효율적으로 관리할 수 있습니다.



이러한 방식으로 **vCenter는 보안과 관리 편의성을 극대화하면서도, 세밀한 권한 제어를 제공**할 수 있습니다.

------

------

## About Roles

![image-20250304113201452](/assets/cisco_post_img/2025-03-04-temp//image-20250304113201452.png)

**vCenter 역할(Role) 개요**



vCenter의 역할(Role)은 **여러 개의 권한(Privilege)을 그룹화한 설정**으로, 사용자가 특정 작업을 수행할 수 있도록 관리하는 중요한 개념입니다. 역할을 사용하면 **각 사용자가 수행할 수 있는 작업을 효과적으로 제한**하고 관리할 수 있습니다.

------

**1. Privilege(권한)와 Role(역할)의 차이**

​	•	**Privilege(권한):** 특정 작업을 수행할 수 있는 개별적인 허가

​	•	예: **가상 머신 전원 켜기(VM Power On), 데이터스토어 읽기(Datastore Browse)**

​	•	**Role(역할):** 여러 개의 권한을 그룹으로 묶어서 사용자에게 할당

​	•	예: **가상 머신 사용자(Virtual Machine User)** 역할 → VM 관련 권한만 포함

------

**2. 기본 제공(System) 역할**

vCenter는 몇 가지 기본 역할을 제공하며, 이러한 **시스템 역할(System Roles)**은 변경할 수 없습니다.

| **역할(Role)**                    | **설명**                                              |
| --------------------------------- | ----------------------------------------------------- |
| **Administrator**                 | 모든 객체에서 모든 작업을 수행할 수 있는 최고 관리자  |
| **Read-only**                     | 객체의 상태를 볼 수만 있고 변경할 수 없음             |
| **No access**                     | 객체를 볼 수도 없고 변경할 수도 없음                  |
| **No cryptography administrator** | 관리자 권한과 동일하나, **암호화 관련 권한이 제외**됨 |

​	❗ **기본 제공 역할은 수정할 수 없음**

------

**3. 샘플(Sample) 역할**



VMware에서는 사용자가 쉽게 활용할 수 있도록 **샘플 역할(Sample Roles)**을 제공합니다.

이 역할들은 직접 수정할 수 없지만, **복제(Clone)**하여 사용자 정의 역할(Custom Role)을 생성할 수 있습니다.

| **역할(Role)**                    | **설명**                                    |
| --------------------------------- | ------------------------------------------- |
| **Content Library Administrator** | 콘텐츠 라이브러리 관리 가능                 |
| **Datastore Consumer**            | 데이터스토어 사용 권한 보유, 생성/삭제 불가 |
| **Network Administrator**         | 네트워크 구성 및 관리 가능                  |

​	✅ **샘플 역할은 복제(Clone) 후 수정 가능**

------

**4. 사용자 정의 역할(Custom Role)**



관리자는 **기존 샘플 역할을 복제(Clone)하거나, 새로운 역할을 생성**하여 사용자의 필요에 맞게 권한을 조정할 수 있습니다.

**사용자 정의 역할 생성 예제**

1. **스토리지 관리자(Storage Admin)**

​	•	데이터스토어 생성/삭제 가능

​	•	데이터스토어 읽기/쓰기 권한 포함

2. **VM 관리자(VM Admin)**

​	•	가상 머신 생성/삭제 가능

​	•	VM 스냅샷 생성/삭제 가능

​	•	네트워크 설정 변경 불가

3. **보안 관리자(Security Admin)**

​	•	암호화 관련 작업 수행 가능

​	•	네트워크 방화벽 규칙 변경 가능

------

**5. 역할(Role)과 사용자(User) 또는 그룹(Group) 연결**



역할(Role)은 개별 사용자(User) 또는 그룹(Group)에 할당됩니다.

​	•	개별 사용자: admin_user1, dev_user1

​	•	그룹: IT_Admins, VM_Users



**예제**

| **객체 (Object)** | **사용자 또는 그룹** | **역할 (Role)**      |
| ----------------- | -------------------- | -------------------- |
| ESXi-01           | IT_Admins            | Administrator        |
| Datastore1        | storage_admins       | Storage Admin        |
| VM1               | dev_user1            | Virtual Machine User |

------

**6. 역할(Role)의 작동 방식**

​	1.	사용자가 vSphere Client에 로그인

​	2.	vCenter가 사용자의 역할(Role) 확인

​	3.	사용자가 특정 작업 요청 (예: VM 전원 켜기)

​	4.	vCenter가 해당 객체(Object)에서 사용자의 권한(Privilege)을 확인

​	5.	역할(Role) 내의 권한이 있다면 작업 수행, 없다면 접근 차단

------

**7. 역할(Role)과 권한(Privilege)의 특징**



✅ **역할은 계층적 구조를 가지지 않음**

​	•	역할 간에 상속(Hierarchy)이 존재하지 않음

​	•	**각 역할은 독립적으로 존재하며, 상속되거나 자동으로 포함되지 않음**

​	•	예: “Read-only” 역할을 부여했다고 해서 자동으로 “No access” 역할과 병합되지 않음



✅ **사용자/그룹은 여러 역할을 가질 수 있음**

​	•	한 사용자는 여러 역할을 가질 수 있으며, 여러 객체에 대해 서로 다른 역할을 가질 수 있음

​	•	예:

​	•	같은 사용자가 VM에서는 **Virtual Machine User** 역할을 가지고

​	•	데이터스토어에서는 **Read-only** 역할을 가질 수 있음



✅ **세부적인 권한 조정 가능**

​	•	역할을 세분화하여 보안 및 운영을 강화할 수 있음

​	•	예: VM 운영팀은 가상 머신 관련 권한만 부여하고, 네트워크 팀은 네트워크 설정 변경만 가능하도록 구성

------

**결론**

​	•	**Privilege(권한):** 특정 작업을 수행할 수 있는 개별 권한

​	•	**Role(역할):** 여러 권한을 묶어서 관리하는 단위

​	•	**기본 역할(System Role):** 수정 불가 (Administrator, Read-only 등)

​	•	**샘플 역할(Sample Role):** 복제 후 수정 가능

​	•	**사용자 정의 역할(Custom Role):** 필요에 따라 새로운 역할 생성 가능

​	•	**역할은 사용자(User) 또는 그룹(Group)에 할당되며, 특정 객체(Object)에서 적용됨**

​	•	**역할 간 계층 구조 없음, 한 사용자가 여러 역할을 가질 수 있음**



이러한 방식으로 vCenter에서는 **세밀한 접근 제어 및 관리 효율성을 극대화**할 수 있습니다.

------

------

## About Objects( **vSphere 객체[Objects] 개요**)

![image-20250304113644830](/assets/cisco_post_img/2025-03-04-temp//image-20250304113644830.png)





vSphere 환경에서 **객체(Objects)** 는 특정 작업이 수행되는 엔터티를 의미합니다. 객체에는 데이터 센터(Data Center), 폴더(Folders), 클러스터(Clusters), 호스트(Hosts), 데이터스토어(Datastores), 네트워크(Networks), 가상 머신(VMs) 등이 포함됩니다.



각 객체는 **사용자 또는 그룹과 연결된 역할(Role)을 통해 접근 권한이 설정**되며, 모든 객체는 **“Permissions” (권한) 탭**을 가지고 있습니다.

------

**1. 주요 객체 유형**

| **객체 유형**                 | **설명**                                      |
| ----------------------------- | --------------------------------------------- |
| **데이터 센터 (Data Center)** | 가상 환경을 논리적으로 분리하는 컨테이너 역할 |
| **폴더 (Folders)**            | 객체들을 조직화하여 관리하는 논리적 그룹      |
| **클러스터 (Clusters)**       | 여러 ESXi 호스트를 하나의 리소스 풀로 결합    |
| **호스트 (Hosts)**            | ESXi 하이퍼바이저가 설치된 물리적 서버        |
| **데이터스토어 (Datastores)** | 가상 머신 및 파일을 저장하는 스토리지 리소스  |
| **네트워크 (Networks)**       | 가상 네트워크 환경(VM 네트워크, vSwitch 등)   |
| **가상 머신 (VMs)**           | 가상 환경에서 실행되는 게스트 운영 체제       |

------

**2. 객체별 권한 설정**



각 객체는 **사용자 또는 그룹과 역할(Role)을 연결하여 권한을 부여**할 수 있습니다.

예를 들어, **특정 사용자가 VM을 생성할 수 있지만, 네트워크 설정을 변경할 수 없도록 제한**하는 것이 가능합니다.



**🔹 객체의 Permissions(권한) 탭**



**Permissions(권한) 탭**에서는 다음 정보를 확인할 수 있습니다.

​	1.	**User/Group**: 객체에 권한이 부여된 사용자 또는 그룹

​	2.	**Role**: 해당 사용자/그룹이 가진 역할 (예: Administrator, Read-only 등)

​	3.	**Defined In**: 권한이 설정된 위치 (예: Global Permission 또는 특정 객체)



**예제**

| **객체 (Object)**       | **사용자 또는 그룹**        | **역할 (Role)**      | **정의된 위치 (Defined In)** |
| ----------------------- | --------------------------- | -------------------- | ---------------------------- |
| sa-esxi-01.vclass.local | VSPHERE.LOCAL\Administrator | Administrator        | sa-vcsa-02.vclass.local      |
| Photon-01 (VM)          | VSPHERE.LOCAL\DevTeam       | Virtual Machine User | Global Permission            |
| SA-Datacenter           | VSPHERE.LOCAL\IT_Admins     | Datastore Consumer   | Global Permission            |

------

**3. vSphere 객체 및 권한 관리 예제**



**(1) 데이터 센터 객체의 역할 부여**

​	•	IT 관리자는 SA-Datacenter의 **Administrator** 역할을 가짐

​	•	개발자는 SA-Datacenter의 **Read-only** 역할만 가짐



**(2) 특정 사용자에게 VM 관련 권한 부여**

​	•	dev_user1은 Photon-01 (VM)에서 **Virtual Machine User** 역할을 가짐

(가상 머신 전원 켜기/끄기 가능, 네트워크 설정 변경 불가)



**(3) 특정 네트워크에 대한 관리자 권한 부여**

​	•	network_admin1은 VM Network에서 **Network Administrator** 역할을 가짐

(가상 네트워크 설정 변경 가능)

------

**4. vSphere 권한 관리 Best Practice**



✅ **역할(Role) 기반 접근 제어 사용**

​	•	개별 사용자에게 권한을 직접 할당하는 대신, **그룹을 사용하여 역할을 부여**하는 것이 좋음

​	•	예: IT_Admins 그룹에 **Datastore Consumer** 역할을 부여하면, 그룹에 속한 모든 사용자가 동일한 권한을 가짐



✅ **최소 권한 원칙 적용**

​	•	사용자에게 **필요한 최소한의 권한**만 부여하여 보안 강화

​	•	예: 개발자는 VM을 생성하거나 삭제할 수 없고, **오직 실행(Start) 및 정지(Stop)만 가능**하도록 제한



✅ **글로벌 권한(Global Permission)과 개별 객체 권한 분리**

​	•	**Global Permission**은 전체 vSphere 환경에 영향을 미치므로, **최소한의 사용자만 포함**

​	•	특정 데이터 센터 또는 VM에 대한 권한은 개별 객체에서만 설정



✅ **주기적으로 권한 감사(Audit) 수행**

​	•	불필요한 권한이 부여된 사용자가 있는지 확인하고 정기적으로 제거

------

**결론**

​	•	vSphere 객체(Objects)는 데이터 센터, 클러스터, 호스트, 데이터스토어, VM 등 다양한 유형이 존재

​	•	각 객체는 **Permissions(권한) 탭**을 통해 **사용자 또는 그룹과 역할(Role)을 연결하여 접근 제어**

​	•	**최소 권한 원칙**을 적용하고, **그룹 기반 접근 제어**를 통해 관리 효율성과 보안을 강화해야 함

------

------

## Assigning Permissions

![image-20250304114030687](/assets/cisco_post_img/2025-03-04-temp//image-20250304114030687.png)

**권한 할당하기:**

1. 객체 선택

2. 도메인 선택

3. 사용자/그룹 선택

4. 역할 선택

5. 권한을 하위 객체에 전파

------

------

## Applying Permissions: Scenario 1 (**권한 적용 시나리오 1: 계층적 권한 적용과 예외 처리**)



![image-20250304114312410](/assets/cisco_post_img/2025-03-04-temp//image-20250304114312410.png)

vSphere에서 **권한(permissions)**은 객체 계층 구조를 따라 하위 객체로 전파될 수 있으며, 특정 하위 객체에서 이를 재정의할 수도 있습니다.



**1. 기본 개념**

​	•	**권한 전파(Propagation)**: 특정 객체에 할당된 권한이 그 하위 모든 객체에 자동으로 적용됨.

​	•	**예외 처리(Override)**: 특정 하위 객체에서 상위 객체의 권한을 무시하고 다른 권한을 설정할 수 있음.



**2. 적용 사례**



위의 예시에서 **사용자 Greg**은 다음과 같은 권한 구조를 가짐:

​	1.	**Training 데이터센터** 전체에 대해 Read-Only 권한을 가짐.

​	2.	Training 데이터센터 내에 있는 **모든 하위 객체(Production, Test and Dev 포함)**에도 Read-Only 권한이 자동으로 전파됨.

​	3.	단, Prod03-2 VM에서는 예외적으로 Administrator 역할을 가짐.



**3. 주요 특징**

​	•	Greg은 Training 데이터센터 내 대부분의 객체에서 Read-Only 권한을 가지므로 해당 객체들의 상태를 볼 수는 있지만 변경할 수 없음.

​	•	하지만 Prod03-2 VM에서는 Administrator 권한이 있어 이 VM에 대한 모든 관리 작업을 수행할 수 있음.

​	•	이는 특정 개체에 대해 개별적인 권한을 설정할 수 있음을 보여주는 사례이며, 권한 전파(Propagation) 기능을 활용하는 대표적인 방식임.



**4. 활용 방안**

​	•	대규모 가상 환경에서 특정 사용자 또는 그룹이 특정 데이터센터, 폴더, 또는 VM에 대해 제한적인 액세스를 가지도록 설정할 수 있음.

​	•	중요한 VM이나 자원에 대해서는 일반적인 전파된 권한을 무시하고 별도의 관리 권한을 부여할 수 있음.

​	•	vSphere 내에서 **권한 관리의 유연성을 극대화**할 수 있도록 설계됨.



이런 방식으로 vSphere의 **유연한 권한 제어**를 통해 조직의 보안 및 운영 정책을 효과적으로 적용할 수 있습니다.

------

## Applying Permissions: Scenario 2 (**권한 적용 시나리오 2: 그룹별 권한 병합 [Union of Privileges]**)

![image-20250304114722158](/assets/cisco_post_img/2025-03-04-temp//image-20250304114722158.png)

vSphere에서 사용자가 **여러 그룹에 속해 있고, 동일한 객체에 대해 각 그룹이 서로 다른 권한을 가지고 있는 경우**, 사용자는 **각 그룹에서 부여된 모든 권한을 통합적으로 부여받게 됩니다**.

**1. 기본 개념**

​	•	**그룹 권한 통합(Union of Privileges)**: 사용자가 여러 그룹에 속해 있으면, 각 그룹에서 부여받은 권한이 모두 적용됨.

​	•	**전파(Propagation)**: 부모 객체에 설정된 권한이 하위 객체로 자동 적용될 수 있음.

**2. 적용 사례**



위의 예제에서 **사용자 Greg**은 **Group1과 Group2** 두 개의 그룹에 속해 있으며, 각각의 그룹은 서로 다른 역할(Role)을 가짐:

​	1.	**Group1** → VM_Power_On 역할을 부여받음

​	•	이 역할에는 가상 머신(VM) 전원 켜기 권한만 포함됨.

​	2.	**Group2** → Take_Snapshots 역할을 부여받음

​	•	이 역할에는 스냅샷 생성 및 삭제 권한이 포함됨.

​	3.	**Training 데이터센터**에서 이 두 개의 역할이 **전파(Propagation)** 되어 모든 하위 객체(Production, Test and Dev 포함)에 자동 적용됨.

​	4.	**Greg은 Group1과 Group2 모두에 속해 있으므로, 두 개의 역할을 합한 권한을 가짐.**

​	•	결과적으로 Greg은 **Training 데이터센터 내의 모든 객체에서** VM을 **전원 켜기(Power On)할 수 있으며, 스냅샷 생성 및 삭제도 가능함**.

**3. 주요 특징**

​	•	**다중 그룹 멤버십을 통한 권한 확장:**

​	•	Greg은 특정 역할(Role)만 할당받은 것이 아니라, 자신이 속한 **모든 그룹에서 받은 권한이 조합**되어 전체적인 액세스 수준이 증가함.

​	•	**권한 전파(Propagation)에 따른 전체 객체 적용:**

​	•	Training 데이터센터에 설정된 그룹 권한이 **하위 모든 객체(Production, Test and Dev 포함)에 자동 적용**되므로 개별 객체에 대해 추가적으로 설정할 필요 없음.

**4. 활용 방안**

​	•	**역할 기반 액세스 제어(RBAC, Role-Based Access Control) 최적화:**

​	•	사용자가 단일 역할만 가지도록 제한하는 대신, 여러 그룹을 활용하여 **유연한 권한 조합**을 적용할 수 있음.

​	•	**운영 관리의 효율성 향상:**

​	•	특정 사용자에게 여러 그룹을 부여함으로써 **세밀한 권한 분배가 가능**하며, 필요 시 그룹 단위로 쉽게 관리 가능.

​	•	**보안 정책 적용:**

​	•	예를 들어, 운영 팀(Group1)은 VM을 켜고 끌 수 있지만, 백업 팀(Group2)은 스냅샷을 관리하는 등 역할을 분리하여 보안성을 강화할 수 있음.

**5. 결론**

​	•	vSphere에서 사용자의 권한은 **소속된 모든 그룹의 권한을 합산하여 적용**되므로, 역할을 적절히 설계하면 **더 유연하고 강력한 권한 관리가 가능함**.

​	•	특히, **각 그룹의 역할(Role)을 명확하게 정의하면 운영과 보안 정책을 효과적으로 적용할 수 있음**.

------

------

## Activity: Applying Group Permissions

![image-20250304115001814](/assets/cisco_post_img/2025-03-04-temp//image-20250304115001814.png)



이 예제는 **그룹 기반 권한 할당이 사용자에게 어떻게 적용되는지**를 보여줍니다.

​	1.	**Group1**은 **Administrator** 역할을 가지고 있고,

​	2.	**Group2**는 **No Access(접근 불가)** 역할을 가집니다.

​	3.	**Greg**는 **두 그룹(Group1, Group2)에 모두 속해 있습니다.**

![image-20250304115012784](/assets/cisco_post_img/2025-03-04-temp//image-20250304115012784.png)

**결과:**

Greg는 Group1에서 **Administrator(전체 권한)**을 받지만, Group2에서 **No Access(접근 불가)** 역할을 받습니다.

하지만, **vSphere에서는 여러 그룹의 권한이 있을 경우, 더 높은 권한을 적용**하기 때문에,

Greg는 **Administrator 권한을 유지**합니다.

------

## Applying Permissions: Scenario 4

![image-20250304115403340](/assets/cisco_post_img/2025-03-04-temp//image-20250304115403340.png)

이 시나리오는 **개별 사용자에게 설정된 권한이 그룹 권한보다 우선 적용되는 방식**을 보여줍니다.

**설정된 권한:**

​	1.	**Group1**은 **Training 데이터센터**에 **VM_Power_On 역할**을 가지고 있음

→ 하위 객체(예: VM)까지 이 권한이 전파됨

​	2.	**Group2**는 **Training 데이터센터**에 **Take_Snapshots 역할**을 가지고 있음

→ 하위 객체까지 전파됨

​	3.	**Greg(개별 사용자)**는 **Training 데이터센터**에서 **No Access(접근 불가) 역할**을 가짐

→ 하위 객체에도 적용됨



**결과:**

​	•	Greg는 **Group1과 Group2의 멤버**이지만, **개별 사용자 권한이 그룹 권한보다 우선** 적용됨.

​	•	**Greg가 Group1과 Group2를 통해 권한을 가질 수 있었지만, No Access가 개별적으로 설정되었으므로 Training 데이터센터 및 모든 하위 객체에 접근할 수 없음.**



**핵심 개념:**



**개별 사용자에게 명시적으로 부여된 권한이 그룹을 통해 할당된 모든 권한보다 우선적으로 적용됨.**

즉, **Greg는 Training 데이터센터 및 하위 객체에서 아무런 권한도 가질 수 없음.**

------

------

## About Global Permissions

![image-20250304115754635](/assets/cisco_post_img/2025-03-04-temp//image-20250304115754635.png)



Global Permissions(글로벌 권한)은 **vRealize Orchestrator** 같은 솔루션과 **여러 vCenter 인스턴스**에 걸쳐 **전체 시스템에서 적용되는 권한**을 설정할 수 있습니다.



**Global Permissions의 특징**

​	1.	**전체 vCenter 계층에 적용**

​	•	단일 vCenter가 아닌 **모든 vCenter 인스턴스**와 관련된 개체(ESXi, VM, Content Library 등)에 대한 권한을 설정할 수 있음.

​	2.	**Global Root Object에서 관리**

​	•	글로벌 루트 객체(Global Root Object)는 vCenter의 모든 객체(Content Library, Data Center, Tag 등)를 포함하는 최상위 개체입니다.

​	•	이 루트 객체에서 설정된 권한은 **모든 vCenter 인스턴스**에서 동일하게 유지됩니다.

​	3.	**모든 vCenter 인스턴스에 대한 접근 권한 부여**

​	•	특정 vCenter 개체(ESXi 호스트, VM)에 대한 개별 권한이 아닌, **전체 인프라에서 사용자 또는 그룹이 동일한 역할을 수행하도록 설정** 가능.



**그림 설명**

​	•	**Global Root Object**는 전체 시스템의 최상위 객체입니다.

​	•	이 글로벌 루트 객체는 **모든 vCenter 인스턴스, Content Library, Tag 등을 포함**합니다.

​	•	글로벌 권한(Global Permissions)은 **vCenter의 개별 객체가 아니라 전체 vCenter 환경에 대한 권한을 관리**할 수 있도록 합니다.

**결론**



**Global Permissions은 vCenter 전체 인프라에 걸쳐 적용되는 권한을 제공하며, 다수의 vCenter 인스턴스를 관리하는 환경에서 일관된 액세스 정책을 설정하는 데 유용합니다.**

------

------

