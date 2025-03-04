---
title: <h0>Module 5 Configuring vSphere Networking</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-04-cisco]
tags: [Network, Cloud]













---

# Module 5 Configuring vSphere Networking



**ESXi 네트워킹의 중요성**

ESXi 네트워크를 올바르게 구성하면 **가상 머신(VM)**이 다른 **가상 및 물리적 머신과 원활하게 통신**할 수 있습니다. 또한, **VMkernel을 활용한 원격 호스트 관리 및 IP 기반 스토리지 운영**이 가능해집니다.

**vSphere 네트워크 구성**

​	1.	**vSphere 표준 스위치(Standard Switch, vSS)**

​	•	소규모 환경에 적합

​	•	ESXi 호스트별 개별 설정이 필요하며, 중앙 집중식 관리 기능이 없음

​	•	간단한 네트워크 구성 시 유용

​	2.	**vSphere 분산 스위치(Distributed Switch, vDS)**

​	•	대규모 환경에서 네트워크 관리 효율성을 극대화

​	•	vCenter를 통해 여러 ESXi 호스트의 네트워크를 중앙에서 관리 가능

​	•	고급 기능 지원 (트래픽 모니터링, QoS, 보안 강화)

**규모 확장 및 관리 효율성**

​	•	**소규모 환경**에서는 **vSphere 표준 스위치(vSS)**가 적절

​	•	**대규모 환경**에서는 **vSphere 분산 스위치(vDS)**가 **효율적인 네트워크 관리 및 성능 최적화**에 필수적



**결론**



ESXi 네트워크가 **올바르게 설정되어야** 가상 머신과 물리적 네트워크 간 원활한 통신이 가능합니다.

환경 규모에 따라 적절한 **표준 스위치(vSS) 또는 분산 스위치(vDS)를 선택하여 네트워크를 최적화하는 것이 중요**합니다.

------

------

## About Virtual Switches(**가상 스위치의 개요**)

가상 스위치(Virtual Switch, vSwitch)는 **가상 머신(VM)을 물리적 네트워크에 연결**하는 역할을 합니다.

**가상 스위치의 주요 기능**

1. **VM 간 네트워크 연결**

​	•	같은 ESXi 호스트 내의 VM 간 통신 제공

​	•	다른 ESXi 호스트에 있는 VM과도 연결 가능

2. **VM과 물리적 네트워크 연결**

​	•	가상 머신이 외부 네트워크(인터넷, 사내망 등)와 통신할 수 있도록 지원

3. **(중요)VMkernel 서비스 지원** - 하이퍼바이저 통신을 위해서 V통신을 하는것

​	•	**vSphere vMotion**: VM의 실시간 마이그레이션

​	•	**iSCSI 및 NFS 스토리지**: 네트워크 기반 스토리지 액세스

​	•	**관리 네트워크 접속**: ESXi 호스트 및 vCenter 관리

**가상 스위치의 유형**

1. **vSphere 표준 스위치(Standard Switch, vSS)**

​	•	개별 ESXi 호스트에서 로컬로 관리

​	•	기본적인 네트워크 연결 제공

​	•	소규모 환경에서 적합

2. ​	**vSphere 분산 스위치(Distributed Switch, vDS)**

​	•	vCenter를 통해 여러 ESXi 호스트에 걸쳐 중앙 집중식 관리

​	•	고급 네트워크 기능 및 트래픽 제어 가능

​	•	대규모 환경에서 유용



**결론**



가상 스위치는 **VM 간 및 VM과 물리적 네트워크 간의 연결을 담당**하며, **vMotion, iSCSI/NFS 스토리지, 관리 네트워크와 같은 중요한 ESXi 서비스**를 지원합니다.

환경 규모에 따라 **vSS 또는 vDS를 선택**하여 최적의 네트워크 구성을 해야 합니다.

------

------

## Types of Virtual Switches(**가상 스위치의 유형**)





가상 네트워크는 **표준 스위치(Standard Switch, vSS)** 와 **분산 스위치(Distributed Switch, vDS)** 를 지원합니다.

이 두 스위치 유형은 **자동으로 포트를 생성 및 제거**하며, 동적인 확장성을 제공합니다.

**1. 표준 스위치 (Standard Switch, vSS)**

​	•	**개별 ESXi 호스트에서 로컬로 관리**

​	•	하나의 ESXi 호스트에서만 구성 가능

​	•	**각 호스트마다 별도로 설정해야 하므로 관리 오버헤드 증가**

​	•	**소규모 환경**에 적합

**2. 분산 스위치 (Distributed Switch, vDS)**

​	•	**vCenter를 통해 여러 ESXi 호스트에 중앙 집중식 관리**

​	•	**하나의 vDS에 최대 2,000개의 ESXi 호스트 연결 가능**

