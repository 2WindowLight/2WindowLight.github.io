---
title: <h0>Module 8-2 Lesson 2 Configuring Enhanced vMotion Compatibility</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-10-cisco]
tags: [Network, Cloud]








---

# Lesson 2: Configuring Enhanced vMotion Compatibility

------

## **CPU Constraints on vSphere vMotion Migration(가상 머신 마이그레이션을 위한 CPU 제약사항)**



vSphere vMotion 마이그레이션을 위해 소스 호스트와 대상 호스트는 CPU 호환성이 요구된다. 단, CPU 특성에 따라 정확한 일치가 필요한 경우와 그렇지 않은 경우가 있다.

1. **클럭 속도, 캐시 크기, 하이퍼스레딩, 코어 수**
   * 소스와 대상 호스트 간에 정확한 일치는 필요하지 않다.
   * 이는 VMkernel이 해당 특성들을 가상화하기 때문이다.

2. **제조사, 패밀리, 세대**
   * 예를 들어, Intel과 AMD, 또는 Intel의 특정 세대(예: Westmere)와 같은 차이는 중요한 요소이다.
   * 소스와 대상 호스트가 동일해야 한다.

3. **명령어 집합 (SSE3, SSSE3, SSE4.1 등)**
   * SSE4.1과 같은 명령어는 가상화 계층을 우회하여 애플리케이션이 직접 사용하기 때문에,

소스 호스트가 지원하고 대상 호스트가 지원하지 않으면 호환되지 않아 마이그레이션에 실패한다.

4. **가상화 하드웨어 지원**
   * 32비트 VM의 경우 해당 특성은 VMkernel이 가상화하므로 정확한 일치는 필요하지 않다.
   * 그러나 Intel 기반 64비트 VM의 경우 Intel VT와 같은 가상화 하드웨어 지원이 요구된다.



예를 들어, 소스 호스트에서 하이퍼스레딩이 활성화되어 있고 대상 호스트에서 비활성화된 경우에는 VMkernel이 이를 가상화하므로 마이그레이션이 진행된다. 그러나 소스 호스트 프로세서가 SSE4.1 명령어를 지원하고 대상 호스트가 지원하지 않으면, 이 차이로 인해 마이그레이션이 실패한다.



------



**요약**

vSphere vMotion은 CPU의 여러 특성 중 일부(클럭 속도, 캐시, 하이퍼스레딩, 코어 수)는 VMkernel이 가상화하므로 정확한 일치가 필요 없지만, 제조사, 패밀리, 세대 및 특정 명령어 집합(SSE4.1 등)은 정확히 일치해야 하며, 이를 만족하지 못하면 마이그레이션이 실패한다.

------

------

## **Enhanced vMotion Compatibility(강화된 vMotion 호환성)**



