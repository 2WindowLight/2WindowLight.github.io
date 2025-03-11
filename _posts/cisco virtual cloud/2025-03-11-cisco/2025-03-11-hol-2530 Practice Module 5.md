---
title: <h0>hol-2530 Practice Module 5</h0>
author: cotes   
categories: [cisco virtual cloud,2025-03-11-cisco]
tags: [Network, Cloud]







---



## 1. 백업 구성

------

### step1

![image-20250311151628553](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311151628553.png)

------

### step2

![image-20250311151716945](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311151716945.png)

------

### step3

![image-20250311151837800](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311151837800.png)

------

### step4

![image-20250311151933657](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311151933657.png)

------

### step5

![image-20250311152052521](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311152052521.png)

------

### step6

![image-20250311152130446](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311152130446.png)

------

------

## 스냅샷 구성



![image-20250311152248943](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311152248943.png)

**vCenter Server Appliance가 보이지 않는 이유**



------



1. **문제 상황**
   * vSphere Client 환경에서 **vCenter Server Appliance(VCSA)가 보이지 않음**
   * 왼쪽 트리 구조에서 **RegionA01-COMPO1** 클러스터에 **경고 아이콘(!)** 표시됨
   * vCenter가 실행되지 않는 것으로 보이지만, 이는 **의도된 설계(Design)**



------



2. **원인 분석**
   * **Nested 환경**에서 실습 중이므로 vCenter Server가 **상위 계층에서 실행됨**
   * 따라서 현재 vSphere Client에서 직접 vCenter Server를 확인할 수 없음
   * 하지만 **스냅샷 프로세스는 다른 VM과 동일**하게 작동
   * 실습에서는 **acct-web01 VM을 vCenter Server의 대체로 사용**

------

3. **결론**
   * vCenter Server는 **Nested 환경의 상위 계층**에서 실행되므로 현재 화면에서 보이지 않는 것이 정상
   * vSphere 관리 기능은 정상 작동하며, 실습에서는 **acct-web01 VM을 대체로 사용 가능**
   * 클러스터 경고 상태를 점검하여 **추가적인 문제 발생 여부 확인 필요**
   * vCenter 관련 작업(스냅샷, 마이그레이션 등)은 다른 VM에서도 동일한 방식으로 수행할 수 있음

------

### step1

![image-20250311152337511](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311152337511.png)

------

### step2

![image-20250311152411524](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311152411524.png)

------

### step3

![image-20250311152449509](/assets/cisco_post_img/2025-03-11-hol-2530 Practice Module 5//image-20250311152449509.png)

------

------

## **스냅샷과 vCenter 백업 비교 및 활용 가이드**



1. ------

   **스냅샷(Snapshots)의 개념 및 주의점**

   * 스냅샷은 VM의 특정 시점 상태를 저장하여 빠른 복구를 가능하게 함
   * **파일 수준 복구(File-level Restore)에는 적합하지 않음**
   * 장기적으로 유지하면 성능 저하 및 **스토리지 문제**를 초래할 수 있음
   * 스냅샷이 오래 지속될수록 **기본 디스크와 델타 파일 크기 증가**
   * **단기적인 목적으로만 사용**(예: 패치 적용 전, 업데이트 테스트)



2. ------

   **vCenter 백업(vCenter Backup)의 특징**

   * **vCenter Server Appliance(VCSA) 자체 내장 백업 기능** 활용 가능
   * VM에 별도의 백업 에이전트가 필요 없음
   * **운영 중에도 백업 가능**(다운타임 없음)
   * 백업 파일은 **외부 저장소**(지원되는 프로토콜 활용)에 저장됨
   * 복구 시 VCSA ISO를 마운트하여 **원본 구성 그대로 복원 가능**
   * 기존 UUID 유지 → 기존 서비스와의 연결 유지됨



3. ------

   **스냅샷과 vCenter 백업의 차이점**

| **항목**      | **스냅샷**                                        | **vCenter 백업**                             |
| ------------- | ------------------------------------------------- | -------------------------------------------- |
| **목적**      | 단기 복구, 테스트 환경                            | 장기 백업, 재해 복구                         |
| **저장 방식** | VM의 상태를 기록하고 변경 사항을 델타 파일로 보관 | 전체 VCSA 파일을 백업하여 외부 저장소에 저장 |
| **성능 영향** | 장기간 유지 시 성능 저하 가능                     | VM 성능에 영향 없음                          |
| **유지 기간** | 짧은 기간(패치, 테스트 등)                        | 장기간 유지 가능                             |
| **복구 방식** | 스냅샷 되돌리기 또는 병합                         | 백업 파일을 이용한 복구                      |

4. ------

   **활용 방법 정리**

   * **스냅샷** → 단기적인 **테스트, 패치 적용 전 백업 용도**
   * *vCenter 백업** → 장기적인 **재해 복구 및 운영 백업**에 적합
   * 스냅샷은 **백업 솔루션의 일부로 활용 가능하지만**, 단독으로 장기적인 백업 솔루션으로 사용해서는 안 됨
   * vCenter 백업은 **재해 복구 계획** 및 **기업의 공식적인 백업 정책**에 반드시 포함해야 함

이와 같은 개념을 이해하고, **각각의 목적에 맞는 방식으로 스냅샷과 백업을 활용**하는 것이 중요함.