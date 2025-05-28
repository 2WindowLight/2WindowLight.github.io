---
title:Transit, interface endpoint, Gateway endpoint 구분
author: cotes   
categories: [AWS SSA 03]
tags: [AWS]








---

## **게이트웨이 엔드포인트 (Gateway Endpoint)**

* **용도**: **S3** 및 **DynamoDB**에 대한 **프라이빗 연결** 제공.

### **VPC (Virtual Private Cloud) 내에서 다른 AWS 서비스에 접근하기 위한 통신 수단**

------

## **인터페이스 엔드포인트 (Interface Endpoint)**

- **용도**: **AWS PrivateLink**로 지원되는 서비스(API Gateway, CloudWatch, KMS 등)에 대한 프라이빗 연결.
- **서로 다른 프로토콜이나 네트워크를 사용하는 시스템 간에 통신을 가능하게 하는 관문 역할을 하는 것을 의미**
- **즉, 한 네트워크에서 다른 네트워크로 접근하거나, 다른 프로토콜을 사용하는 시스템 간에 데이터를 교환하기 위해 필요한 연결 장치 또는 소프트웨어를 지칭**

------

## **Transit Gateway**

- **용도**: **여러 VPC, VPN, Direct Connect 연결을 중앙 관리**하는 네트워크 허브.