​	•	**모든 연결된 호스트에서 일관된 네트워크 설정 유지**

​	•	vDS를 사용하려면 **Enterprise Plus 라이선스** 또는 **vSAN 클러스터 구성 필요**

​	•	**대규모 데이터 센터 환경에서 효과적**

**결론**

​	•	**소규모 환경**에서는 **표준 스위치(vSS)** 가 적합하며, 개별 ESXi 호스트마다 직접 설정해야 합니다.

​	•	**대규모 환경**에서는 **분산 스위치(vDS)** 를 활용하면 여러 ESXi 호스트에서 일관된 네트워크 구성이 가능하며, 효율적인 네트워크 관리가 가능합니다.

------

------

## **표준 스위치(Standard Switch)와 분산 스위치(Distributed Switch) 공통 기능**



표준 스위치(vSS)와 분산 스위치(vDS)는 여러 네트워크 기능을 공유하며, 아래 기능을 모두 지원합니다.



**공유 기능**

| **기능**                                               | **표준 스위치 (vSS)** | **분산 스위치 (vDS)** |
| ------------------------------------------------------ | --------------------- | --------------------- |
| **Layer 2 스위치**                                     | ✅                     | ✅                     |
| **VLAN 세분화 (Segmentation)**                         | ✅                     | ✅                     |
| **802.1Q 태깅**                                        | ✅                     | ✅                     |
| **IPv4 및 IPv6 지원**                                  | ✅                     | ✅                     |
| **NIC 팀 구성(NIC Teaming)**                           | ✅                     | ✅                     |
| **아웃바운드 트래픽 쉐이핑(Outbound Traffic Shaping)** | ✅                     | ✅                     |

**설명**

​	•	**Layer 2 스위치**: OSI 2계층에서 작동하며, MAC 주소를 기반으로 패킷을 전달

​	•	**VLAN 세분화**: 동일한 네트워크에서 논리적으로 트래픽을 분리하여 보안과 관리 향상

​	•	**802.1Q 태깅**: VLAN 태깅을 통해 여러 VLAN을 동일한 물리적 네트워크에서 처리

​	•	**IPv4 및 IPv6 지원**: 네트워크 환경에서 최신 프로토콜 지원

​	•	**NIC 팀 구성(NIC Teaming)**: 여러 NIC을 결합하여 대역폭 확장 및 장애 조치(HA) 제공

​	•	**아웃바운드 트래픽 쉐이핑**: 네트워크 대역폭을 조절하여 과부하 방지



**결론**

​	•	표준 스위치(vSS)와 분산 스위치(vDS)는 기본적인 네트워크 기능을 동일하게 제공

​	•	**규모가 작은 환경**에서는 표준 스위치가 적합

​	•	**대규모 네트워크 환경**에서는 **추가적인 중앙 관리 기능**이 있는 **분산 스위치(vDS)** 사용이 효과적

------

------

**분산 스위치(Distributed Switch) 기능**



분산 스위치는 표준 스위치(Standard Switch)보다 다양한 고급 기능을 제공합니다.

여러개의 스위치를 하나로 묶는 것

| **기능**                                                     | **표준 스위치** | **분산 스위치** |
| ------------------------------------------------------------ | --------------- | --------------- |
| **인바운드 트래픽 쉐이핑(Inbound Traffic Shaping)**          | ❌               | ✅               |
| **구성 백업 및 복원(Configuration Backup and Restore)**      | ❌               | ✅               |
| **프라이빗 VLAN(Private VLANs)**                             | ❌               | ✅               |
| **링크 집계 제어 프로토콜(Link Aggregation Control Protocol, LACP)** | ❌               | ✅               |
| **데이터센터 수준 관리(Data Center Level Management)**       | ❌               | ✅               |
| **vSphere vMotion 네트워크 상태 마이그레이션**               | ❌               | ✅               |
| **네트워크 I/O 제어(Network I/O Control)**                   | ❌               | ✅               |
| **포트별 정책 설정(Per-Port Policy Settings)**               | ❌               | ✅               |
| **포트 상태 모니터링(Port State Monitoring)**                | ❌               | ✅               |
| **NetFlow(네트워크 흐름 분석)**                              | ❌               | ✅               |
| **포트 미러링(Port Mirroring)**                              | ❌               | ✅               |
| **VMware NSX 지원(Support for NSX)**                         | ❌               | ✅               |

**설명**

​	•	**인바운드 트래픽 쉐이핑**: 들어오는 네트워크 트래픽을 제어하여 대역폭을 최적화

​	•	**구성 백업 및 복원**: 스위치 설정을 저장하고 복원 가능하여 관리 용이

​	•	**프라이빗 VLAN**: VLAN 내부에서 논리적 분할을 제공하여 보안 강화

​	•	**LACP (링크 집계)**: 여러 물리적 NIC을 결합하여 네트워크 성능 및 가용성 향상

