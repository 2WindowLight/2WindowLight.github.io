---
title: <h0>Module 1 VMware vSphere Install, Configure, Manage</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-27-cisco]
tags: [Network, Cloud]







---

# Module 1: VMware vSphere: Install, Configure, Manage [V8]



------

**Importance(중요성)**

vSphere 관리자로서 **vSphere 구성 요소와 리소스를 이해하고, 이들이 환경에서 어떻게 상호 작용하는지**에 대한 지식이 필요하다.

또한, **vSphere 환경을 설치, 배포, 관리하는 실무 능력**이 요구된다.

이러한 지식과 기술을 습득함으로써 **고도로 확장 가능한 vSphere 가상 인프라를 구축하고 운영할 수 있다**.



------

### **학습 목표**

​	1.	**ESXi 호스트 설치 및 구성**

​	2.	**vCenter 배포 및 구성**

​	3.	**vSphere Client를 사용하여 vCenter 인벤토리 생성 및 vCenter 사용자 역할 할당**

​	4.	**vSphere 표준 스위치 및 분산 스위치를 사용하여 가상 네트워크 생성 및 구성**

​	5.	**vSphere에서 지원하는 스토리지 기술을 활용하여 데이터스토어 생성 및 구성**

​	6.	**vSphere Client를 사용하여 가상 머신, 템플릿(틀), 클론, 스냅샷 생성**

​	7.	**가상 머신 배포를 위한 콘텐츠 라이브러리 생성**

​	8.	**가상 머신의 리소스 사용량 관리**

------

### **설정 및 관리 목표**

​	•	**vSphere vMotion 및 vSphere Storage vMotion 마이그레이션을 지원하도록 ESXi 호스트 구성**

​	•	**vSphere 고가용성(HA) 및 vSphere 분산 리소스 스케줄러(DRS)가 활성화된 vSphere 클러스터 생성 및 구성**

​	•	**vSphere 라이프사이클을 관리하여 vCenter 및 ESXi 호스트를 최신 상태로 유지**

**vMotion : 가상 머신을 넘기는것**

**클러스터 : 그룹으로 묶는 것 for High Availabilty**

**분산 리소스 스케줄러(DRS, Distributed Resource Scheduler)** -  분산 리소스 스케줄러(DRS)는 **VMware vSphere 환경에서 가상 머신(VM)의 CPU 및 메모리 사용량을 자동으로 조정하는 기능**이다.

클러스터 내에서 리소스를 균형 있게 분배하여 **워크로드를 최적화하고 성능을 향상**시킨다.

------

### 기타

**datastore : storage**