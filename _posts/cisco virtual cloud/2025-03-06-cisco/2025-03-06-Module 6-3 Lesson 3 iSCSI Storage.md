---
title: <h0>Module 6-3 Lesson 3 iSCSI Storage</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-05-cisco]
tags: [Network, Cloud]
















---

# Lesson 3 iSCSI Storage



------

## **iSCSI 구성 요소 개요**

![image-20250306120238940](/assets/cisco_post_img/2025-03-06-Module 6-3 Lesson 3 iSCSI Storage//image-20250306120238940.png)

iSCSI (Internet Small Computer Systems Interface)는 **TCP/IP 네트워크를 통해 SCSI 명령을 전송하여 SAN(스토리지 영역 네트워크)과 호스트 간에 통신하는 프로토콜**이다.



iSCSI SAN은 **iSCSI 스토리지 시스템(스토리지 프로세서와 LUN 포함)과 ESXi 호스트 간의 연결을 제공하는 네트워크 인프라**로 구성됨.

------

**1. iSCSI SAN의 주요 구성 요소**

1. **iSCSI Storage System (iSCSI 스토리지 시스템)**
   * **스토리지 프로세서(Storage Processor, SP)**: iSCSI 트래픽을 처리하며 LUN을 호스트에 제공하는 역할.
   * **LUN(Logical Unit Number)**: 호스트가 사용할 수 있는 논리적 스토리지 단위.

2. **iSCSI Initiator (iSCSI 이니시에이터)**
   * **ESXi 호스트가 스토리지에 연결할 때 사용되는 인터페이스**.
   * **하드웨어 기반**과 **소프트웨어 기반** 이니시에이터로 구분됨.
   * **하드웨어 기반**: iSCSI 전용 HBA(Host Bus Adapter)를 사용하여 SCSI 명령을 직접 네트워크로 전송.
   * **소프트웨어 기반**: ESXi 호스트의 네트워크 어댑터(NIC)와 OS 내부의 iSCSI 소프트웨어를 사용하여 iSCSI 트래픽을 처리.

3. **IP Network (IP 네트워크)**
   * iSCSI는 **TCP/IP를 기반으로 스토리지 트래픽을 전달**.
   * **일반적으로 전용 네트워크 또는 VLAN을 사용하여 데이터 트래픽과 분리**.
   * **Jumbo Frame(점보 프레임) 활성화로 성능 최적화 가능**.

4. **iSCSI Target (iSCSI 타겟)**
   * **스토리지 어레이 내에서 SCSI 명령을 받아 처리하는 엔드포인트**.
   * 여러 개의 타겟을 구성하여 다양한 LUN을 제공할 수 있음.
   * **iSCSI 타겟은 특정 호스트(이니시에이터)만 액세스할 수 있도록 제한 가능**.

------

**2. iSCSI 프로토콜 작동 방식**

​	1.	**이니시에이터(ESXi 호스트)가 네트워크를 통해 하나 이상의 iSCSI 타겟을 찾음**.

​	2.	**타겟이 이니시에이터에게 LUN을 제공**.

​	3.	**이니시에이터는 IP 네트워크를 통해 타겟에 SCSI 명령을 전송**.

​	4.	**LUN이 ESXi 호스트에 마운트되고 데이터스토어로 사용됨**.

------

**3. iSCSI 보안 및 접근 제한**

​	•	**iSCSI 배열은 보안 설정을 통해 특정 호스트만 타겟에 접근하도록 제한 가능**.

​	•	**IP 주소 기반 접근 제한**.

​	•	**서브넷 기반 접근 제한**.

​	•	**CHAP(Challenge Handshake Authentication Protocol) 기반 인증**.

------

**4. 그림 설명**

​	•	**호스트(ESXi 서버)**는 **하드웨어 HBA 또는 소프트웨어 iSCSI 이니시에이터**를 사용하여 **IP 네트워크를 통해 iSCSI 스토리지 시스템과 연결됨**.

​	•	**스토리지 프로세서(SP)**는 **디스크 어레이 내부의 LUN을 관리하며, 호스트에서 전송된 SCSI 명령을 처리**.

​	•	**IP 네트워크를 통해 iSCSI 트래픽이 전송되며, 네트워크가 성능과 안정성을 보장하도록 구성되어야 함**.

------

------

## **iSCSI 주소 지정 개요**



iSCSI 환경에서 **iSCSI 노드(initiator 또는 target)는 네트워크에서 식별되고 발견될 수 있는 주소 지정 가능한 엔터티**이다. 각 iSCSI 노드는 **고유한 이름**을 가져야 하며, 이를 통해 **IP 주소 변경과 관계없이 스토리지를 지속적으로 관리할 수 있음**.

------

**1. iSCSI 노드의 주요 구성 요소**

1. **Initiator (이니시에이터)**
   * **iSCSI 명령을 전송하는 호스트 측 컴포넌트**.
   * **ESXi 서버 또는 iSCSI 호환 장치에 설정됨**.
   * **하드웨어(HBA 기반) 또는 소프트웨어 기반 iSCSI 이니시에이터로 구성 가능**.

2. **Target (타겟)**
   * **iSCSI 명령을 수신하고 LUN을 제공하는 스토리지 시스템**.
   * 여러 개의 LUN을 제공할 수 있으며, 특정 이니시에이터만 액세스할 수 있도록 설정 가능.

3. **IP Network (IP 네트워크)**

   * **iSCSI 트래픽이 TCP/IP 네트워크를 통해 전송됨**.

   * 일반적으로 전용 VLAN 또는 별도 네트워크를 구성하여 데이터 트래픽과 분리하는 것이 권장됨.

------

**2. iSCSI 노드의 주소 지정 방식**



각 iSCSI 노드는 **IQN(iSCSI Qualified Name) 또는 EUI(Extended Unique Identifier)**를 사용하여 고유하게 식별됨.

------

**(1) IQN (iSCSI Qualified Name)**

​	•	**최대 255자까지 사용 가능**.

​	•	**고유성을 유지하기 위해 도메인 기반 네이밍 규칙을 사용**.

​	•	일반적인 IQN 형식:

```
iqn.yyyy-mm.naming-authority:unique-identifier
```

예제:

```
iqn.2024-03.com.example:storage.disk1
```

​	•	iqn.: IQN 접두사.

​	•	2024-03: 조직이 도메인을 등록한 연도와 월.

​	•	com.example: 조직의 도메인 네임(반전된 도메인 형식).

​	•	storage.disk1: 특정 스토리지 장치 식별자.

------

**(2) EUI (Extended Unique Identifier)**

​	•	**IEEE에서 할당한 24비트 회사 식별자와 40비트의 고유한 ID(예: 시리얼 번호)로 구성**.

​	•	16자리 16진수로 표현됨.

​	•	일반적인 EUI 형식:

```
eui.0123456789ABCDEF
```

​	•	eui.: EUI 접두사.

​	•	0123456789ABCDEF: 16자리 고유 식별자.

------

**3. iSCSI 네임스페이스 적용 예시**

​	•	**스토리지 장치가 여러 개의 LUN을 제공하는 경우, IQN을 사용하여 LUN을 구분**.

​	•	**IQN 또는 EUI를 사용하면 네트워크 변경에도 iSCSI 스토리지 환경이 지속적으로 유지될 수 있음**.

------

**4. 그림 설명**

​	•	**호스트(ESXi 서버)에는 iSCSI Initiator가 설정되어 있으며, IP 네트워크를 통해 iSCSI Target과 통신함**.

​	•	**스토리지 프로세서(SP)는 iSCSI Target의 역할을 수행하며, LUN을 제공**.

​	•	**각 노드는 IQN 또는 EUI를 통해 고유하게 식별됨**.

​	•	**IP 네트워크를 통한 iSCSI 트래픽이 이루어지며, 안정적인 스토리지 연결을 위해 네트워크 구성 및 인증이 필요함**.

------

------

## **iSCSI 어댑터 개요**

![image-20250306120714482](/assets/cisco_post_img/2025-03-06-Module 6-3 Lesson 3 iSCSI Storage//image-20250306120714482.png)

ESXi 호스트가 **iSCSI 스토리지에 액세스하려면 iSCSI 어댑터를 설정해야 함**.

iSCSI 어댑터는 **SCSI 요청과 응답을 iSCSI 프로토콜로 캡슐화하여 전송**하며,

이를 **iSCSI 이니시에이터(initiator)**라고 부름.



iSCSI 어댑터는 **소프트웨어 iSCSI**와 **하드웨어 iSCSI** 두 가지 방식으로 구분됨.

------

**1. 소프트웨어 iSCSI (Software iSCSI)**

​	•	**VMkernel에 내장된 소프트웨어 이니시에이터 사용**.

​	•	**일반 네트워크 어댑터(NIC)를 사용하여 iSCSI 트래픽을 처리**.

​	•	iSCSI 프로토콜 처리를 CPU에서 수행 → **CPU 부하 증가 가능**.

​	•	**추가 하드웨어 없이 iSCSI 사용 가능하여 비용 절감**.



**구성 요소**

​	•	iSCSI Initiator (vmhba65): 소프트웨어 기반 iSCSI 이니시에이터.

​	•	TCP/IP (vmk1): iSCSI 트래픽을 처리하는 VMkernel 네트워크 포트.

​	•	NIC driver: 네트워크 인터페이스 드라이버.

​	•	NIC (vmnic1): 실제 네트워크 어댑터.

------

**2. 하드웨어 iSCSI (Hardware iSCSI)**



하드웨어 iSCSI 어댑터는 **iSCSI 트래픽 처리를 네트워크 어댑터 자체에서 수행**,

성능을 향상시키고 CPU 부하를 줄일 수 있음.

하드웨어 iSCSI는 **의존형(Dependent)과 독립형(Independent) 두 가지 유형**이 있음.



**(1) 의존형 하드웨어 iSCSI (Dependent HW iSCSI)**

​	•	**일반 NIC에 iSCSI 오프로드 기능(TCP Offload Engine, TOE)을 포함**.

​	•	**iSCSI 처리는 어댑터가 담당하지만, VMkernel에서 네트워크 설정이 필요**.

​	•	**VMkernel iSCSI 포트(vmk1)와 바인딩 필요**.

**구성 요소**

​	•	iSCSI Initiator: 하드웨어에서 iSCSI 프로세싱 처리.

​	•	TCP/IP (TOE): NIC에서 일부 네트워크 처리를 오프로드.

​	•	iSCSI / NIC HBA: NIC과 iSCSI 기능을 결합한 하드웨어.

​	•	vmhba1 / vmnic1: 네트워크 인터페이스.

**(2) 독립형 하드웨어 iSCSI (Independent HW iSCSI)**

​	•	**전용 iSCSI HBA를 사용하여 네트워크 및 iSCSI 트래픽을 독립적으로 처리**.

​	•	**VMkernel iSCSI 포트(vmk1) 불필요** → 하드웨어에서 직접 관리.

​	•	성능이 뛰어나지만 **전용 하드웨어가 필요하여 비용 증가**.

**구성 요소**

​	•	iSCSI HBA: iSCSI 및 네트워크 트래픽을 전담하는 독립형 어댑터.

​	•	iSCSI Initiator: 하드웨어에서 iSCSI 트래픽을 직접 처리.

​	•	TCP/IP (TOE): 전용 HBA에서 TCP/IP 오프로드.

​	•	vmhba1: HBA 장치.

------

**3. 비교 정리**

| **구분**                    | **소프트웨어 iSCSI** | **의존형 하드웨어 iSCSI** | **독립형 하드웨어 iSCSI** |
| --------------------------- | -------------------- | ------------------------- | ------------------------- |
| **iSCSI 처리 위치**         | CPU (소프트웨어)     | NIC (일부 오프로드)       | HBA (완전 오프로드)       |
| **성능**                    | 낮음 (CPU 사용 증가) | 중간 (NIC 오프로드)       | 높음 (전담 하드웨어)      |
| **네트워크 구성 필요 여부** | 필요 (VMkernel 포트) | 필요 (VMkernel 포트)      | 불필요 (HBA가 직접 처리)  |
| **비용**                    | 낮음 (일반 NIC 사용) | 중간 (특수 NIC 필요)      | 높음 (전용 HBA 필요)      |
| **사용 환경**               | 소규모 / 테스트      | 중규모                    | 대규모 / 엔터프라이즈     |

------

**4. 그림 설명**

​	•	**왼쪽 (Software iSCSI)**: VMkernel에서 iSCSI 처리를 수행하며, 일반 NIC을 통해 iSCSI 트래픽을 전송함.

​	•	**가운데 (Dependent HW iSCSI)**: iSCSI 트래픽의 일부를 NIC이 처리하지만, VMkernel 설정이 필요함.

​	•	**오른쪽 (Independent HW iSCSI)**: 전용 iSCSI HBA를 사용하여, iSCSI 트래픽을 하드웨어에서 완전히 처리함.

파란색 하이퍼 바이저

네트워크 인터페이스 카드 빨간색

------

**5. 결론**

​	•	**소프트웨어 iSCSI는 비용이 저렴하지만 CPU 부하가 증가할 수 있음**.

​	•	**의존형 하드웨어 iSCSI는 성능을 개선하지만 네트워크 구성이 필요함**.

​	•	**독립형 하드웨어 iSCSI는 성능이 가장 좋지만 비용이 높음**.

​	•	**운영 환경에 따라 적절한 iSCSI 방식 선택 필요**.

------

------

## **ESXi 네트워크 설정: 소프트웨어 iSCSI 구성**

![image-20250306121017767](/assets/cisco_post_img/2025-03-06-Module 6-3 Lesson 3 iSCSI Storage//image-20250306121017767.png)

소프트웨어 iSCSI를 사용하려면 **VMkernel 포트를 생성하여 iSCSI 트래픽을 처리**해야 함.

iSCSI 트래픽은 **다른 네트워크(NAS/NFS)와 분리하여 성능 및 보안을 최적화**하는 것이 중요함.

​	•	**물리적 분리(Physical Separation) 권장**

​	•	**물리적 분리가 어려울 경우 VLAN을 사용하여 논리적 분리(Logical Separation) 가능**

------

**1. 소프트웨어 iSCSI 네트워크 구성**



**(1) VMkernel 포트 생성**

​	•	VMkernel 포트는 iSCSI 트래픽을 처리하는 네트워크 인터페이스.

​	•	소프트웨어 iSCSI 이니시에이터 활성화 전에 **반드시 VMkernel 포트를 생성**해야 함.



**(2) 물리 네트워크 어댑터 설정**

​	•	**단일 물리 네트워크 어댑터 사용 시**

​	•	하나의 VMkernel 포트를 가상 스위치(vSwitch)에 연결하여 iSCSI 트래픽을 처리.

​	•	**두 개 이상의 물리 네트워크 어댑터 사용 시**

​	•	**호스트 기반 멀티패스(multipathing) 구성 가능**.

​	•	여러 개의 VMkernel 포트를 사용하여 **로드 밸런싱 및 장애 복구(Failover) 지원**.



**(3) 네트워크 격리 (Security & Performance)**

​	•	**iSCSI 네트워크는 다른 네트워크(관리, VM, vMotion)와 분리해야 함.**

​	•	**방법 1: 물리적 네트워크 분리**

​	•	별도의 네트워크 스위치 및 어댑터 사용.

​	•	**방법 2: VLAN을 사용한 논리적 분리**

​	•	가상 스위치 내에서 서로 다른 VLAN ID 할당.

------

**2. 그림 설명**



**(1) VMkernel 포트와 물리 어댑터 연결**

​	•	**IP Storage 1 (172.20.10.61, vmk1)**

​	•	iSCSI 네트워크용 **VMkernel 포트(vmknic)** 설정됨.

​	•	물리 네트워크 어댑터 **vmnic5**가 연결됨.

​	•	**IP Storage 2 (172.20.10.71, vmk2)**

​	•	두 번째 iSCSI VMkernel 포트 추가로 **멀티패스 구성 가능**.

​	•	다른 물리 어댑터 **vmnic6**과 연결됨.



**(2) 물리 어댑터(Physical Adapters)**

​	•	**vmnic0, vmnic4, vmnic5, vmnic6** → 10Gbps 속도로 동작.

​	•	**iSCSI 네트워크는 vmnic5, vmnic6을 사용**하여 독립적으로 트래픽을 처리.

------

**3. 최적화된 iSCSI 네트워크 구성**

​	•	**최적의 성능을 위해 물리적 네트워크 분리 권장.**

​	•	**VLAN을 통해 논리적으로 분리 가능.**

​	•	**멀티패스 구성(Multipathing) 적용 시 장애 복구 및 로드 밸런싱 가능.**



이러한 구성을 통해 **안정적이고 성능 최적화된 iSCSI 네트워크를 운영할 수 있음.**

------

------

## **iSCSI 타겟 검색 (iSCSI Target Discovery)**

![image-20250306121525441](/assets/cisco_post_img/2025-03-06-Module 6-3 Lesson 3 iSCSI Storage//image-20250306121525441.png)

iSCSI 어댑터는 네트워크에서 사용 가능한 스토리지를 검색하여 ESXi 호스트가 접근할 수 있는 리소스를 결정함.

ESXi 호스트는 **정적(Static) 검색** 및 **동적(SendTargets) 검색** 방법을 지원함.

------

**1. iSCSI 타겟 검색 방법**



**(1) 정적(Static) 검색**

​	•	**사전 구성된 타겟 정보(IP, 도메인)를 이용하여 직접 연결.**

​	•	이니시에이터(ESXi)가 타겟을 수동으로 지정.

​	•	**iSCSI LUN 목록이 고정됨** → 재검색 시 새로운 타겟 추가 불가.

​	•	보안 및 성능이 중요한 환경에서 유용함.

**(2) 동적(SendTargets) 검색**

​	•	**이니시에이터가 iSCSI 서버에게 SendTargets 요청을 전송하여 타겟 목록을 자동으로 가져옴.**

​	•	응답 시 타겟 IQN 및 사용 가능한 IP 주소 제공.

​	•	**새로운 iSCSI 타겟을 자동 감지 가능** → 환경 변경 시 유용함.

​	•	동적 검색 후 **탐색된 타겟이 정적 타겟 목록에 자동 추가됨.**

------

**2. 그림 설명**



**(1) iSCSI 스토리지 검색 프로세스**

​	1.	**ESXi 호스트의 iSCSI HBA**가 **IP 네트워크를 통해 iSCSI 스토리지(SP, Storage Processor)에 연결.**

​	2.	**정적 검색 시:** ESXi에서 직접 타겟 IP 및 IQN을 설정하여 접속.

​	3.	**동적 검색(SendTargets) 시:**

​	•	호스트가 스토리지에 SendTargets 요청을 보냄.

​	•	스토리지가 사용 가능한 **iSCSI 타겟 목록(IP, IQN 포함)을 반환.**

​	4.	**검색된 타겟은 vSphere Client에 추가됨.**

**(2) 좌측 하단 인터페이스**

​	•	**Static Discovery 탭:** ESXi 호스트에 **수동으로 추가된 iSCSI 타겟 목록 표시.**

​	•	“172.20.10.15:3260” → 정적 방식으로 등록된 iSCSI 타겟.

**(3) 우측 하단 인터페이스**

​	•	**검색된 iSCSI 타겟 목록**(SendTargets 응답 결과).

​	•	iqn.2008-08.com.starwindsoftware.shared:172.20.10.10:3260

​	•	iSCSI 타겟 IQN 및 IP 주소 표시.

​	•	검색된 타겟이 정적 타겟 목록으로 추가될 수 있음.

------

**3. 정적 vs. 동적 검색 비교**

| **검색 방법**              | **설명**                        | **장점**                          | **단점**                                                  |
| -------------------------- | ------------------------------- | --------------------------------- | --------------------------------------------------------- |
| **정적(Static) 검색**      | 미리 정의된 타겟 IP 및 IQN 사용 | 보안성, 고정된 타겟 사용          | 타겟 변경 시 수동 업데이트 필요                           |
| **동적(SendTargets) 검색** | iSCSI 서버로부터 자동 검색      | 자동화된 타겟 추가, 유지보수 용이 | 타겟 목록이 예측 불가 (일부 환경에서 보안 문제 발생 가능) |

------

**4. 요약**

​	•	ESXi 호스트는 iSCSI 스토리지를 검색하기 위해 **정적 또는 동적 방법을 사용.**

​	•	**정적 검색:** 미리 설정한 iSCSI 타겟과만 통신.

​	•	**동적 검색(SendTargets):** 서버에서 자동으로 타겟 목록을 가져와 추가.

​	•	**재검색(Rescan) 또는 호스트 재부팅 시, 동적 검색 결과가 갱신될 수 있음.**

​	•	**보안이 중요한 환경에서는 정적 검색을, 유연성이 필요한 환경에서는 동적 검색을 권장.**

------

------

## **iSCSI 보안: CHAP 인증**

![image-20250306121920233](/assets/cisco_post_img/2025-03-06-Module 6-3 Lesson 3 iSCSI Storage//image-20250306121920233.png)

CHAP(Challenge-Handshake Authentication Protocol)은 iSCSI 이니시에이터(initiator)와 타겟(target) 간의 인증을 제공하는 방식임.

**기본적으로 ESXi에서는 CHAP가 비활성화되어 있으며, 필요에 따라 활성화할 수 있음.**

------

**CHAP 인증 방식**

ESXi에서는 다음 두 가지 CHAP 인증 방식을 지원함.

**(1) 단방향 CHAP (Unidirectional CHAP)**

​	•	**iSCSI 타겟이 이니시에이터를 인증.**

​	•	이니시에이터는 사전에 설정된 CHAP 시크릿(secret)을 타겟에게 제공하여 인증받음.

​	•	**이니시에이터는 타겟을 인증하지 않음.**

​	•	보안이 필요한 경우 기본적인 인증 기능을 제공.



**(2) 양방향 CHAP (Bidirectional CHAP)**

​	•	**이니시에이터와 타겟이 서로 인증.**

​	•	**추가적인 보안 계층을 제공.**

​	•	**각각 다른 CHAP 시크릿을 설정해야 함.**

​	•	CHAP의 3-way 핸드셰이크 알고리즘을 사용하여 인증 진행.

 	•	**네트워크 암호화 및 방화벽 규칙을 통해 추가적인 보호 조치 가능.**

------

**요약**

​	•	CHAP는 iSCSI 환경에서 인증을 수행하는 보안 프로토콜.

​	•	**단방향 CHAP**: 타겟이 이니시에이터를 인증.

​	•	**양방향 CHAP**: 이니시에이터와 타겟이 서로 인증.

​	•	**iSCSI 보안을 강화하려면 CHAP를 활성화하고, VLAN 및 네트워크 분리를 고려하는 것이 권장됨.**

------

------

## **Software iSCSI의 Multipathing 설정**

![image-20250306122001307](/assets/cisco_post_img/2025-03-06-Module 6-3 Lesson 3 iSCSI Storage//image-20250306122001307.png)

소프트웨어 iSCSI 환경에서는 **여러 개의 NIC**를 활용하여 iSCSI 트래픽의 **다중 경로(Multipathing)** 및 **장애 조치(Failover)**를 구성할 수 있음.

------

**1. Software iSCSI의 Multipathing 개념**

​	•	**각 NIC는 별도의 VMkernel 포트(VMkernel Port)에 연결됨.**

​	•	**각 VMkernel 포트는 iSCSI Initiator에 바인딩됨.**

​	•	**Multipathing을 통해 네트워크 장애 발생 시 자동으로 다른 경로를 사용하여 연결을 유지함.**

​	•	**iSCSI 트래픽의 부하 분산(Load Balancing)과 장애 조치(Failover)를 지원.**

------

**2. 그림 설명**

**(1) iSCSI Storage와 네트워크 연결**

​	•	**iSCSI Storage:** 다수의 LUN(논리 유닛)이 포함된 스토리지 풀.

​	•	**SP (Storage Processor):** iSCSI 스토리지를 네트워크에 연결하는 역할.

**(2) ESXi 호스트의 네트워크 구성**

​	•	**vSwitch (가상 스위치):** 여러 개의 물리적 NIC을 가상 네트워크로 연결.

​	•	**vmnic1, vmnic2:** 두 개의 물리적 네트워크 인터페이스 카드(NIC)가 iSCSI 트래픽을 담당.

​	•	**VMkernel Port Binding:** 각 VMkernel 포트가 특정 NIC와 연결됨.

​	•	**IP-Storage-01:** vmk1 (172.20.13.53) → vmnic1을 통해 iSCSI 스토리지와 연결됨.

​	•	**IP-Storage-02:** vmk2 (172.20.13.63) → vmnic2를 통해 iSCSI 스토리지와 연결됨.

​	•	**vmhba65 (iSCSI Software Initiator):** 소프트웨어 iSCSI 이니시에이터를 통해 iSCSI 타겟에 접근.

------

**3. Software iSCSI Multipathing 설정 방식**

**(1) 활성/대기(Active/Standby)**

​	•	기본 경로(Active Path) 하나를 사용하며, 장애 발생 시 대기 경로(Standby Path)로 전환.

​	•	단순한 장애 복구를 위한 설정.

**(2) 라운드 로빈(Round Robin)**

​	•	여러 NIC을 번갈아 사용하여 부하를 분산.

​	•	네트워크 대역폭을 최적화하여 성능 향상.

**(3) 다중 경로 활성(Active/Active)**

​	•	모든 활성 경로를 동시에 사용하여 성능을 극대화.

​	•	일부 고급 iSCSI 스토리지에서만 지원됨.

------

**4. iSCSI Multipathing 설정 시 고려사항**

1. **VMkernel 포트 바인딩**
   * 각 VMkernel 포트는 하나의 물리적 NIC(vmnic)과 1:1로 연결되어야 함.
   * 여러 개의 VMkernel 포트를 iSCSI Initiator에 바인딩해야 함.

2. **vSwitch 구성**
   * **각 VMkernel 포트는 별도의 NIC(vmnic)에 연결되어야 함.**
   * 포트 그룹에 특정 물리 NIC만 연결하여 iSCSI 트래픽을 격리.

3. **라우팅 제한**
   * iSCSI 트래픽 경로는 vSphere Multipathing 모듈을 통해 관리됨.
   * **iSCSI 트래픽은 VMkernel 라우팅 테이블을 참조하지 않음.**
   * iSCSI 트래픽에 IP 라우팅을 적용하는 것은 성능 저하를 유발할 수 있음.

**5. 요약**

​	•	**Software iSCSI Multipathing은 여러 개의 NIC을 활용하여 iSCSI 트래픽을 최적화함.**

​	•	**각 VMkernel 포트는 개별 NIC과 바인딩되어야 하며, iSCSI Initiator와 연결됨.**

​	•	**vSphere Multipathing 모듈을 통해 트래픽을 관리하며, 라우팅은 사용되지 않음.**

​	•	**부하 분산(Round Robin) 및 장애 조치(Failover) 설정을 통해 성능과 가용성을 높일 수 있음.**

------

------

## **Multipathing with Dependent Hardware iSCSI**

![image-20250306122225572](/assets/cisco_post_img/2025-03-06-Module 6-3 Lesson 3 iSCSI Storage//image-20250306122225572.png)

------

**1. 개요**

​	•	**종속형 하드웨어 iSCSI(Dependent Hardware iSCSI)**는 네트워크 인터페이스 카드(NIC)와 iSCSI HBA(Host Bus Adapter)를 결합한 어댑터를 사용함.

​	•	각 NIC는 **별도의 VMkernel 포트(VMkernel Port)에 연결됨.**

​	•	각 VMkernel 포트는 **iSCSI Initiator**와 바인딩되어 iSCSI 트래픽을 관리.

​	•	**vSphere 네트워크 및 iSCSI 설정에 따라 동작**하는 방식으로, 하드웨어 오프로드 기능을 활용하면서도 **소프트웨어 iSCSI와 유사한 방식으로 구성**됨.

------

**2. 그림 설명**



**(1) iSCSI Storage 및 네트워크 연결**

​	•	**iSCSI Storage:** 스토리지 프로세서(Storage Processor, SP)를 통해 네트워크에 연결됨.

​	•	**SP (Storage Processor):** 스토리지의 논리 유닛(LUN)을 ESXi 호스트에 제공.



**(2) ESXi 호스트 내 네트워크 및 iSCSI 어댑터 설정**

​	•	**vSwitch (가상 스위치):** iSCSI 트래픽을 위한 가상 네트워크 환경 제공.

​	•	**vmnic1, vmnic2 (NIC):** 두 개의 물리적 네트워크 어댑터가 각각 iSCSI 트래픽을 처리.

​	•	**vmhba1, vmhba2 (iSCSI HBA):** NIC과 함께 동작하는 iSCSI 어댑터.

​	•	**VMkernel 포트 바인딩 (VMkernel Port Binding):**

​	•	**IP-Storage-01:** vmk1 (172.20.13.52) → vmnic1 + vmhba1과 연결됨.

​	•	**IP-Storage-02:** vmk2 (172.20.13.62) → vmnic2 + vmhba2과 연결됨.

**3. Dependent Hardware iSCSI의 특징**

​	•	**iSCSI 기능을 하드웨어(NIC)에서 처리**하면서도 **vSphere 네트워크 설정을 활용**하는 하이브리드 방식.

​	•	**HBA를 독립적으로 사용하는 Independent Hardware iSCSI와는 다름.**

​	•	**NIC과 iSCSI 엔진이 같은 포트를 공유**하며, vSphere에서 관리 가능.

​	•	**소프트웨어 iSCSI보다 CPU 부하가 낮음.**

​	•	**Multipathing 및 Failover 지원:** 두 개 이상의 경로를 설정하여 장애 발생 시 자동 전환 가능.

**4. iSCSI Multipathing 설정**

​	•	**VMkernel 포트 바인딩을 통해 Multipathing 지원.**

​	•	**각 VMkernel 포트는 단일 물리 NIC에 연결됨.**

​	•	**vSphere에서 다중 경로(Active/Active, Active/Standby) 구성 가능.**

​	•	**Load Balancing 및 Failover를 활용하여 iSCSI 트래픽의 안정성 및 성능 향상.**

**5. 요약**

​	•	**Dependent Hardware iSCSI는 NIC과 iSCSI HBA가 결합된 하드웨어를 활용함.**

​	•	**각 VMkernel 포트가 특정 NIC 및 iSCSI Initiator와 바인딩됨.**

​	•	**vSphere 네트워크 설정을 사용하면서도 하드웨어 iSCSI의 성능을 일부 활용 가능.**

​	•	**Multipathing과 Failover 설정을 통해 iSCSI 네트워크의 안정성과 성능을 높일 수 있음.**

------

------

## **Multipathing with Independent Hardware iSCSI**

![image-20250306122253492](/assets/cisco_post_img/2025-03-06-Module 6-3 Lesson 3 iSCSI Storage//image-20250306122253492.png)

**1. 개요**

​	•	**독립형 하드웨어 iSCSI(Independent Hardware iSCSI)**는 **전용 iSCSI HBA(Host Bus Adapter)**를 사용하여 **iSCSI 스토리지와 직접 통신**하는 방식.

​	•	일반적으로 **ESXi 호스트에는 두 개 이상의 iSCSI HBA가 있으며**, 하나 이상의 스위치를 통해 스토리지 시스템에 연결됨.

​	•	**VMkernel 포트 바인딩이 필요하지 않으며**, 하드웨어 레벨에서 **iSCSI 프로세싱을 전담**하여 성능 최적화 가능.

**2. 그림 설명**



**(1) iSCSI Storage 및 네트워크 연결**

​	•	**iSCSI Storage:** 스토리지 프로세서(Storage Processor, SP)를 통해 네트워크에 연결됨.

​	•	**SP (Storage Processor):** LUN을 ESXi 호스트에 제공.

​	•	**IP Network:** iSCSI 트래픽이 전송되는 네트워크.



**(2) ESXi 호스트 내 iSCSI HBA 구성**

​	•	**Independent Hardware iSCSI Adapter (iSCSI HBA):**

​	•	**vmhba1 (IP: 172.20.13.51)** → SP1과 연결됨.

​	•	**vmhba2 (IP: 172.20.13.61)** → SP2와 연결됨.

​	•	**HBA는 물리적 네트워크 포트를 직접 사용하여 iSCSI 트래픽을 처리**하므로, 소프트웨어 iSCSI처럼 VMkernel 네트워크를 통한 바인딩이 필요 없음.

**3. Independent Hardware iSCSI의 특징**

​	•	**전용 하드웨어(HBA)가 iSCSI 트래픽을 처리**하여 CPU 부하를 줄이고 성능을 향상.

​	•	**Multipathing을 통해 여러 경로 제공하여 가용성 및 안정성을 높일 수 있음.**

​	•	**소프트웨어 iSCSI 및 Dependent Hardware iSCSI보다 구성 단순화** (VMkernel 바인딩 불필요).

​	•	**Active-Active 또는 Active-Passive 구성을 통해 다중 경로 페일오버 지원.**

**4. iSCSI Multipathing 설정**

​	•	**HBA가 자동으로 여러 경로를 설정하여 Failover 및 Load Balancing 수행.**

​	•	**ESXi에서 다중 경로 정책(MPP, Multi-Pathing Policy)을 적용하여 성능 최적화 가능.**

​	•	**Active-Active 및 Active-Passive 디스크 어레이에 따라 동작 방식이 다름.**

**5. 요약**

​	•	**독립형 하드웨어 iSCSI는 전용 HBA를 사용하여 iSCSI 트래픽을 관리하며, CPU 부하를 줄이고 성능을 높이는 방식.**

​	•	**VMkernel 포트 바인딩이 필요 없으며, HBA가 직접 iSCSI 트래픽을 처리.**

​	•	**Multipathing을 활용하여 이중화 및 성능 최적화 가능.**

​	•	**Active-Active 또는 Active-Passive 환경에서 Failover 및 Load Balancing을 제공.**

------

------

## **Lab 9: iSCSI 스토리지 액세스**



iSCSI 데이터스토어에 대한 액세스를 구성:

​	1.	**기존 ESXi 호스트 iSCSI 구성 확인**

​	2.	**IP 스토리지용 VMkernel 포트 추가**

​	3.	**두 번째 IP 스토리지용 VMkernel 포트 추가**

​	4.	**ESXi 호스트에 iSCSI 소프트웨어 어댑터 추가**

​	5.	**iSCSI 타겟 서버에서 LUN 검색**