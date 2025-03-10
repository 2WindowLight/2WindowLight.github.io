---
title: <h0>Module 8-4 Lesson 4 Cross vCenter Migrations</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-10-cisco]
tags: [Network, Cloud]






---

# Lesson 4: Cross vCenter Migrations

------

## Cross vCenter Migration Requirements

![image-20250310131443842](/assets/cisco_post_img/8-4//image-20250310131443842.png)

**Cross vCenter Migration 요구 사항 정리**

1. **호스트 및 vCenter 시간 동기화**
   * 각 ESXi 호스트는 시간이 동기화되어 있어야 함.
   * 서로 다른 vCenter 인스턴스 간에도 정확한 Single Sign-On 토큰 검증을 위해 시간 동기화가 필수적임.

2. **vCenter Single Sign-On 도메인**

   * 두 vCenter 인스턴스는 같은 SSO 도메인에 있거나 다른 SSO 도메인에 있을 수 있음.

   * Enhanced Linked Mode에 반드시 속해 있어야 하는 것은 아님.

3. **공유 스토리지 연결(Compute 리소스 마이그레이션 시)**
   * 컴퓨트 리소스만 이전하는 경우, VM이 위치한 공유 스토리지에 두 vCenter 인스턴스 모두 연결되어 있어야 함.

4. **버전 호환성**
   * 서로 다른 버전의 vCenter 인스턴스 간에도 Cross vCenter Migration 가능.

------

------

## About Cross vCenter Migrations

1. **vSphere vMotion을 통한 Cross vCenter 마이그레이션**
   * 서로 다른 vCenter 인스턴스 간에 VM을 마이그레이션할 수 있음.
   * 두 vCenter가 동일한 Enhanced Linked Mode 그룹에 속해 있지 않아도 가능.

2. **활용 사례**
   * **워크로드 균형화**
     * 동일 사이트 또는 다른 지리적 위치에 있는 클러스터나 vCenter 인스턴스 간에 워크로드를 분산하기 위함.

* **환경 간 이동**
  * 예: 개발 환경에서 프로덕션 환경으로 VM을 옮겨야 할 때.

* **서비스 수준 요구사항 충족**
  * 스토리지 용량, 성능, 기타 SLA 요건 변화에 맞춰 VM을 적절한 리소스로 이전.

* **온프레미스에서 퍼블릭 클라우드로 이동**
  * 예: VMware Cloud on AWS 같은 퍼블릭 클라우드 환경으로 VM을 이전할 때.



위와 같은 Cross vCenter Migration을 통해, 가상화 환경 내 워크로드를 유연하게 배치하고 이동시키면서, 운영 효율성과 확장성을 높일 수 있습니다.

------

------

## About Long-Distance vSphere vMotion Migration

![image-20250310131903136](/assets/cisco_post_img/8-4//image-20250310131903136.png)

1. **개념**
   * **Long-Distance vSphere vMotion**은 **Cross vCenter Migration**의 확장 기능으로, 지리적으로 큰 거리에 걸쳐 분산되어 있는 vCenter 인스턴스 간에 마이그레이션을 수행함.
   * 사이트 간 지연(latency)이 큰 환경에서도 VM을 마이그레이션할 수 있도록 지원함.

2. **활용 사례**
   * **영구(Permanent) 마이그레이션**: 물리적 위치가 크게 다른 두 데이터센터 간에 VM을 완전히 이전해야 할 때.
   * **재해 회피(Disaster Avoidance)**: 재해가 예측되거나 발생하기 전에 VM을 안전한 위치로 이동.
   * **Site Recovery Manager(SRM) 및 재해 회피 테스트**: DR 시나리오 테스트 시 VM을 다른 사이트로 이동.
   * **멀티사이트 로드 밸런싱**: 여러 지역에 분산된 리소스를 효율적으로 사용하기 위해 VM을 움직여 워크로드를 분산.
   * **Follow-the-Sun 시나리오**: 전 세계 여러 타임존에서 근무하는 지원팀이 순차적으로 VM을 관리할 때, VM을 해당 팀과 동일한 지역으로 마이그레이션하여 장거리 액세스의 비효율을 줄임.

이를 통해 대규모 지리적 분산 환경에서도 VM을 유연하고 안전하게 이동할 수 있으며, 가용성과 효율성을 높일 수 있습니다.