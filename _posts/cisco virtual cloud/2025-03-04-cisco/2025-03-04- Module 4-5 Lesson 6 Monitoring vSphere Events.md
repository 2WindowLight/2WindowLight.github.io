---
title: <h0>Module 4-5 Lesson 6 Monitoring vSphere Events</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-03-04-cisco]
tags: [Network, Cloud]












---



# Lesson 6: Monitoring vSphere Events



------

## About vSphere Tasks(**vSphere Tasks 개요**)

![image-20250304122207836](/assets/cisco_post_img/2025-03-04-Lesson 6 Monitoring vSphere Events//image-20250304122207836.png)



vSphere에서 수행하는 모든 작업은 **Task(작업)**라고 하며, 일반적인 작업 예시는 다음과 같습니다.

​	•	가상 머신 전원 켜기(Powering on a virtual machine)

​	•	네트워크 구성 변경(Updating the network configuration)

​	•	호스트 및 가상 머신 설정 수정(Altering host and virtual machine configurations)



**vSphere Tasks 관리 및 모니터링**



vSphere 관리자는 **vSphere Client에서 작업을 모니터링**하여 시스템 내에서 수행된 작업을 추적할 수 있습니다.



**1. 작업 목록(Task List)**

​	•	작업(Task Name): 수행된 작업의 이름 (예: 가상 머신 재구성, OVF 배포 등)

​	•	대상(Target): 해당 작업이 적용된 ESXi 호스트, VM, 네트워크 등의 대상

​	•	상태(Status): 작업이 완료(Completed), 진행 중(In Progress), 실패(Failed) 상태인지 표시

​	•	실행자(Initiator): 작업을 수행한 사용자 또는 관리자



**2. 작업 로그 활용**

​	•	**문제 해결(Troubleshooting)**: 특정 설정이 변경되었거나 예상치 못한 장애가 발생했을 때, **작업 기록을 확인하여 원인을 분석**할 수 있습니다.

​	•	**보안 감사(Security Auditing)**: 특정 작업이 언제, 누가 수행했는지를 파악하여 보안 감사 및 정책 준수를 검토할 수 있습니다.



**결론**

vSphere Tasks는 관리자가 가상 인프라에서 **작업 이력을 추적하고, 문제를 해결하며, 보안 및 운영 관리를 강화하는 데 중요한 도구**입니다.

------

------

## About vCenter Log Levels

**vCenter 로그 레벨 개요**



vCenter의 각 서비스는 자체적인 **로그 파일**을 생성하며, 이 로그는 **문제 해결(Troubleshooting)**에 활용됩니다.

**로그 레벨(Log Level)**을 설정하여 **저장되는 로그의 양과 유형을 조정**할 수 있습니다.

**로그 레벨 및 설정 목적**

| **옵션(Option)**              | **설명(Description)**                                   |
| ----------------------------- | ------------------------------------------------------- |
| **None**                      | 로그 기록 비활성화 (Turns off logging)                  |
| **Error (오류만)**            | 오류(Error) 로그만 기록                                 |
| **Warning (경고 및 오류)**    | 경고(Warning) 및 오류(Error) 로그 기록                  |
| **Info (일반 로그 - 기본값)** | 정보(Info), 경고(Warning), 오류(Error) 로그 기록        |
| **Verbose (상세 로그)**       | 정보, 경고, 오류 및 상세(Verbose) 로그 기록             |
| **Trivia (가장 상세한 로그)** | 모든 로그(Info, Warning, Error, Verbose 및 Trivia) 기록 |

**로그 레벨 변경이 필요한 경우**

​	1.	**복잡한 문제 해결(Troubleshooting)**

​	•	장애나 성능 문제 분석을 위해 **Verbose 또는 Trivia** 수준으로 로그를 설정

​	•	문제 해결 후 **Info**(기본값)로 되돌려야 불필요한 로그 저장을 방지

​	2.	**로그 저장 공간 관리**

​	•	로그 파일이 너무 많을 경우 **Warning 또는 Error** 수준으로 제한

​	•	로그 파일 크기 및 저장 공간을 최적화할 때 사용

​	3.	**보안 및 감사(Auditing & Compliance)**

​	•	중요한 작업 기록을 보존하기 위해 **Info 이상**으로 유지

​	•	필요 시 **Verbose 또는 Trivia**로 설정하여 상세한 작업 내역을 확인

**변경 사항 적용**

​	•	**로그 레벨을 변경하면 즉시 적용**되며, **vCenter 시스템을 재시작할 필요 없음**

​	•	문제 해결이 끝난 후에는 **Info(일반 로그)**로 되돌려 **불필요한 리소스 낭비 방지** 필요

**결론**



vCenter 로그 레벨 설정은 문제 해결과 시스템 관리에 중요한 역할을 하며, **목적에 맞게 적절한 로그 수준을 선택하는 것이 필수**입니다.

특히, **Verbose 또는 Trivia 모드는 문제 해결 후 반드시 기본 설정으로 복원**해야 합니다.