​	•	**데이터센터 수준 관리**: 여러 ESXi 호스트에서 일관된 네트워크 구성을 유지

​	•	**vMotion 네트워크 상태 마이그레이션**: VM 이동 시 네트워크 설정도 함께 마이그레이션 가능

​	•	**네트워크 I/O 제어**: 네트워크 리소스 할당을 동적으로 조정

​	•	**포트별 정책 설정**: 특정 포트에 개별적인 정책 적용 가능

​	•	**포트 상태 모니터링**: 네트워크 트래픽 상태를 실시간으로 감지 및 분석

​	•	**NetFlow**: 네트워크 트래픽 흐름을 모니터링하여 분석 가능

​	•	**포트 미러링**: 네트워크 트래픽을 복제하여 보안 및 성능 분석에 활용

​	•	**VMware NSX 지원**: 가상 네트워크 및 보안 기능과의 통합 지원

**결론**

​	•	**표준 스위치(Standard Switch)**: 단순한 네트워크 환경에 적합

​	•	**분산 스위치(Distributed Switch)**: 대규모 환경 및 고급 네트워크 관리 기능이 필요한 경우 필수



분산 스위치는 고급 네트워크 기능과 중앙 집중식 관리를 지원하여, **대규모 vSphere 환경에서 강력한 네트워크 제어 및 최적화 기능을 제공**합니다.

------

------

## Types of Virtual Switch Connections

