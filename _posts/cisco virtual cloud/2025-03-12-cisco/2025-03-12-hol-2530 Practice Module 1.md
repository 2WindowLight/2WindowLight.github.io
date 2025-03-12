---
title: <h0>hol-2530 Practice Module 1</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-12-cisco]
tags: [Network, Cloud]









---

# Module 1 - Consistent ESXi Deployment with Cluster Quickstart



------

## **Cluster QuickStart 개념**

Cluster QuickStart는 **vSphere에서 클러스터를 신속하게 생성 및 구성하고, 확장할 수 있도록 지원하는 기능**이다.

이를 통해 관리자는 **HCI(하이퍼컨버지드 인프라) 클러스터의 배포 및 운영을 간소화**할 수 있다.



------

### ** Cluster QuickStart의 주요 개념**

1. **클러스터 생성 및 초기 설정 자동화**
   * **vSphere HA, DRS, vSAN** 등의 핵심 서비스 설정을 간편하게 구성 가능
   * 자동화된 UI 기반 워크플로우 제공

2. **호스트 추가 및 구성 일관성 유지**
   * 새로운 호스트를 클러스터에 추가할 때 **기존 설정을 자동으로 적용**
   * **네트워크 및 스토리지 구성 자동화**

3. **하드웨어 및 소프트웨어 호환성 검증**
   * VMware **하드웨어 호환성 목록(HCL)** 및 소프트웨어 요구 사항 확인
   * **vSphere Distributed Switch(VDS) 및 네트워크 설정 자동화**

4. **구성 완료 후 상태 점검 수행**
   * **VMware Validated Design(VVD) 권장 사항 적용**
   * 설정 완료 후 **최적의 성능과 안정성을 보장하는지 자동 검사**

> [!IMPORTANT]
>
>  Cluster QuickStart는 **HCI 클러스터의 배포 및 확장을 자동화하는 기능**으로,
>
> 클러스터 운영의 **속도, 효율성, 안정성**을 크게 향상시킨다.
>
> 이를 통해 관리자는 최소한의 수작업으로 **VMware 환경을 최적의 상태로 유지**할 수 있다.

------



## **Cluster QuickStart 개요 및 주요 기능**

이 모듈에는 **vSphere Cluster QuickStart**를 활용한 **클러스터 구성 및 관리** 과정이 포함되어 있다.

QuickStart는 HCI(하이퍼컨버지드 인프라) 클러스터를 **쉽고 빠르게 구축하고 확장하는 방법**을 제공한다.



------



1. **모듈 구성**
   * **레슨 1**: 클러스터 생성
   * **레슨 2**: 새 클러스터에 호스트 추가
   * **레슨 3**: 클러스터 구성
   * **레슨 4**: 이미지 만들기



------



**2. Cluster QuickStart 개요**

* **vCenter에서 Cluster QuickStart 도입**
  * 클러스터 구성을 간소화하고 모든 호스트에서 **일관성 유지** 지원
  * VMware 모범 사례 기반의 **자동 검사 및 권장 사항 제공**
  * 클러스터의 안정성과 성능 보장

* **자동화 기능 제공**
  * **vSphere Client UI**에서 탐색 가능
  * **vSphere API**를 활용한 자동화 지원



------



**3. Cluster QuickStart의 주요 기능**

* **하드웨어 및 소프트웨어 일관성 유지**
  * 모든 호스트가 동일하거나 유사한 하드웨어 구성을 갖도록 권장
  * 일관된 클러스터 환경을 구축하여 성능 및 안정성 향상

* **구성 시간 단축**

* **vSphere HA 및 vSAN 설정 자동화**

* **Virtual Distributed Switch 배포** 및 vMotion, vSAN 네트워크 설정

* **vSAN 확장 클러스터 또는 장애 도메인 구성 지원**

* **NTP(Network Time Protocol) 설정 자동화**

* **상태 검사 및 VMware 권장 사항 적용**

* 클러스터 설정 완료 후 **상태 점검 수행**

* **VMware Validated Design 권장 사항 자동 적용**

* **기존 클러스터 확장 지원**

* 기존 클러스터에 **새 호스트 추가 가능**

* 일관된 구성을 유지하면서 클러스터 확장 가능



------



**4. Cluster QuickStart의 장점**

​	•	**빠르고 효율적인 클러스터 구축**

​	•	**일관된 설정 유지 및 관리 용이성 향상**

​	•	**안정성과 성능 최적화**

​	•	**스크립팅 없이 간편한 UI 활용 가능**



**결론**: Cluster QuickStart는 **HCI 클러스터 배포 및 확장에 최적화된 기능**을 제공하여 **속도, 일관성, 안정성을 보장**하는 강력한 도구이다.

------

## Practice

------

### Step 1

![image-20250312150745667](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312150745667.png)

------

### Step 2

![image-20250312151017796](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312151017796.png)

- **vSphere DRS = 비활성화됨**
- **vSphere HA = 비활성화됨**
- **vSAN = 비활성화됨**

------

### Step 3

![image-20250312160430848](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312160430848.png)

------

### Step 4

![image-20250312161727914](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312161727914.png)

------

### Step 5

![image-20250312162133388](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312162133388.png)

------

### Step 6

![image-20250312162213793](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312162213793.png)

랩 환경에서는 이미 필요한 모든 분산 스위치가 있으므로 이 구성을 건너뛸 수 있는 옵션을 선택합니다. 

그러나 이 페이지에서는 필요한 경우 클러스터에 추가 분산 스위치를 추가할 수 있는 옵션이 있습니다. 이 프로세스는 환경에서 분산 스위치를 만드는 것과 매우 유사합니다. vDS를 만들려면 이름을 제공하고 물리적 어댑터를 선택해야 합니다.

------

### Step 7

![image-20250312162410016](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312162410016.png)

호스트 옵션에서 클러스터의 호스트가 나머지 환경과 시간 동기화를 갖도록 NTP 서버 정보를 제공해야 합니다.

------

### Step 8

![image-20250312163009376](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312163009376.png)

------

------

## 이미지 생성 -QuickStart로 새 클러스터를 구성했으므로 클러스터 이미지가 첨부된 클러스터를 만드는 과정

### Step 1

![image-20250312163422124](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312163422124.png)

------

### Step 2

![image-20250312163532691](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 1//image-20250312163532691.png)

------

