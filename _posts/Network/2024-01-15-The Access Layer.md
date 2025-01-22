---
title: <h0>The Access Layer</h0>
author: cotes   
categories: [Network]
tags: [Network]

---

# Access Layer

Access Layer는 네트워크의 가장 하위 계층으로, 사용자 장치(EndPoint)와 네트워크 인프라(switch, router) 간의 연결을 담당한다.

주요기능으로는 다음과 같다.

1. 장치 연결 관리: pc, 서버, Iot 장치 등 다양한 디바이스를 네트워크에 연결
2. 데이터 전송 및 보안 VLAN 설정, 포트 보안, 인증 등 기본 보안 제공
3. QoS(Quality of Service): 특정 트래픽에 우선순위를 부여하여 네트워크 성능 최적화
4. 속도 및 매체 유형: Fast Ethernet, Gigabit Ethernet 등 다양한 전송 속도 지원

Access Layer 와 분할1:
   
* Access Layer는 데이터 분할보다 물리적 및 링크 계층 쳥녁을 처리한다.
* 데이터 분할과 관련된 작업은 주로 네트워크 및 전송 계층에서 이루어진다.

Access Layer 설계

* Port Security: 특정 Mac 주소만 허용하여 보안 강화.
* VLAN 지원: 네트워크 트래픽을 논리적으로 분리.
* Power over Ethernet (POE): 네트워크 케이블을 통해 전력을 공급한다.