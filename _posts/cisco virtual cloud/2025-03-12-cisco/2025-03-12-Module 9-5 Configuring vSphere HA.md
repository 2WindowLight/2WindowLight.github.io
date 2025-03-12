---
title: <h0>Configuring vSphere HA</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-12-cisco]
tags: [Network, Cloud]








---

## **vSphere HA Orchestrated Restart 개요**

![image-20250312112649350](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312112649350.png)

**Orchestrated Restart**는 vSphere HA에서 VM 재시작 우선순위 설정의 대안으로 제공되는 기능이다.

이 기능을 사용하면 특정한 **재시작 순서**를 정의할 수 있어, 서비스가 **올바른 순서로 시작되도록 보장**할 수 있다.



------



**1. vSphere HA Orchestrated Restart의 필요성**

​	•	복잡한 애플리케이션 환경에서는 **VM 간 의존성(Dependency)** 이 존재함.

​	•	예를 들어, **3계층(Three-Tier) 아키텍처**에서는 **DB → App → Web** 순으로 서비스가 시작되어야 정상적인 동작을 보장할 수 있음.

​	•	단순히 **VM 재시작 우선순위(Restart Priority)** 설정만으로는 이 의존성을 충분히 반영할 수 없음.

​	•	**Orchestrated Restart**를 사용하면 **VM 간 의존성을 정의하고, 특정 순서로 VM을 재시작할 수 있음**.



------



**2. Orchestrated Restart 작동 방식**

​	•	VM 간의 **“Depends On” (의존 관계)** 을 설정하여 재시작 순서를 정함.

​	•	**직접(Direct) 의존성만 지원**하며, 순환 의존성(Cyclic Dependency)은 설정할 수 없음.

​	•	VM이 의존하는 다른 VM이 정상적으로 시작된 후에만 다음 VM을 시작하도록 보장.



------



**3. Orchestrated Restart의 주요 특징**

​	1.	**서비스가 특정 순서로 재시작되도록 보장**

​	•	예를 들어, **DB → App → Web** 순으로 VM이 재시작되도록 설정 가능.

​	2.	**직접 의존성(Direct Dependency)만 지원**

​	•	Web → App → DB와 같은 **직접적인 의존 관계**만 설정할 수 있음.

​	•	Web → DB 같은 **간접 의존성(Indirect Dependency)** 은 자동 적용되지 않음.

​	•	순환 의존성(Circular Dependency)을 설정하면 VM 재시작이 실패할 수 있음.

​	3.	**VM 재시작 시점을 최적화**

​	•	VM이 정상적으로 작동하기 전까지 다음 VM을 재시작하지 않음.

​	•	서비스 복구 시간을 줄이고, 장애 발생 시 의존성 문제를 방지.



------



**4. Orchestrated Restart 설정 예시**



**예제: 3계층 애플리케이션(Three-Tier Application)**



**환경**:

​	•	**DB 서버** → **애플리케이션 서버** → **웹 서버** 순으로 시작되어야 함.



**설정 방식**:

​	•	**Web VM** 은 **App VM** 에 의존

​	•	**App VM** 은 **DB VM** 에 의존



**작동 순서**:

​	1.	**DB VM** 이 먼저 부팅됨.

​	2.	**App VM** 은 **DB VM이 정상적으로 시작된 후** 부팅됨.

​	3.	**Web VM** 은 **App VM이 정상적으로 시작된 후** 부팅됨.



------



**5. Orchestrated Restart vs. VM Restart Priority 차이점**

| **기능**        | **Orchestrated Restart**                      | **VM Restart Priority**                  |
| --------------- | --------------------------------------------- | ---------------------------------------- |
| **재시작 순서** | 직접적인 의존 관계를 기반으로 순차적 재시작   | 우선순위 기반 (High, Medium, Low) 재시작 |
| **의존성 설정** | VM 간 “Depends On” 관계를 설정                | 개별 VM 우선순위 설정                    |
| **순환 의존성** | 설정 불가능                                   | 고려하지 않음                            |
| **적용 방식**   | 특정 VM이 정상적으로 작동한 후 다음 VM 재시작 | 지정된 우선순위 그룹별로 동시에 재시작   |





