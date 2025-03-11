---
title: <h0>Module 9 Deploying and Configuring vSphere Clusters</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-11-cisco]
tags: [Network, Cloud]







---

# vSphere Clusters Overview

------

**vSphere 클러스터 개요**



vSphere에서 클러스터는 ESXi 호스트 그룹 간의 물리적 자원을 공유하는 데 사용된다. vCenter는 클러스터 자원을 단일 리소스 풀로 관리하며, 클러스터를 목적에 따라 여러 개 생성할 수 있다.



**1. 클러스터의 목적**



클러스터는 사용 목적에 따라 다음과 같이 구분될 수 있다.

​	•	**Management(관리용)**

​	•	**Production(운영 환경)**

​	•	**Compute(연산 및 컴퓨팅 자원 제공)**



**2. 클러스터의 최대 용량**

​	•	하나의 클러스터는 **최대 96개의 ESXi 호스트**를 포함할 수 있다.

​	•	**vSphere 7 Update 1 이상**이 필요하다.



**3. 예제 화면 설명**



위 그림에서는 **다섯 개의 클러스터**가 표시되어 있다.

​	•	**SA-Compute-01**

​	•	**SA-Compute-02**

​	•	**SA-Management**

​	•	**SB-Development**

​	•	**SB-Management**



각 클러스터 내에는 여러 ESXi 호스트와 가상 머신(VM)이 배치되어 있으며, vSphere 클러스터를 통해 이러한 자원이 중앙에서 관리된다.

------

------

## Creating a vSphere Cluster

![image-20250311113150963](/assets/cisco_post_img/9장//image-20250311113150963.png)

------

------

**클러스터 퀵스타트(Cluster Quickstart) 개요**

![image-20250311115230950](../../../assets/cisco_post_img/9장//image-20250311115230950.png)

클러스터를 생성한 후, 클러스터 퀵스타트(Cluster Quickstart) 워크플로우를 사용하여 클러스터를 구성할 수 있다.



------



​	1.	클러스터 퀵스타트 기능

​	•	클러스터 배포 프로세스를 안내하는 자동화된 구성 워크플로우이다.

​	•	호스트, 네트워크 및 vSphere 설정을 포함한 초기 구성을 지원한다.

​	•	클러스터 확장을 위해 추가 호스트를 쉽게 추가할 수 있다.

​	•	클러스터 구성 시간을 단축할 수 있다.

​	2.	클러스터 퀵스타트에서 수행하는 작업

​	•	vSphere HA, vSphere DRS, vSAN 등의 서비스 설정

​	•	하드웨어 및 소프트웨어 호환성 확인

​	•	vSphere Distributed Switches 배포

​	•	vSphere vMotion 및 vSAN을 위한 네트워크 설정

​	•	vSAN 확장 클러스터 또는 vSAN 장애 도메인 생성

​	•	클러스터 전반에 걸친 NTP 설정 일관성 보장

​	3.	추가 정보

​	•	클러스터 생성 및 구성에 대한 자세한 내용은 vCenter Server 및 호스트 관리 문서 참고:

[VMware vSphere 공식 문서](https://docs.vmware.com/en/VMware-vSphere/index.html)



------



클러스터 퀵스타트를 활용하면 효율적인 클러스터 배포 및 확장이 가능하며, 관리자가 설정해야 할 주요 항목들을 자동화하여 시간을 절약할 수 있다.

------

------

## Cluster Quickstart: Activating Services

![image-20250311115244017](../../../assets/cisco_post_img/9장//image-20250311115244017.png)

------

------

## Cluster Quickstart: Adding Hosts

![image-20250311115258222](../../../assets/cisco_post_img/9장//image-20250311115258222.png)

------

------

## Cluster Quickstart: Configuring the Cluster

![image-20250311115311112](../../../assets/cisco_post_img/9장//image-20250311115311112.png)

------

------

## Configuring a Cluster: Distributed Switches

![image-20250311115334141](../../../assets/cisco_post_img/9장//image-20250311115334141.png)

------

------

## Configuring a Cluster: vSAN and vMotion Traffic

![image-20250311115353652](../../../assets/cisco_post_img/9장//image-20250311115353652.png)

------

------

## Configuring a Cluster: Advanced Options

![image-20250311120140388](/assets/cisco_post_img/9장//image-20250311120140388.png)

------

------

## Viewing Cluster Summary Information

![image-20250311120155340](/assets/cisco_post_img/9장//image-20250311120155340.png)

------

------