![image-20250310123414188](/assets/cisco_post_img/2025-03-10-Module 8-2 Lesson 2 Configuring Enhanced vMotion Compatibility//image-20250310123414188.png)



Enhanced vMotion Compatibility(EVC)는 서로 다른 CPU를 사용하는 호스트 간에도 vSphere vMotion을 수행할 수 있도록 지원하는 클러스터 기능이다. 이 기능은 CPU 기능 집합(베이스라인)을 통합하여 모든 호스트가 동일한 기능을 제공하도록 강제함으로써, 다른 세대의 CPU 간에도 VM 이동이 가능하게 한다.



------



1. **Enhanced vMotion Compatibility의 주요 기능**

   * **CPU 기능 일관성 유지:**
     * 클러스터 내 모든 호스트가 동일한 CPU 기능 세트(베이스라인)를 제공하도록 설정됨.
     * VM이 실행되는 호스트가 변경되더라도 CPU 차이로 인한 문제 발생 가능성을 줄일 수 있음.

   * **자동 호환성 검증:**
     * CPU 베이스라인을 지원하지 않는 호스트는 클러스터에 추가될 수 없음.
     * 호스트가 클러스터에 추가될 때 자동으로 CPU 베이스라인에 맞춰 구성됨.

   * **CPU 호환성 적용 방식:**
     * vCenter는 CPU 기능을 강제로 통일하는 CPU 베이스라인을 적용하여, 서로 다른 CPU 세대 간에도 vMotion 수행 가능.
     * VM이 어느 호스트에서 실행되든 동일한 CPU 기능을 감지하도록 보장.



------



2. **Enhanced vMotion Compatibility 작동 방식**
   * 클러스터 내 각 호스트의 CPU는 동일한 기능 집합(베이스라인)을 제공하도록 설정됨.
   * VM이 실행 중인 호스트에서 다른 호스트로 이동할 때 CPU 차이로 인한 호환성 문제를 방지.
   * 베이스라인과 호환되지 않는 호스트는 클러스터에 추가될 수 없음.

------



3. **Enhanced vMotion Compatibility 설정 방법**
   * vSphere Client에서 클러스터 선택
   * **설정(Settings) > 고급 옵션(Advanced Options)** 으로 이동
   * **Enhanced vMotion Compatibility(EVC)** 활성화
   * CPU 베이스라인 선택 (예: Intel “Westmere”, AMD “Opteron G4”)
   * 클러스터 내 모든 호스트가 동일한 CPU 베이스라인을 따르도록 강제 적용



------



4. **Enhanced vMotion Compatibility를 사용해야 하는 이유**
   * 서로 다른 CPU 세대 간에도 VM 마이그레이션 가능
   * VM 실행 환경 일관성 유지 (애플리케이션 안정성 보장)
   * CPU 차이로 인한 충돌 및 다운타임 방지
   * 클러스터 내 VM 이동을 보다 유연하게 수행 가능



------



5. **Enhanced vMotion Compatibility 적용 시 고려 사항**
   * 일부 최신 CPU 기능이 비활성화될 수 있음.
   * CPU 기능을 통일하는 과정에서 최신 CPU에서 지원하는 일부 기능이 비활성화될 수 있음.
   * 따라서, 클러스터에 추가할 호스트의 CPU 세대를 고려하여 적절한 베이스라인을 선택해야 함.
   * EVC를 적용하면 기존 클러스터 호스트에도 즉시 적용됨.
   * 클러스터 내 VM이 EVC 설정 변경에 영향을 받을 수 있으므로, 적용 전에 충분한 검토 필요.



------

**결론**

Enhanced vMotion Compatibility를 사용하면 서로 다른 CPU 세대를 가진 호스트에서도 VM을 원활하게 마이그레이션할 수 있으며, 운영 중단 없이 안정적인 가상 환경을 유지할 수 있다.

------

------

## **Enhanced vMotion Compatibility(강화된 vMotion 호환성)**



Enhanced vMotion Compatibility(EVC)는 **서로 다른 CPU를 사용하는 호스트 간에도 vSphere vMotion을 수행할 수 있도록 지원하는 클러스터 기능**이다. 이 기능은 **CPU 기능 집합(베이스라인)을 통합하여 모든 호스트가 동일한 기능을 제공하도록 강제**함으로써, 다른 세대의 CPU 간에도 VM 이동이 가능하게 한다.



------



1. **Enhanced vMotion Compatibility의 주요 기능**

   * **CPU 기능 일관성 유지:**
     * 클러스터 내 모든 호스트가 **동일한 CPU 기능 세트(베이스라인)를 제공**하도록 설정됨.
     * VM이 실행되는 호스트가 변경되더라도 **CPU 차이로 인한 문제 발생 가능성을 줄일 수 있음**.

   * **자동 호환성 검증:**
     * *CPU 베이스라인을 지원하지 않는 호스트는 클러스터에 추가될 수 없음.
     * **호스트가 클러스터에 추가될 때 **자동으로 CPU 베이스라인에 맞춰 구성됨**.

   * **CPU 호환성 적용 방식:**
     * vCenter는 **CPU 기능을 강제로 통일하는 CPU 베이스라인을 적용**하여, 서로 다른 CPU 세대 간에도 vMotion 수행 가능.
     * VM이 어느 호스트에서 실행되든 **동일한 CPU 기능을 감지**하도록 보장.



------



2. **Enhanced vMotion Compatibility 작동 방식**
   * 클러스터 내 **각 호스트의 CPU는 동일한 기능 집합(베이스라인)을 제공하도록 설정됨.**
   * VM이 실행 중인 호스트에서 다른 호스트로 이동할 때 **CPU 차이로 인한 호환성 문제를 방지**.
   * **베이스라인과 호환되지 않는 호스트는 클러스터에 추가될 수 없음.**

------



3. **Enhanced vMotion Compatibility 설정 방법**
   * **vSphere Client에서 클러스터 선택**
   * **“설정(Settings) > 고급 옵션(Advanced Options)“으로 이동**
   * **“Enhanced vMotion Compatibility(EVC)” 활성화**
   * **CPU 베이스라인 선택 (예: Intel “Westmere”, AMD “Opteron G4”)**
   * **클러스터 내 모든 호스트가 동일한 CPU 베이스라인을 따르도록 강제 적용**



------



4. **Enhanced vMotion Compatibility를 사용해야 하는 이유**

✔ **서로 다른 CPU 세대 간에도 VM 마이그레이션 가능**

✔ **VM 실행 환경 일관성 유지 (애플리케이션 안정성 보장)**

✔ **CPU 차이로 인한 충돌 및 다운타임 방지**

✔ **클러스터 내 VM 이동을 보다 유연하게 수행 가능**



------



5. **Enhanced vMotion Compatibility 적용 시 고려 사항**

   * **일부 최신 CPU 기능이 비활성화될 수 있음.**
     * CPU 기능을 통일하는 과정에서 **최신 CPU에서 지원하는 일부 기능이 비활성화될 수 있음.**
     * 따라서, 클러스터에 추가할 호스트의 CPU 세대를 고려하여 적절한 베이스라인을 선택해야 함.

   * **EVC를 적용하면 기존 클러스터 호스트에도 즉시 적용됨.**
     * 클러스터 내 VM이 EVC 설정 변경에 영향을 받을 수 있으므로, **적용 전에 충분한 검토 필요**.



------

**결론**

Enhanced vMotion Compatibility를 사용하면 **서로 다른 CPU 세대를 가진 호스트에서도 VM을 원활하게 마이그레이션할 수 있으며**, 운영 중단 없이 안정적인 가상 환경을 유지할 수 있다.

------

------

## Virtual Machine EVC CPU Mode



![image-20250310123640892](/assets/cisco_post_img/2025-03-10-Module 8-2 Lesson 2 Configuring Enhanced vMotion Compatibility//image-20250310123640892.png)