------



**6. 결론**

​	•	**Orchestrated Restart**는 VM 간 **의존성을 고려하여 올바른 순서로 재시작**하도록 보장하는 기능.

​	•	**3계층 애플리케이션(Three-Tier App)과 같은 환경에서 유용**하며, 서비스가 정상적으로 복구될 수 있도록 지원.

​	•	**직접 의존성(Direct Dependency)만 지원**하며, 순환 의존성을 설정하면 VM이 정상적으로 재시작되지 않음.

​	•	**VM Restart Priority**와 비교했을 때 **더 세밀한 제어가 가능**하며, 특정 VM이 **완전히 준비된 후 다음 VM이 재시작되도록 보장**함.

------

------







------

------

## **Admission Control Using Cluster Resource Percentage**

![image-20250312115242926](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312115242926.png)

**Admission Control**은 vSphere HA(High Availability)에서 **호스트 장애 발생 시 필요한 리소스를 보장하기 위해 가용 리소스를 예약하는 기능**이다. 기본적으로 **클러스터 리소스 백분율(Cluster Resource Percentage)** 방식을 사용하여 **가용 리소스를 자동으로 계산하고 예약**한다.



------



**1. 클러스터 리소스 개요**



**(1) 총 클러스터 용량**

​	•	CPU: **18 GHz** (각 호스트가 6 GHz, 총 3대)

​	•	Memory: **24 GB** (각 호스트가 8 GB, 총 3대)



**(2) VM 예약 리소스**

​	•	CPU: **7 GHz** (VM들이 요구하는 총 CPU 예약량)

​	•	Memory: **6 GB** (VM들이 요구하는 총 메모리 예약량)



------



**2. Failover 용량 계산 방법**



**Failover 용량**은 클러스터에서 예약된 리소스를 제외하고 **장애 발생 시 사용할 수 있는 리소스의 비율**을 계산하는 방식이다.



**(1) 현재 Failover CPU 용량**

​	•	계산식:

**(총 CPU 용량 - VM 예약량) / 총 CPU 용량**

**(18 GHz - 7 GHz) / 18 GHz = 61%**

​	•	현재 CPU 기준으로 **61%의 가용 용량이 남아 있음**.



**(2) 현재 Failover Memory 용량**

​	•	계산식:

**(총 Memory 용량 - VM 예약량) / 총 Memory 용량**

**(24 GB - 6 GB) / 24 GB = 75%**

​	•	현재 Memory 기준으로 **75%의 가용 용량이 남아 있음**.



------



**3. Admission Control의 역할**

​	•	**호스트 장애 발생 시에도 클러스터가 정상적으로 운영되도록 보장**.

​	•	예약된 리소스를 유지하면서 **가용 리소스를 관리**.

​	•	**자동 재계산을 수행하여 변경 사항(호스트 추가/제거)에 따라 적절한 리소스를 유지**.



------



**4. 예제에서 설정된 Reserved Failover 용량**



**스크린샷에서 설정된 값:**

​	•	**Reserved failover CPU capacity: 33%**

​	•	**Reserved failover Memory capacity: 33%**



이 설정에 따르면, 클러스터는 **총 용량의 33%를 장애 발생 시 사용할 수 있도록 예약**하고, 남은 리소스 내에서 VM을 배치함. 이 값이 클수록 장애 발생 시 안정성이 높아지지만, 클러스터 내에서 운영할 수 있는 VM의 수는 줄어들게 됨.



------



**5. 결론**

​	•	**클러스터 리소스 백분율(Cluster Resource Percentage)** 방식은 **가용 리소스를 자동으로 조정**하여 장애 시 VM이 정상적으로 재배치되도록 보장.

