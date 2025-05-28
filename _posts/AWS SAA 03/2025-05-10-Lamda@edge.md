---
title: Lambda@edge
author: cotes   
categories: [AWS SSA 03]
tags: [AWS]








---

####  Lambda@Edge는 AWS의 서버리스 컴퓨팅 서비스인 Lambda를 Amazon CloudFront의 엣지 로케이션에서 실행할 수 있도록 하는 기능입니다. 

####  이를 통해 사용자와 가까운 곳에서 코드를 실행하여 지연 시간을 줄이고 웹 애플리케이션의 성능을 개선



**기능:**

* CloudFront 를 통과하는 요청 및 응답을 수정하는 데 사용
* 이미지 동적 조정
* HTTP 헤더 기반 적절한 형식 제공
* 오리진 대기 시간 줄임