---
title: AWS Access key, CLI and SDK
author: cotes   
categories: [AWS SSA 03]
tags: [AWS,AWS SSA 03 Section4]





---

# AWS Access Key, CLI and SDK

다음은 강의 내용을 체계적으로 정리한 것입니다.

------

**1. AWS 액세스 방법**

AWS에 접근하는 방법은 **세 가지**가 있다.

1. **AWS 관리 콘솔 (Management Console)**

   * 웹 브라우저에서 **사용자 이름, 비밀번호, MFA(다중 인증)**를 사용하여 로그인

   * 가장 일반적인 접근 방식

2. **AWS CLI (Command Line Interface)**

   * **명령줄 인터페이스**를 통해 AWS와 상호작용

   * **액세스 키**를 사용하여 인증

   * 스크립트 자동화 가능

3. **AWS SDK (Software Development Kit)**

   * 프로그래밍 코드 내에서 AWS API를 호출하는 방식

   * **액세스 키**를 사용하여 인증

   * 다양한 언어(Java, Python, JavaScript 등)를 지원

------

**2. AWS CLI(Command Line Interface)**

**CLI란?**

​	•	명령줄 환경에서 AWS 서비스와 직접 상호작용할 수 있는 도구

​	•	모든 명령어가 aws로 시작함 (예: aws s3 cp)

​	•	AWS 서비스의 **공용 API**에 직접 액세스 가능

**CLI 사용 목적**

​	•	관리 콘솔을 사용하지 않고 **터미널에서 직접 AWS 리소스를 제어**

​	•	**반복 작업을 스크립트로 자동화**하여 운영 효율성을 높임

​	•	오픈 소스 프로젝트로 **GitHub에서 소스 코드 확인 가능**

------

**3. AWS SDK(Software Development Kit)**

**SDK란?**

​	•	특정 프로그래밍 언어를 위한 **AWS 라이브러리 모음**

​	•	애플리케이션 코드 내에서 AWS API를 호출할 때 사용

​	•	터미널이 아닌 **코딩을 통해 AWS 서비스와 통신**

**지원 언어**

​	•	JavaScript, Python, PHP, .NET, Ruby, Java, Go, Node.js, C++

​	•	모바일 앱 개발을 위한 **Android 및 iOS SDK** 지원

​	•	IoT(사물인터넷) 기기를 위한 **IoT SDK** 지원

**SDK 사용 예시**

​	•	AWS CLI는 사실상 **Python용 AWS SDK인 Boto**를 기반으로 구축됨

------

**4. AWS 액세스 키 (Access Key) 관리**

AWS CLI와 SDK는 **액세스 키**를 사용하여 인증한다.

​	•	**액세스 키 ID** = 사용자 이름

​	•	**비밀 액세스 키** = 비밀번호

**액세스 키 생성 방법**

​	•	**AWS 관리 콘솔**에서 **Create Access Key** 버튼을 클릭하여 생성

​	•	**액세스 키 다운로드 후** 터미널에서 AWS CLI 인증에 사용

**액세스 키 보안 원칙**

​	•	**비밀번호처럼 다루어야 하며, 타인과 공유해서는 안 됨**

​	•	**각 IAM 사용자는 자신의 액세스 키를 직접 관리**해야 함

​	•	**유출되면 보안 위험이 크므로, 필요하지 않다면 생성하지 않는 것이 좋음**

------

**5. 정리**

​	•	AWS 접근 방식은 **관리 콘솔, CLI, SDK**로 나뉨

​	•	**CLI는 명령줄 기반 도구**, **SDK는 애플리케이션 내 API 호출 방식**

​	•	CLI와 SDK는 **액세스 키로 보호**되며, **보안상 철저한 관리 필요**

​	•	AWS CLI를 활용하면 **자동화 및 효율적인 리소스 관리** 가능



다음 강의에서는 **CLI 설정 및 액세스 키 활용 방법**을 실습할 예정이다.