​	•	Failover 용량을 **수동으로 설정(Override calculated failover capacity)** 할 수도 있으며, 이 경우 운영 환경에 맞게 최적의 값으로 조정해야 함.

​	•	설정된 값이 너무 낮으면 장애 발생 시 충분한 리소스를 확보하지 못할 수 있으며, 너무 높으면 클러스터의 전체 사용 가능 리소스가 제한될 수 있음.

------

------



## **Admission Control Using Slots in vSphere HA**

![image-20250312115819321](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312115819321.png)

![image-20250312115641390](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312115641390.png)



**Admission Control**은 vSphere HA(High Availability)에서 **호스트 장애 시 가용 리소스를 보장하기 위해 슬롯(Slot) 개념을 활용하는 기능**이다.



------



**1. 슬롯(Slot) 개념**

​	•	**슬롯(Slot)**은 **클러스터 내에서 가장 높은 CPU 및 메모리 예약 값을 기준으로 정의된 최소 리소스 단위**이다.

​	•	vSphere HA는 슬롯 기반 계산을 통해 클러스터에서 사용할 수 있는 가용 리소스를 평가하고, **VM을 배치할 수 있는지 결정**한다.



------



**2. 슬롯 크기(Slot Size) 계산**



**슬롯 크기는 클러스터 내 가장 높은 CPU 및 메모리 예약을 기준으로 결정됨**.



**(1) VM 리소스 예약 값**

​	•	VM1: **2 GHz / 1 GB**

​	•	VM2: **2 GHz / 1 GB**

​	•	VM3: **1 GHz / 2 GB**

​	•	VM4: **1 GHz / 1 GB**

​	•	VM5: **1 GHz / 1 GB**



**(2) 슬롯 크기 결정**

​	•	**가장 높은 CPU 예약:** **2 GHz**

​	•	**가장 높은 메모리 예약:** **2 GB**

​	•	**따라서 슬롯 크기:** **2 GHz CPU / 2 GB 메모리**



------



**3. 클러스터 슬롯 개수 계산**



**(1) 호스트별 리소스**

​	•	각 호스트:

​	•	**CPU:** 6 GHz

​	•	**Memory:** 8 GB



**(2) 슬롯 개수 계산**

​	•	슬롯 크기(2 GHz CPU / 2 GB 메모리) 기준으로, 각 호스트는 다음과 같이 슬롯을 가질 수 있음.

​	•	**CPU 슬롯:** 6 GHz ÷ 2 GHz = **3 슬롯**

​	•	**Memory 슬롯:** 8 GB ÷ 2 GB = **4 슬롯** → **더 적은 값(3 슬롯) 적용**

​	•	**각 호스트는 총 3 슬롯을 가짐**

​	•	**클러스터 전체 슬롯 개수:**

​	•	3 슬롯 × 3 호스트 = **9 슬롯**



------



**4. Admission Control 동작**

​	•	HA는 **최대 9개의 슬롯을 활용할 수 있도록 VM을 배치**.

​	•	**하나의 호스트가 장애가 나더라도 VM이 이동하여 실행될 수 있도록 가용 슬롯을 보장**.

​	•	**VM이 할당할 수 있는 슬롯이 부족하면 추가적인 VM 전원이 켜지지 않음**.



------



**5. 결론**

​	•	슬롯(Slot) 기반 방식은 **클러스터 내 가장 높은 리소스를 요구하는 VM 기준으로 리소스를 예약**함.

​	•	높은 리소스를 요구하는 VM이 있으면 **슬롯 크기가 커져서 전체 슬롯 개수가 줄어들고, 수용할 수 있는 VM 개수가 감소**할 수 있음.

​	•	**슬롯 크기를 줄이기 위해 VM의 예약 값을 낮추는 방법을 고려**할 수 있음.

 리소스 불균형일때 많이 씀

------

------

## **Admission Control Using Slots (2)**

![image-20250312120010628](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312120010628.png)

