---
title: <h0>6-4 Lesson 4 VMFS Datastores</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-05-cisco]
tags: [Network, Cloud]


















---

# 6-4 Lesson 4 VMFS Datastores

------

## **NFS 구성 요소 (NFS Components)**

![image-20250306143809559](/assets/cisco_post_img/2025-03-06-Module 6-5 Lesson 5 NFS Datastores//image-20250306143809559.png)

NFS(Network File System)는 **ESXi 호스트와 NFS 서버 간에 파일 공유를 제공하는 프로토콜**이다.

ESXi 호스트는 **VMkernel 포트를 통해 NFS 서버에 접근**하여 스토리지를 마운트할 수 있다.



------



**1. NFS 구성 요소**



NFS 환경에서는 다음과 같은 주요 구성 요소가 필요하다.



**(1) NFS 서버**

​	•	하나 이상의 **공유 디렉토리(exported directory)**를 포함하고 있음.

​	•	ESXi 호스트와 **TCP/IP 네트워크를 통해 연결**됨.

​	•	예시: /iso 디렉토리를 공유하여 ISO 파일을 저장할 수 있음.



**(2) ESXi 호스트**

​	•	**VMkernel 포트를 통해 NFS 서버와 통신**함.

​	•	가상 스위치(Virtual Switch)에 연결된 **NIC를 사용하여 TCP/IP 네트워크를 통해 NFS에 접근**.

​	•	공유된 NFS 볼륨을 **데이터스토어로 마운트하여 VM 파일, ISO 이미지 등을 저장 가능**.



**(3) TCP/IP 네트워크**

​	•	NFS 트래픽이 **네트워크를 통해 전달**됨.

​	•	**ESXi 호스트와 NFS 서버 간의 연결을 담당**하며, 물리적인 NIC(Network Interface Card)를 통해 연결됨.



------



**2. NFS 연결 방식**

​	1.	**NFS 서버에서 공유 디렉토리(exported directory)를 설정**.

​	2.	**ESXi 호스트에서 VMkernel 포트를 통해 NFS 서버에 접근**.

​	3.	**TCP/IP 네트워크를 통해 가상 스위치(Virtual Switch)와 연결된 NIC를 사용**하여 NFS 서버에 접속.

​	4.	**NFS 데이터스토어를 마운트하여 VM 파일, ISO 이미지 등의 저장소로 활용**.



------



**3. 그림 설명**

​	•	**NFS 서버가 /iso 디렉토리를 공유**하며, 이 디렉토리는 **ESXi 호스트에서 접근 가능**.

​	•	**ESXi 호스트는 VMkernel 포트를 통해 가상 스위치(Virtual Switch)와 연결**.

​	•	**TCP/IP 네트워크를 통해 NFS 서버와 통신**하며, 물리적인 NIC를 통해 데이터 전송이 이루어짐.



------



**4. 결론**

​	•	NFS를 사용하면 **ESXi 호스트가 공유 스토리지를 손쉽게 액세스**할 수 있음.

​	•	**TCP/IP 기반이므로 네트워크 구성을 최적화하여 성능을 향상**시킬 수 있음.

​	•	**물리적으로 네트워크를 분리하거나 VLAN을 설정하여 보안 및 성능을 향상**시키는 것이 중요함.

------

------

## **NFS v3와 NFS v4.1 비교 (NFS v3 vs. NFS v4.1)**



NFS(Network File System)는 **ESXi에서 사용할 수 있는 파일 기반 스토리지 프로토콜**이며, NFS 데이터스토어는 NFS 3 또는 NFS 4.1 버전으로 생성 가능하다.

그러나 두 버전 간의 차이점으로 인해 **동일한 데이터스토어를 서로 다른 NFS 버전에서 동시에 액세스할 수 없다**.



------



**1. NFS v3 vs. NFS v4.1 비교**

| **항목**                      | **NFS 3**                               | **NFS 4.1**                                             |
| ----------------------------- | --------------------------------------- | ------------------------------------------------------- |
| **Multipathing**              | ESXi에서 관리                           | 기본적으로 지원 (Native Multipathing, Session Trunking) |
| **인증 방식**                 | AUTH_SYS (Root 기반 인증)               | 선택적 Kerberos 인증 지원                               |
| **파일 잠금(File Locking)**   | VMware 독점적인 클라이언트 측 파일 잠금 | 서버 측 파일 잠금                                       |
| **오류 추적(Error Tracking)** | 클라이언트 측 오류 추적                 | 서버 측 오류 추적                                       |





------



**2. NFS 4.1의 향상된 기능**

​	•	**Multipathing 및 Session Trunking 지원**

​	•	NFS 4.1은 기본적으로 **Multipathing**을 지원하며, 네트워크 부하 분산 및 성능 향상을 제공.

​	•	Session Trunking을 통해 여러 경로에서 **병렬 I/O 처리가 가능**.

​	•	**Kerberos 인증 지원**

​	•	NFS 4.1에서는 **Kerberos 기반의 보안 인증**이 추가되어 더 안전한 데이터 보호가 가능.

​	•	**서버 측 파일 잠금 (Server-side File Locking)**

​	•	NFS 3에서는 **VMware 독자적인 파일 잠금 방식**을 사용하지만, NFS 4.1에서는 **서버 측 파일 잠금이 표준 방식으로 적용**.

​	•	**서버 측 오류 추적 (Server-side Error Tracking)**

​	•	NFS 4.1에서는 **서버에서 직접 오류를 감지하고 추적**하여 클라이언트와의 네트워크 문제를 줄일 수 있음.



------



**3. NFS v3와 v4.1을 혼용할 경우 발생하는 문제**

​	•	**NFS v3과 NFS v4.1을 같은 데이터스토어에서 동시에 사용할 수 없음**.

​	•	같은 데이터스토어를 NFS 3과 NFS 4.1이 **혼합하여 접근하면 데이터 손상이 발생**할 가능성이 높음.

​	•	만약 NFS 4.1로 구성된 데이터스토어를 사용한다면, **접근하는 모든 호스트가 NFS 4.1을 사용해야 함**.



------



**4. 결론**

​	•	NFS 4.1은 **Multipathing, Kerberos 인증, 서버 측 파일 잠금** 등의 개선된 기능을 제공하여 **보안성과 성능이 향상**됨.

​	•	그러나 **NFS 3과 NFS 4.1을 혼용하면 데이터 손상이 발생할 수 있으므로, 환경에 맞는 버전을 일관되게 적용하는 것이 중요**함.

------

------

## **vSphere 환경에서 NFS 버전 호환성**



vSphere는 **NFS 4.1을 지원하여 NFS 3의 여러 가지 제한 사항을 개선**하지만, **두 버전(NFS 3 및 NFS 4.1)의 사용에는 중요한 제약 사항이 존재**한다.

특히, **NFS 4.1은 Storage DRS, Storage I/O Control 및 일부 Site Recovery Manager(SRM) 기능을 지원하지 않는다**.



------



**1. vSphere 기술별 NFS 3 및 NFS 4.1 호환성**

| **vSphere 기술**                                 | **NFS 3** | **NFS 4.1**                    |
| ------------------------------------------------ | --------- | ------------------------------ |
| **vSphere vMotion & Storage vMotion**            | ✅ 지원    | ✅ 지원                         |
| **vSphere HA & Fault Tolerance(FT)**             | ✅ 지원    | ✅ 지원                         |
| **vSphere Distributed Resource Scheduler (DRS)** | ✅ 지원    | ✅ 지원                         |
| **Host Profiles**                                | ✅ 지원    | ✅ 지원                         |
| **Storage DRS & Storage I/O Control**            | ✅ 지원    | ❌ 지원되지 않음                |
| **Virtual Volumes (VVols)**                      | ✅ 지원    | ✅ 지원                         |
| **Site Recovery Manager (SRM)**                  | ✅ 지원    | ⚠️ **일부 기능 지원 (Partial)** |
| **vSphere Virtual Volumes & Replication**        | ✅ 지원    | ✅ 지원                         |
| **vRealize Operations Manager**                  | ✅ 지원    | ✅ 지원                         |

🔹 **⚠️ Site Recovery Manager(SRM) 관련 주의사항**

​	•	NFS 4.1 데이터스토어는 **배열 기반 복제(Array-based Replication)** 및 **vSphere Virtual Volumes 복제(VVols Replication)** 를 지원하지 않음.

​	•	그러나 **vSphere Replication 방식으로는 사용 가능**.



------



**2. NFS 4.1의 주요 개선점**



NFS 4.1은 **NFS 3의 여러 제한을 극복하기 위해 다양한 기능을 추가**하였으며, 보안 및 성능이 크게 향상되었다.



✅ **Multipathing 및 Session Trunking 지원**

​	•	NFS 4.1은 **서버가 Session Trunking을 지원할 경우 Multipathing을 사용할 수 있음**.

​	•	다중 IP 주소를 통해 단일 NFS 볼륨에 접근 가능.

​	•	단, **Client ID 기반 트렁킹은 지원되지 않음**.



✅ **Kerberos 인증 지원**

​	•	NFS 4.1에서는 기존의 AUTH_SYS 방식 외에도 **Kerberos 인증을 지원하여 보안이 강화됨**.



✅ **향상된 파일 잠금 (Built-in File Locking)**

​	•	NFS 4.1에서는 **서버 측에서 파일 잠금 기능을 개선**하여 데이터 무결성을 보장.



✅ **오류 복구 기능 강화**

​	•	**서버 측에서 열린 파일 및 위임(Delegations) 추적 가능**

​	•	연결 끊김(Server Failure) 및 네트워크 장애 시 더 나은 복구 성능 제공.



✅ **프로토콜 효율성 개선**

​	•	**Session Lease 기능 추가 및 프로토콜 오버헤드 감소**

​	•	기존 NFS 3보다 **데이터 접근 속도가 향상**됨.



------



**3. NFS 4.1 클라이언트의 추가 기능**



NFS 4.1 클라이언트는 **기존 NFS 3보다 개선된 파일 및 세션 관리 기능을 제공**한다.

​	•	**상태 기반(Stateful) 잠금 및 공유 예약 기능 지원**

​	•	**보조 프로토콜(Auxiliary Protocol) 없이 파일 잠금 및 마운트 가능**

​	•	**트렁킹(True NFS Multipathing) 지원**

→ **여러 경로(Session)를 통해 NAS 스토리지에 연결하고, 부하 분산 가능**

​	•	**향상된 오류 복구 기능**

→ **서버 장애 및 연결 손실 시, 보다 빠르게 복구 가능**



------



**4. 결론**



✅ **NFS 4.1은 보안성(Kerberos 인증), 성능(Multipathing), 오류 복구 기능이 향상**됨.

✅ **vSphere 주요 기능(vMotion, HA, DRS 등)과 호환 가능**하지만,

❌ **Storage DRS, Storage I/O Control, Site Recovery Manager의 일부 기능과는 호환되지 않음**.

⚠️ **혼합 사용 시 데이터 손상 위험이 있으므로, 환경에 맞는 버전을 일관되게 적용해야 함**.

------

------