![image-20250304124726856](/assets/cisco_post_img/2025-03-04- Module 5: Configuring vSphere Networking//image-20250304124726856.png)

이 이미지는 가상 스위치(Virtual Switch)의 다양한 포트 유형과 연결 방식을 보여줍니다.



**가상 스위치의 주요 연결 유형:**

1. **VM 포트(VM Ports)** - 그룹 포트

​	•	가상 머신(VM)이 네트워크에 연결되는 포트

​	•	VM끼리 또는 물리적 네트워크와 통신 가능

2. **VMkernel 포트(VMkernel Ports)** - 하이퍼바이저용 포트

​	•	ESXi 호스트에서 관리 및 특수 네트워크 서비스(IP 스토리지, vMotion, vSAN 등)를 처리하는 포트

3. **IP 스토리지, vSphere vMotion, vSphere Fault Tolerance, vSAN, vSphere Replication 및 ESXi 관리 네트워크**

​	•	VMkernel 포트를 사용하여 네트워크 기반의 데이터 전송 및 관리를 수행

4. **업링크 포트(Uplink Ports)**

​	•	물리적 네트워크 어댑터(NIC)와 가상 스위치를 연결

​	•	외부 네트워크와의 통신을 담당

**포트 그룹(Port Groups) 개념:**

​	•	**VM 포트 및 VMkernel 포트는 포트 그룹 내에서 구성**되며, 각각의 목적에 맞게 설정됨.

​	•	예를 들어, **Production, TestDev, DMZ** 같은 VM 포트 그룹과 **vSphere vMotion, Management** 같은 VMkernel 포트 그룹이 있음.



이러한 개념을 이해하면 ESXi 및 vSphere 환경에서 네트워크를 효과적으로 구성하고 관리할 수 있습니다.

------

------

## Virtual Switch Connection Examples

![image-20250304125435172](/assets/cisco_post_img/2025-03-04- Module 5: Configuring vSphere Networking//image-20250304125435172.png)

이 이미지는 가상 스위치(Virtual Switch)의 다양한 연결 방식과 설계 예제를 보여줍니다.

**가상 스위치 연결 예제**

1. **단일 가상 스위치 사용**

​	•	하나의 가상 스위치 내에서 여러 개의 네트워크(포트 그룹)가 공존 가능

​	•	VLAN을 사용하여 네트워크를 논리적으로 분리할 수 있음

​	•	제한된 물리적 네트워크 어댑터(NIC)를 효율적으로 활용 가능

​	2.	**여러 개의 가상 스위치 사용**

​	•	각 네트워크를 별도의 가상 스위치로 분리 가능

​	•	네트워크 트래픽을 물리적으로 분리하여 보안 및 성능을 강화할 수 있음

​	•	충분한 물리적 NIC가 필요함



**네트워크 설계 고려사항**

​	•	**물리적 NIC 수량**

충분한 NIC가 있다면 네트워크를 개별 가상 스위치로 분리 가능하지만, NIC가 부족하면 VLAN을 활용하여 하나의 가상 스위치에서 여러 네트워크를 운영 가능함.

​	•	**보안 및 성능 요구사항**

중요한 네트워크(예: 관리 네트워크, vMotion, 스토리지 네트워크)는 개별 가상 스위치로 구성하는 것이 일반적임.

​	•	**VLAN을 통한 네트워크 격리**

하나의 가상 스위치에서 여러 네트워크를 운영해야 하는 경우, VLAN을 활용하여 트래픽을 논리적으로 분리 가능함.



이러한 가상 스위치 설계 방법을 활용하면, 환경에 맞는 최적의 네트워크 구성을 구축할 수 있습니다.

물리 lan 카드 갯수

------

------

## About VLANs and Virtual Switch Tagging

![image-20250304131421909](/assets/cisco_post_img/2025-03-04- Module 5: Configuring vSphere Networking//image-20250304131421909.png)

**VLAN과 가상 스위치 태깅(VST, Virtual Switch Tagging)**



ESXi는 **802.1Q VLAN 태깅**을 지원하며, 포트 그룹에 VLAN ID를 할당하여 VLAN을 구현할 수 있습니다. 가상 스위치 태깅(VST)은 여러 VLAN을 하나의 물리적 네트워크 연결에서 처리할 수 있도록 하는 방식입니다.

**1. VLAN을 통한 네트워크 분리**

​	•	각 포트 그룹에 VLAN ID를 설정하여 논리적으로 네트워크를 분리할 수 있습니다.

​	•	같은 VLAN ID를 사용하는 가상 머신(VM)들끼리는 동일한 물리적 LAN에 연결된 것처럼 동작합니다.

**2. 가상 스위치 태깅(VST) 방식**

​	•	**VM에서 전송된 프레임이 가상 스위치를 통과할 때 태그(VLAN ID)가 추가됨**

​	•	**반대로, 태그가 포함된 프레임이 VM으로 전달되기 전에 태그를 제거(untagging)**

​	•	**VLAN 태깅 작업이 가상 스위치에서 이루어지므로 VM에서는 VLAN을 인식할 필요 없음**

​	•	**물리적 스위치 포트는 트렁크(Trunk) 포트로 설정되어야 함**

**3. VLAN 사용의 장점**

✔ **논리적인 네트워크 구성**

물리적인 네트워크 구성과 무관하게 VLAN을 통해 네트워크를 논리적으로 분할할 수 있습니다.

✔ **브로드캐스트 트래픽 감소**

같은 VLAN 내에서만 브로드캐스트가 전달되므로, 전체 네트워크의 브로드캐스트 트래픽을 줄일 수 있습니다.

✔ **비용 절감**

VLAN을 사용하면 물리적인 라우터를 추가하지 않고도 네트워크를 분할할 수 있어 비용 절감이 가능합니다.

**4. 물리적 스위치 설정 요구사항**

​	•	**ESXi가 연결된 물리적 스위치 포트는 트렁크(Trunk) 포트로 설정되어야 함**

​	•	**트렁크 포트는 여러 VLAN을 처리할 수 있도록 구성되어 있어야 함**

​	•	**가상 스위치(Virtual Switch)에서 VLAN ID가 태깅되고, 물리적 네트워크를 통해 태그가 유지됨**

**5. 구현 예시**

​	•	Production VLAN(105)과 Test VLAN(106)을 하나의 가상 스위치에서 처리

​	•	각 포트 그룹이 별도의 VLAN ID를 가짐

​	•	VLAN ID가 지정된 포트 그룹은 서로 통신할 수 없음

​	•	트렁크 포트를 통해 물리적 네트워크에 연결됨

VLAN을 사용하면 네트워크를 유연하게 관리할 수 있으며, 가상화 환경에서 논리적으로 격리된 네트워크를 구축하는 데 매우 유용합니다.

------

------

## Viewing Standard Switches

![image-20250304144233690](/assets/cisco_post_img/2025-03-04- Module 5: Configuring vSphere Networking//image-20250304144233690.png)

------

------

## Adding Standard Switches

![image-20250304144251206](/assets/cisco_post_img/2025-03-04- Module 5: Configuring vSphere Networking//image-20250304144251206.png)

------

------

## VMkernel Adapter Properties

![image-20250304144438155](/assets/cisco_post_img/2025-03-04- Module 5: Configuring vSphere Networking//image-20250304144438155.png)

------

## VMkernel Adapter Properties: Enabled Services

![image-20250304144630337](/assets/cisco_post_img/2025-03-04- Module 5: Configuring vSphere Networking//image-20250304144630337.png)

**VMkernel Adapter 에서 서비스를 활성화 할 수 있음**

------

------

## Physical Adapter Properties

![image-20250304144930220](/assets/cisco_post_img/2025-03-04- Module 5: Configuring vSphere Networking//image-20250304144930220.png)

**물리 어댑터 패널은 속도, 듀플렉스 및 네트워크와 같은 어댑터 세부 정보를 표시한다.**

**속도 및 듀플렉스 설정은 구성할 수 있지만, 최상의 방법은 자동 협상(Auto Negotiation)으로 설정한다.**