vSphere HA(High Availability)에서는 **슬롯(Slot) 기반으로 Admission Control을 수행**하여 **장애 발생 시에도 충분한 리소스를 유지**할 수 있도록 한다.



------



**1. 슬롯 기반 장애 허용 용량(Failover Capacity)**

​	•	**vSphere HA는 현재 장애 허용 용량을 계산하여 클러스터 내에서 허용 가능한 장애 수준을 결정**.

​	•	클러스터의 **각 호스트는 3개의 슬롯을 제공**.

​	•	**전체 클러스터 슬롯 개수:**

​	•	3 슬롯 × 3 호스트 = **9 슬롯**

​	•	**현재 실행 중인 VM:** 5개

​	•	5개 슬롯 사용 중



------



**2. 장애 발생 시 슬롯 동작**



**(1) 호스트 1개 장애**

​	•	**남은 슬롯 개수:**

​	•	전체 슬롯(9) - 장애 호스트 슬롯(3) = **6 슬롯**

​	•	**현재 실행 중인 VM(5개) 배치 가능 → 정상 작동**



**(2) 호스트 2개 장애**

​	•	**남은 슬롯 개수:**

​	•	전체 슬롯(9) - 장애 호스트 슬롯(6) = **3 슬롯**

​	•	**현재 실행 중인 VM(5개) 배치 불가능 → 일부 VM 실행 불가**



------



**3. Admission Control 동작**

​	•	**현재 장애 허용 용량이 설정된 허용 용량보다 낮으면, 추가 VM 전원 켜기를 제한**.

​	•	즉, **호스트 2개 이상이 장애가 발생하면, 실행할 수 있는 슬롯이 부족하여 일부 VM이 실행되지 못함**.



------



**4. 결론**

​	•	**장애 허용 용량(Failover Capacity)**은 **클러스터 내 가용 슬롯 개수에 따라 결정**.

​	•	**슬롯 개수가 충분하지 않으면, 신규 VM이 실행되지 않음**.

​	•	**장애 발생 시 실행 가능한 VM 개수를 보장하기 위해, 적절한 HA 정책을 설정해야 함**.

------

------

## **vSphere HA 설정: 성능 저하 허용 한계(Performance Degradation VMs Tolerate)**

![image-20250312120200719](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312120200719.png)

vSphere HA의 **Performance degradation VMs tolerate** 설정은 **호스트 장애 시 VM 성능 저하를 허용하는 비율**을 정의하는 값이다.



------



**1. 기능 개요**

​	•	**호스트 장애 발생 시, 클러스터 내의 VM들이 허용할 수 있는 성능 저하 수준을 지정**.

​	•	**Admission Control**과 연계하여 **사용 가능한 장애 허용 용량(Failover Capacity)**을 초과하면 경고를 발생.

​	•	**VM의 예약된 메모리와 오버헤드를 고려하여 리소스 감소를 계산**.



------



**2. 설정 값과 동작 방식**

​	•	**기본값: 100%**

​	•	**경고 없음**. VM 성능 저하에 대한 경고를 비활성화.

​	•	**0%로 설정**

​	•	**클러스터 용량이 사용량을 초과하면 경고** 발생.

​	•	**즉, 모든 VM이 기존 성능을 유지할 수 없으면 경고**.

​	•	**20%로 설정**

​	•	VM 성능이 20% 저하될 수 있도록 허용.

​	•	**성능 저하 허용 기준** = 현재 사용량 × 20%.

​	•	성능 저하 후에도 **사용량이 가용 용량을 초과하면 경고 발생**.



------



**3. 예제**

| **설정 값** | **동작 방식**                         |
| ----------- | ------------------------------------- |
| 100%        | 성능 저하에 대해 경고 없음.           |
| 50%         | 성능이 50% 이하로 떨어지면 경고 발생. |
| 0%          | 성능 저하가 발생하면 즉시 경고 발생.  |





------



**4. 추가 조건**

