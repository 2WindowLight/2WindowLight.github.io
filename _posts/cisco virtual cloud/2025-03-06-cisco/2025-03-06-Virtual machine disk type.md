---
title: <h0>Virtual machine disk type</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-06-cisco]
tags: [Network, Cloud]






---

# Virtual machine disk type

## **가상 머신 디스크 유형**



가상 머신을 생성하거나 복제할 때, 또는 가상 머신을 마이그레이션할 때 사용할 수 있는 세 가지 가상 디스크 형식이 있다.

1. ------

   **씬 프로비저닝(Thin Provisioning)** [디폴트 방식]

​	•	초기에는 최소한의 스토리지만 사용하고, 실제 데이터가 추가될 때마다 크기가 증가하는 방식이다.

​	•	스토리지 공간을 절약할 수 있지만, 데이터 증가에 따라 성능 저하가 발생할 수 있다.

2. ------

   **두꺼운 프로비저닝 - 지연 초기화(Thick Provision Lazy Zeroed)**

​	•	디스크를 생성할 때 전체 용량이 할당되지만, 기존 데이터는 유지되며 가상 머신이 데이터를 기록할 때마다 0으로 초기화된다.

​	•	데이터 복구 가능성이 있어 보안이 중요한 환경에는 적합하지 않다.

​	•	씬 프로비저닝 디스크로 변환할 수 없다.

3. ------

   **두꺼운 프로비저닝 - 즉시 초기화(Thick Provision Eager Zeroed)**

​	•	디스크 생성 시 전체 공간이 할당되고, 기존 데이터가 즉시 0으로 초기화된다.

​	•	Fault Tolerance(FT) 같은 클러스터링 기능을 지원한다.

​	•	디스크 생성 시간이 길지만, 성능 및 보안이 강화된 방식이다.



------

**그림 설명**



위 그림은 ESXi 호스트에서 실행되는 가상 머신들이 VMFS 데이터스토어를 사용하는 방식을 나타낸다.

​	•	첫 번째 가상 머신은 **두꺼운 프로비저닝(Thick Provisioned)** 디스크(20GB)를 사용하며, 생성과 동시에 20GB의 공간이 할당된다.

​	•	두 번째 및 세 번째 가상 머신은 **씬 프로비저닝(Thin Provisioned)** 디스크를 사용한다.

​	•	두 번째 가상 머신은 40GB의 씬 프로비저닝 디스크를 가지고 있지만, 현재 사용 중인 공간은 20GB이다.

​	•	세 번째 가상 머신은 80GB 씬 프로비저닝 디스크를 할당받았으나, 현재 40GB만 사용하고 있다.

​	•	VMFS 데이터스토어의 총 용량은 100GB이며, 두꺼운 프로비저닝과 씬 프로비저닝 디스크가 함께 사용되고 있다.

------

------

