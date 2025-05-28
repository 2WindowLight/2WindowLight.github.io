---
title: AWS Service Catalog
author: cotes   
categories: [AWS SSA 03]
tags: [AWS]









---

####  AWS Service Catalog은 조직이 승인된 IT 서비스를 카탈로그화하고 관리할 수 있도록 해주는 AWS 서비스입니다.

이 서비스는 관리자가 **미리 정의한 리소스(EC2 인스턴스, RDS 데이터베이스 등)를 제품 형태**로 제공하고, 개발팀이 이 제품을 선택하여 **프로비저닝**



------

### Service Catalog의 핵심 개념:

- **제품 (Product):** 

  AWS CloudFormation 템플릿을 기반으로 특정 리소스를 모아놓은 단위입니다.

- **포트폴리오 (Portfolio):** 

  제품의 그룹으로, 특정 애플리케이션 팀에 공개됩니다.

- **카탈로그 (Catalog):** 

  포트폴리오가 포함된 전체적인 서비스 카탈로그입니다.

  

------

### Service Catalog는 다음과 같은 상황에서 유용하게 활용될 수 있습니다:

- 다양한 개발팀이 동일한 리소스 환경을 사용해야 할 때
- 인프라 관리자가 리소스 프로비저닝을 중앙에서 관리하고 싶을 때
- 기업의 정책과 컴플라이언스를 준수해야 할 때
- AWS 리소스 프로비저닝 프로세스를 자동화하고 싶을 때

------

## 참고: AWS Service Catalog는 AWS CloudFormation 템플릿을 기반으로 작동합니다.