​	•	이 기능은 **vSphere DRS(Dynamic Resource Scheduler)가 활성화된 경우에만 사용 가능**.

​	•	설정 값을 낮추면 **클러스터 내 VM 성능 유지 기준이 엄격해져 경고 발생 가능성 증가**.



------



**5. 결론**

​	•	**100% 설정 시 VM 성능 저하에 대해 경고 없음**.

​	•	**0~100% 사이에서 사용자 환경에 맞춰 적절한 성능 저하 허용 한계를 설정**.

​	•	**DRS가 활성화된 클러스터에서만 사용 가능**하며, **클러스터 장애 시 VM 성능을 유지할 수 있도록 사전 대비 가능**.

------

------

## **vSphere HA와 vSphere DRS의 통합 사용**



vSphere HA(High Availability)와 vSphere DRS(Distributed Resource Scheduler)는 서로 밀접하게 통합되어 동작한다.



------



**1. vSphere HA와 vSphere DRS의 협업 방식**

​	•	**호스트 장애 발생 시 vSphere HA는 VM을 재시작할 수 있는 가용 리소스를 우선적으로 확인**.

​	•	**가용 리소스가 부족하면 vSphere DRS가 개입하여 VM을 최적의 호스트로 분배**.



------



**2. vSphere HA가 VM을 복구하지 못하는 경우**

​	•	**vSphere HA의 Admission Control이 비활성화**되어 있고, **남은 호스트의 리소스가 부족**하여 모든 VM을 정상적으로 시작할 수 없는 경우.

​	•	**장애 발생 후 VM이 비효율적으로 배치**되어 한 호스트에 과부하가 발생한 경우.



------



**3. vSphere DRS의 역할**

​	•	**HA가 장애 복구 후 VM을 우선 배치하지만, 부하가 특정 호스트에 집중되는 경우가 많음**.

​	•	**vSphere DRS가 부하를 분산하기 위해 VM을 다른 호스트로 자동 마이그레이션(vMotion)**.



------



**4. 결론**

​	•	vSphere HA는 **즉각적인 VM 가용성을 보장**하지만, **장애 복구 후 리소스 부하를 고려하지 않음**.

​	•	**vSphere DRS가 자동으로 부하를 조정하여 클러스터 전체의 균형을 유지**.

​	•	**두 기능을 함께 사용하면 장애 복구 및 리소스 최적화를 동시에 수행 가능**.

------

------

## **vSphere Fault Tolerance(Fault Tolerance, FT) 개요**

![image-20250312121053022](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312121053022.png)

vSphere Fault Tolerance(FT)는 **미션 크리티컬한 고성능 애플리케이션을 보호**하며, 운영 체제와 관계없이 사용할 수 있다.



------



**1. vSphere Fault Tolerance의 주요 특징**

​	•	**즉각적인 장애 복구(Failover) 및 지속적인 가용성 제공**

​	•	**0초의 다운타임, 0 데이터 손실, VM 네트워크 연결 유지**

​	•	**ESXi 호스트 기반으로 동작하며, 중요한 VM을 보호할 수 있음**



------



**2. vSphere FT의 동작 방식**

​	•	보호되는 VM은 **Primary VM**이라 하고, **Secondary VM**이 다른 호스트에서 동일한 상태로 실행됨.

​	•	**Primary VM과 Secondary VM은 실행 상태를 지속적으로 동기화하여 동일한 연산 수행**.

​	•	**Primary VM이 실행 중인 호스트 장애 발생 시, Secondary VM이 즉시 활성화**되어 서비스 지속.

​	•	**새로운 Secondary VM이 자동 생성되어 Fault Tolerance 복구 및 이중화 유지**.

​	•	**Secondary VM이 실행 중인 호스트 장애 발생 시, 즉시 새로운 Secondary VM 생성**.



------



**3. vSphere FT의 장점**

​	•	**무중단 장애 복구(Transparent Failover) 제공**

​	•	**애플리케이션 및 운영체제 무관하게 적용 가능**

