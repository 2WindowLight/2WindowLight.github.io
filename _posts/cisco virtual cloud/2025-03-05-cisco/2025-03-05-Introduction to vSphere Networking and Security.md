---
title: <h0>Introduction to vSphere Networking and Security </h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-05-cisco]
tags: [Network, Cloud]













---



# Introduction to vSphere Networking and Security 

------

## **vSphere Virtual Switch**

**1. 개요 (Introduction)**

​	•	가상 머신(VM)이 **vSphere 하이퍼바이저에서 논리적 스위치를 통해 물리 네트워크와 통신**할 수 있도록 해야 함.

​	•	기존에는 **각 ESXi 호스트에서 개별적으로 Standard vSwitch(VSS)를 구성**하여 네트워크를 설정했음.

​	•	**vSphere Distributed Switch(VDS)**는 **vCenter에서 중앙 집중식으로 관리할 수 있어** 현재 대부분의 네트워크 트래픽에 권장되는 방식이 됨.



**2. 가상 스위치의 종류 (Types of Virtual Switches)**

​	•	**vSphere Standard Switch (VSS)**

→ ESXi 호스트별로 개별 구성해야 하는 표준 가상 스위치.

​	•	**vSphere Distributed Switch (VDS)**

→ **vCenter에서 중앙 관리할 수 있으며, 더 많은 네트워크 기능을 제공하는 고급 스위치**.

**요약**

​	•	**기존 방식**: ESXi 호스트별로 **Standard vSwitch(VSS)를 개별적으로 구성**해야 했음.

​	•	**현재 권장 방식**: **vSphere Distributed Switch(VDS)를 사용하여 vCenter에서 중앙 집중식 관리** 가능.

​	•	**주요 장점**: **VDS는 고급 네트워크 기능을 제공하며, 데이터센터에서 더 효율적인 네트워크 운영을 지원**.

------

------

## **vSphere Standard Switch (VSS)**

​	•	**물리적 이더넷 스위치와 유사한 동작 방식**

→ VM이 연결된 **가상 포트를 감지하고** 해당 정보를 기반으로 **트래픽을 올바른 VM으로 전달**함.

​	•	**물리 네트워크와 연결 가능**

→ **업링크 어댑터(Physical NIC)를 통해 물리 네트워크와 연결**하여 물리 스위치처럼 확장 가능.

​	•	**개별 ESXi 호스트에서 독립적으로 관리**

→ **각 ESXi 호스트에서 개별적으로 설정해야 하며, 일관성 유지가 어려움**.

​	•	**고급 네트워크 기능이 부족**

→ 물리 스위치처럼 동작하지만, **고급 네트워크 기능이 제한적**.

​	•	**vMotion을 수행하려면 관리자가 수동으로 설정을 맞춰야 함**

→ 모든 ESXi 호스트에서 일관된 네트워크 구성을 유지하려면 **수동 유지 관리 필요**.

------

------

## **vSphere Distributed Switch (VDS)**

​	•	**단일 스위치처럼 동작하며 모든 연결된 ESXi 호스트에서 동일한 설정 유지**

→ **데이터센터 전체의 가상 네트워크를 중앙에서 프로비저닝, 관리 및 모니터링** 가능.

​	•	**vCenter에서 중앙 집중식으로 관리**

→ vCenter에서 설정하면 **연결된 모든 ESXi 호스트에 자동으로 적용**, 별도의 개별 설정이 불필요함.

​	•	**VM이 여러 호스트 간에 마이그레이션될 때도 네트워크 설정 유지**

→ VM이 이동하더라도 **일관된 네트워크 구성을 유지**할 수 있음.

​	•	**ESXi 6.x, 7.x, 8.x 버전에서 지원**

→ VDS에 포함된 ESXi 호스트는 **별도 추가 설정 없이 자동으로 일관성을 유지**함.

​	•	**vSphere 8.0부터 vSphere Distributed Services Engine(vDSE) 도입**

→ **DPU(SmartNIC)를 활용하여 호스트 CPU의 네트워크 기능을 DPU로 오프로드**, 인프라 성능 향상.

**비교 요약**

| **항목**             | **vSphere Standard Switch (VSS)**  | **vSphere Distributed Switch (VDS)**               |
| -------------------- | ---------------------------------- | -------------------------------------------------- |
| **관리 방식**        | ESXi 호스트별 개별 관리            | vCenter에서 중앙 관리                              |
| **네트워크 구성**    | 수동으로 설정 유지 필요            | 설정이 모든 호스트에 자동 적용                     |
| **가상 머신 이동**   | vMotion 시 수동 네트워크 설정 필요 | VM 마이그레이션 시 네트워크 설정 유지              |
| **고급 기능 지원**   | 제한적                             | 네트워크 모니터링, 트래픽 쉐이핑 등 고급 기능 지원 |
| **vSphere 8.0 특징** | 기존 방식 유지                     | DPU(SmartNIC) 활용 가능                            |

