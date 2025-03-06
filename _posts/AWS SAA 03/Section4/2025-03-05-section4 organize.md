---
title: section4 organize
author: cotes   
categories: [AWS SSA 03]
tags: [AWS,AWS SSA 03 Section4]







---

# **IAM 요약 정리**

1. **IAM 사용자(User)**
   * 실제 물리적 사용자와 매핑됨
   * AWS 콘솔 비밀번호를 가짐

2. **IAM 그룹(Group)**
   * 사용자만 포함할 수 있음
   * 그룹을 통해 권한을 일괄 관리 가능

3. **IAM 정책(Policy)**
   * JSON 문서 형식으로 사용자 또는 그룹에 대한 **권한을 정의**

4. **IAM 역할(Role)**
   * 특정 AWS 서비스(예: EC2 인스턴스)가 AWS 리소스에 접근할 수 있도록 권한 부여

5. **보안 기능**

   * **멀티팩터 인증(MFA)** 활성화 가능

   * **비밀번호 정책** 설정 가능

6. **AWS 관리 방법**

   * **CLI(Command Line Interface)**: 명령줄에서 AWS 리소스 관리

   * **SDK(Software Development Kit)**: 프로그래밍 언어를 사용해 AWS 서비스 관리

7. **액세스 키(Access Key)**

   * CLI 및 SDK 사용을 위한 인증 방식

   * 보안 유지를 위해 절대 공유하지 말아야 함

8. **IAM 감사(Auditing)**

   * **IAM 자격 증명 보고서(Credential Report)** 생성

   * **IAM 액세스 어드바이저(Access Advisor)**를 사용해 권한 사용 여부 분석



IAM을 활용하면 AWS 계정을 **안전하고 체계적으로 관리**할 수 있다.

------

**IAM 정책의 문장은 시드, 효과, 원칙, 조치, 리소스, 그리고 조건으로 구성된다. 버전은 IAM 정책 자체의 일부이지, 문장의 일부가 아니다**