​	•	**중요 업무용 VM의 지속적인 보호 가능**

​	•	**자동 복구 기능을 통해 지속적인 이중화 유지**



------



**4. vSphere FT를 사용하는 주요 사례**

​	•	**금융 거래 시스템, 병원 시스템, 제조 자동화 시스템 등** **중단이 허용되지 않는 환경**에서 사용.

​	•	**기업의 핵심 데이터베이스 및 미션 크리티컬 애플리케이션 보호**.

​	•	**네트워크 연결이 유지되어야 하는 중요 서비스 운영**.



------



**결론**



vSphere Fault Tolerance(FT)는 **VM의 지속적인 가용성을 보장**하며, **장애 발생 시 무중단으로 즉시 복구되는 강력한 기능**을 제공한다. **미션 크리티컬한 애플리케이션 보호를 위해 필수적인 기능**이다.



------

------

## **vSphere Fault Tolerance(FT) Checkpointing**

![image-20250312121314965](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312121314965.png)

vSphere Fault Tolerance(FT)에서는 **Primary VM과 Secondary VM이 동기화**되며, 이를 위해 **Checkpointing 기술**을 사용한다.



------



**1. Checkpointing이란?**

​	•	**Primary VM에서 발생하는 모든 변경 사항을 Secondary VM으로 전송하는 프로세스**.

​	•	**CPU, 메모리, 디스크, 네트워크 상태를 지속적으로 동기화**하여 두 VM이 동일한 실행 상태를 유지.

​	•	**FT 네트워크를 통해 데이터를 주기적으로 복제하여 실시간으로 동기화 유지**.



------



**2. Checkpointing의 동작 방식**

​	1.	**Primary VM이 실행 중인 상태에서 변경 사항(메모리, CPU, 디스크 I/O 등)을 캡처**.

​	2.	**캡처된 변경 사항을 FT 네트워크를 통해 Secondary VM으로 전송**.

​	3.	**Secondary VM은 전송받은 데이터를 적용하여 Primary VM과 동일한 상태를 유지**.

​	4.	**Primary VM이 장애 발생 시 Secondary VM이 즉시 활성화되어 서비스를 지속**.



------



**3. Checkpointing을 통한 무중단 서비스 제공**

​	•	Primary VM에서 생성된 변경 사항이 **바로 Secondary VM으로 복제**되므로, 장애 발생 시 **즉시 전환(Failover)** 가능.

​	•	사용자는 **어떤 장애가 발생했는지 인지하지 못할 정도로 빠르게 전환**됨.

​	•	Checkpointing이 지속적으로 동작하면서 **Primary VM과 Secondary VM이 거의 동일한 상태를 유지**.



------



**결론**



Checkpointing 기술을 통해 **Primary VM과 Secondary VM을 지속적으로 동기화**하며, 이를 통해 **무중단 장애 복구(Zero Downtime)를 실현**할 수 있다. **미션 크리티컬 환경에서 중요한 보호 기능**이다.

------

------

## **vSphere Fault Tolerance(FT)와 vSphere HA 및 vSphere DRS의 관계**

