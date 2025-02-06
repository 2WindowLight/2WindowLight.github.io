---
title: <h0>Load Balancing</h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]









---

# 로드 밸런싱 (서버 분산화)

## 스위칭 방식별 부하 분산 71p

부하 분산 (Load balancing) - 라우팅 테이블에 동일한 네트워크로 가는 경로가 다수 존재할 때, 이 다수의 경로를 모두 사용하는 것을 부하 분산 즉 로드 밸런싱 이라고 한다.

부하가 분산되는 경로는 모두 라우팅 테이블에 인스톨된다. 그러나, 라우터가 패킷을 어떤 경로로 전송하느냐 하는 것은 해당 인터페이스가 사용하는 스위칭 방식에 따라서 달라진다.

SLB - Server Load Balancer