**결론**

​	•	**소규모 환경** → **VSS(표준 스위치) 사용 가능**하지만, **일관성 유지가 어려움**.

​	•	**대규모 환경** → **VDS(분산 스위치) 사용 추천**, **중앙 관리 및 고급 네트워크 기능 제공**.

​	•	**vSphere 8.0 이상** → **DPU를 활용한 vSphere Distributed Services Engine(vDSE) 도입 가능**, 성능 향상 가능.

------

------

## **vSS vs. vDS 아키텍처 비교**

![image-20250305130229527](/assets/cisco_post_img/2025-03-05-Introduction to vSphere Networking and Security//image-20250305130229527.png)





**1. vSphere Standard Switch (vSS) 아키텍처**

​	•	**물리 NIC(uplink)와 가상 머신(vNIC) 연결 방식**

​	•	ESXi 호스트의 **물리 NIC(vmnics)를 vSS 업링크 포트에 연결**하여 네트워크를 제공함.

​	•	가상 머신(VM)의 **가상 NIC(vNIC)는 vSS의 포트 그룹에 연결**됨.

​	•	**포트 그룹(port group) 및 네트워크 트래픽 관리**

​	•	각 포트 그룹은 **하나 이상의 물리 NIC을 사용하여 네트워크 트래픽을 처리**함.

​	•	**물리 NIC이 없는 포트 그룹은 같은 포트 그룹 내 VM 간 통신만 가능**하고 외부 네트워크 연결 불가능.

​	•	**물리적 이더넷 스위치와 유사한 동작 방식**

​	•	각 VM과 물리 NIC는 **표준 스위치의 논리 포트에 연결**됨.

​	•	각 논리 포트는 하나의 포트 그룹에 속하며, 특정 네트워크 정책을 따름.

**2. vSphere Distributed Switch (vDS) 아키텍처**

​	•	**vCenter에서 중앙 관리되는 단일 논리 스위치**

​	•	여러 ESXi 호스트를 포함하는 **단일 논리 스위치 역할 수행**.

​	•	vCenter에서 관리하며 **구성이 모든 ESXi 호스트에 자동으로 적용**됨.

​	•	**업링크 및 포트 그룹 관리 방식**

​	•	**업링크 포트 그룹(uplink port group)**: 여러 물리 NIC을 하나의 업링크로 묶어 부하 분산 가능.

​	•	**분산 포트 그룹(Distributed Port Group, DPG)**: 모든 호스트에서 **일관된 네트워크 정책 유지** 가능.

​	•	**고급 네트워크 기능 지원**

​	•	vMotion, QoS, 트래픽 쉐이핑, 포트 미러링, NetFlow 같은 **고급 네트워크 기능 제공**.

​	•	vSphere 8에서는 **DPU(SmartNIC)를 이용한 오프로드 기능 추가**.

**비교 요약**

| **항목**           | **vSphere Standard Switch (vSS)**   | **vSphere Distributed Switch (vDS)**              |
| ------------------ | ----------------------------------- | ------------------------------------------------- |
| **관리 방식**      | 각 ESXi 호스트에서 개별 설정        | vCenter에서 중앙 관리                             |
| **설정 적용 범위** | 개별 호스트마다 수동 적용           | 모든 호스트에 자동 동기화                         |
| **포트 그룹 구성** | 각 ESXi에서 개별 포트 그룹 관리     | 모든 호스트에서 동일한 포트 그룹 사용             |
| **업링크 사용**    | 개별 포트 그룹마다 직접 업링크 연결 | 업링크 포트 그룹을 통해 유연한 NIC 관리           |
| **네트워크 기능**  | 기본 네트워크 연결 제공             | 트래픽 쉐이핑, 포트 미러링, QoS 등 고급 기능 지원 |
| **vMotion 지원**   | 수동 설정 필요                      | 자동으로 설정 유지                                |
| **적용 대상**      | 소규모 환경                         | 대규모 데이터센터 환경                            |

**결론**

​	•	**소규모 환경에서는 vSS를 사용해도 무방하지만, ESXi 개별 설정을 유지해야 하는 부담이 있음**.

​	•	**대규모 데이터센터에서는 vDS를 사용하는 것이 권장됨**.

​	•	**vCenter에서 중앙 관리 가능하며**, VM이 다른 호스트로 이동하더라도 네트워크 설정이 유지됨.

​	•	**vSphere 8.0 이상에서는 DPU를 활용한 성능 최적화 기능(vSphere Distributed Services Engine) 추가됨**.



254