![image-20250312121517411](/assets/cisco_post_img/2025-03-12-Module 9-5 Configuring vSphere HA//image-20250312121517411.png)

vSphere Fault Tolerance(FT)는 **vSphere High Availability(HA) 및 vSphere Distributed Resource Scheduler(DRS)**와 통합되어 작동하며, 이를 통해 **장애 복구 및 리소스 최적화를 자동화**할 수 있다.



------



**1. vSphere HA와 vSphere FT**

​	•	**vSphere HA는 FT를 지원하기 위해 필수적으로 활성화되어야 함**.

​	•	**Primary VM이 실행 중인 ESXi 호스트가 장애가 발생하면, Secondary VM이 즉시 Primary 역할을 수행**.

​	•	**vSphere HA는 FT VM의 장애를 감지하고 새로운 Secondary VM을 생성**하여 지속적인 보호를 제공.



------



**2. vSphere DRS와 vSphere FT**

​	•	**vSphere DRS는 FT VM을 실행할 적절한 호스트를 선택**하지만, FT VM을 자동으로 마이그레이션하지 않음.

​	•	**FT가 활성화된 VM과 그 Secondary VM은 동일한 호스트에서 실행될 수 없음**.

​	•	이 제한은 **하나의 호스트 장애로 인해 Primary 및 Secondary VM이 동시에 손실되는 것을 방지**하기 위함.

​	•	**vSphere vMotion을 사용하여 동일한 호스트로 마이그레이션할 수 없음**.



------



**3. 장애 발생 시 동작 방식**

​	•	Primary VM이 실행 중인 호스트가 장애가 발생하면, **Secondary VM이 즉시 Primary로 전환됨**.

​	•	**vSphere HA는 새로운 Secondary VM을 생성하여 Fault Tolerance 보호를 재구축**함.

​	•	새로운 Secondary VM은 **현재 클러스터 내에서 가용한 다른 호스트에 배치됨**.



------



**결론**

​	•	**vSphere HA는 FT의 필수 구성 요소**이며, 장애 발생 시 **자동 복구를 수행**함.

​	•	**vSphere DRS는 FT VM의 배치를 최적화**하지만, 자동 마이그레이션을 수행하지 않음.

​	•	**FT 환경에서는 Primary 및 Secondary VM이 동일한 호스트에서 실행되지 않도록 보장**하여 장애 복구 시 높은 가용성을 유지할 수 있음.

------

------

## **vSphere Fault Tolerance(FT) 주요 기능**



vSphere Fault Tolerance(FT)는 **무중단 장애 복구를 제공하는 기능**으로, **중요한 가상 머신(VM)의 연속적인 가용성을 보장**한다.



------



**1. Fault Tolerant VM의 스펙**

​	•	**최대 8 vCPU 및 128GB 메모리 지원**.

​	•	**호스트당 최대 4개의 FT VM을 지원**하되, 총 **8 vCPU를 초과할 수 없음**.

​	•	예: 한 호스트에서 **2개의 4 vCPU FT VM** 또는 **4개의 2 vCPU FT VM**이 실행 가능.



------



**2. vSphere FT의 주요 기능**

​	•	**Primary 및 Secondary VM의 vSphere vMotion 마이그레이션 지원**.

​	•	**VM의 모든 파일과 디스크를 복제하여 보조(Secondary) VM 생성**.

​	•	**빠른 체크포인트 복사를 수행하여 Primary VM과 Secondary VM을 지속적으로 동기화**.



------



**3. 지원하는 VM 디스크 포맷**

​	•	**Thin Provisioning**: 필요할 때만 실제 디스크 공간을 사용.

​	•	**Thick Provision Lazy-Zeroed**: 할당된 디스크 공간을 즉시 확보하지만, 데이터는 필요할 때 초기화됨.

​	•	**Thick Provision Eager-Zeroed**: 할당된 디스크 공간을 미리 초기화하여 성능을 최적화함.



------



**4. 추가적인 FT 기능**

​	•	**vSAN과의 상호 운용성 지원** → FT를 사용하는 VM을 vSAN 클러스터에서 실행 가능.

​	•	**고속 체크포인트(Checkpoint) 복사** → Primary VM과 Secondary VM의 동기화를 유지하여 장애 발생 시 즉각적인 전환 가능.



------



**결론**

​	•	**vSphere FT는 무중단 장애 복구를 위한 기술로, 중요한 워크로드를 보호**.

​	•	**Primary VM과 Secondary VM 간의 지속적인 복제 및 동기화가 핵심**.

​	•	**vSphere vMotion 및 다양한 스토리지 포맷을 지원하여 유연성을 제공**.

​	•	**FT VM 배포 시, 호스트당 최대 vCPU 제한을 고려하여 리소스를 계획해야 함**.