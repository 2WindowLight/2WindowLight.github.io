---
title: <h0>BGP Input Policy</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-19-cisco]
tags: [Network]













---





**1. 전체 네트워크 구조**



주어진 그림과 IP/인터페이스 할당을 토대로 보면 다음과 같은 연결 상태를 갖습니다.

​	•	**R1**

​	•	Loopback: 1.1.1.1/24

​	•	e0/0: 1.1.12.1/24 → R2 e0/0(1.1.12.2/24)

​	•	e0/1: 1.1.14.1/24 → R4 e0/1(1.1.14.4/24)

​	•	**R2**

​	•	Loopback: 1.1.2.2/24

​	•	e0/0: 1.1.12.2/24 → R1 e0/0(1.1.12.1/24)

​	•	e0/1: 1.1.23.2/24 → R3 e0/1(1.1.23.3/24)

​	•	**R3**

​	•	Loopback: 1.1.3.3/24

​	•	e0/1: 1.1.23.3/24 → R2 e0/1(1.1.23.2/24)

​	•	e0/0: 1.1.34.3/24 → R4 e0/0(1.1.34.4/24)

​	•	**R4**

​	•	Loopback: 1.1.4.4/24

​	•	e0/0: 1.1.34.4/24 → R3 e0/0(1.1.34.3/24)

​	•	e0/1: 1.1.14.4/24 → R1 e0/1(1.1.14.1/24)



이렇게 네 대의 라우터가 사각형 혹은 삼각형 형태로 서로 연결되어 있습니다(그림상에서는 R1이 위쪽, R2–R3–R4가 아래쪽 라인에 배치된 것으로 보입니다).

**2. 인터페이스 및 IP 주소 설정**



코드 상으로는 각 라우터에서 다음을 수행했습니다.

​	•	**R1**:

​	•	lo0에 1.1.1.1/24

​	•	e0/0에 1.1.12.1/24 (R2와 연결)

​	•	e0/1에 1.1.14.1/24 (R4와 연결)

​	•	**R2**:

​	•	lo0에 1.1.2.2/24

​	•	e0/0에 1.1.12.2/24 (R1과 연결)

​	•	e0/1에 1.1.23.2/24 (R3와 연결)

​	•	**R3**:

​	•	lo0에 1.1.3.3/24

​	•	e0/1에 1.1.23.3/24 (R2와 연결)

​	•	e0/0에 1.1.34.3/24 (R4와 연결)

​	•	**R4**:

​	•	lo0에 1.1.4.4/24

​	•	e0/0에 1.1.34.4/24 (R3와 연결)

​	•	e0/1에 1.1.14.4/24 (R1과 연결)



이 설정으로 인해, 각 라우터 간에 IP 레벨로 통신이 가능하도록 기본 IP가 할당되었습니다.

**3. OSPF 설정**



코드에서 R2, R3, R4는 OSPF를 사용하지만, **R1은 OSPF를 돌리지 않습니다.** 즉, R1 ↔ R2, R1 ↔ R4 구간은 OSPF 라우팅이 아니라 BGP로만 연결됩니다.



**3.1 R2 (OSPF 2)**

```
router ospf 2
 router-id 1.1.2.2
 passive-interface e0/0
 int lo0
  ip ospf 2 area 0
  ip ospf network point-to-point
 int e0/0
  ip ospf 2 area 0
 int e0/1
  ip ospf 2 area 0
```

​	•	router-id로 1.1.2.2 지정

​	•	passive-interface e0/0 → R1과 연결된 인터페이스(e0/0)에서는 OSPF 인접을 형성하지 않겠다는 의미입니다. (R1이 OSPF를 돌리지 않으므로, 불필요하게 헬로우를 주고받지 않게 하려는 설정)

​	•	lo0, e0/0, e0/1를 모두 Area 0에 속하게 했습니다.



**3.2 R3 (OSPF 3)**

```
router ospf 3
 router-id 1.1.3.3
 int lo0
  ip ospf 3 area 0
  ip ospf network point-to-point
 int e0/0
  ip ospf 3 area 0
 int e0/1
  ip ospf 3 area 0
```

​	•	R3는 R2, R4와 OSPF를 통해 인접관계를 형성합니다.



**3.3 R4 (OSPF 4)**

```
router ospf 4
 router-id 1.1.4.4
 int lo0
  ip ospf 4 area 0
  ip ospf network point-to-point
 int e0/0
  ip ospf 4 area 0
 int e0/1
  ip ospf 4 area 0
```

​	•	R4 역시 R3와 OSPF 인접을 형성합니다.





​	•	**R2 ↔ R3 ↔ R4** 이 세 라우터가 OSPF로 서로의 Loopback 및 연결망(1.1.23.x, 1.1.34.x)을 학습하고 공유하게 됩니다.

​	•	R1은 OSPF에 참여하지 않으므로, R1의 네트워크(1.1.1.0/24)는 BGP로만 알려지게 됩니다.

**4. BGP 설정**



**4.1 R1 (AS 1)**

```
router bgp 1
 bgp router-id 1.1.1.1
 nei 1.1.12.2 remote-as 2
 nei 1.1.14.4 remote-as 2
 net 1.1.1.0 mask 255.255.255.0
```

​	•	R1은 자기가 **AS 1**에 속한다고 선언(router bgp 1).

​	•	R2(1.1.12.2)와 R4(1.1.14.4)를 **remote-as 2**로 지정했습니다.

​	•	여기서 **R4가 실제로는 AS 2여야** 정상적으로 eBGP가 수립됩니다.

​	•	1.1.1.0/24(Loopback0)는 BGP로 advertise 합니다.



**4.2 R2 (AS 2)**

```
router bgp 2
 bgp router-id 1.1.2.2
 nei 1.1.12.1 remote-as 1
 nei 1.1.3.3 remote-as 2
 nei 1.1.3.3 update-source lo0
 net 1.1.2.0 mask 255.255.255.0
```

​	•	R2는 **AS 2**

​	•	R1(AS 1)과 eBGP, R3(AS 2)와 iBGP 형성

​	•	update-source lo0를 통해, R3와 iBGP 세션을 맺을 때 소스 IP를 Loopback0(1.1.2.2)로 사용합니다.



**4.3 R4 (AS 2라고 가정)**

```
router bgp 2
 bgp router-id 1.1.4.4
 nei 1.1.14.1 remote-as 1
 nei 1.1.3.3 remote-as 2
 nei 1.1.3.3 update-source lo0
 nei 1.1.3.3 next-hop-self
 net 1.1.4.0 mask 255.255.255.0
```

​	실제 코드에는 router bgp 4라고 되어 있으나, R1에서 remote-as 2로 잡았으므로 여기서는 **AS 2**로 가정하여 설명합니다.



​	•	R4도 **AS 2**

​	•	R1(AS 1)과 eBGP, R3(AS 2)와 iBGP 형성

​	•	next-hop-self 명령어를 통해, R4가 R3로부터 받은 경로를 다른 iBGP 이웃에게 전달할 때(혹은 반대 상황) next-hop을 자기 자신(R4) IP로 변경합니다.



**4.4 R3 (AS 2) - Route Reflector 설정**

```
router bgp 2
 net 1.1.3.0 mask 255.255.255.0
 nei IBGP peer-group
 nei IBGP remote-as 2
 nei IBGP update-source lo0
 nei IBGP route-reflector-client
 nei 1.1.2.2 peer-group IBGP
 nei 1.1.4.4 peer-group IBGP
```

​	•	R3 역시 **AS 2**

​	•	peer-group IBGP를 만들어서, 같은 AS 내의 iBGP 이웃들(R2, R4)을 묶습니다.

​	•	route-reflector-client를 지정함으로써, **R3가 RR(라우트 리플렉터)** 역할을 하게 됩니다. R2와 R4는 RR 클라이언트로 동작합니다.

​	•	이렇게 하면, R2 ↔ R4 간에 iBGP 풀메시를 직접 맺지 않아도, R3가 BGP 경로를 반사(reflect)해 줄 수 있습니다.

**5. R3 BGP 테이블 확인**

```
R3# show ip bgp 1.1.1.0
BGP routing table entry for 1.1.1.0/24, version 5
Paths: (2 available, best #1, table default)
  ...
  1, (Received from a RR-client)
    1.1.4.4 (metric 11) from 1.1.4.4 (1.1.4.4)
      Origin IGP, metric 0, localpref 100, valid, internal, best
  ...
  1, (Received from a RR-client)
    1.1.12.1 (metric 20) from 1.1.2.2 (1.1.2.2)
      Origin IGP, metric 0, localpref 100, valid, internal
```

​	•	R3 입장에서 **1.1.1.0/24**를 AS 1에서 들어온 경로를 두 개 학습했습니다.

​	•	하나는 R4 경유(1.1.4.4), 다른 하나는 R2 경유(1.1.2.2).

​	•	내부(iBGP) 경로이므로 AS-Path에 “1”만 찍혀 있습니다(AS 1에서 온 것이므로).

​	•	IGP metric(OSPF metric)이 R4 쪽이 11, R2 쪽이 20으로, R3에서 R4로 가는 경로 비용이 더 작기 때문에 **R4 경유 경로**가 Best로 선정되었습니다.

```
R3# show ip bgp update-group
BGP version 4 update-group 1, internal, Address Family: IPv4 Unicast
  ...
  Route-Reflector Client
  ...
  Has 2 members:
   1.1.2.2
   1.1.4.4
```

​	•	R2(1.1.2.2)와 R4(1.1.4.4)가 동일한 RR 클라이언트 그룹(IBGP)에 묶여 있음을 보여줍니다.

**6. R1에서의 BGP 라우팅 테이블**

```
R1# show ip route bgp
      1.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
B        1.1.2.0/24 [20/0] via 1.1.12.2, 00:06:46
B        1.1.3.0/24 [20/0] via 1.1.12.2, 00:06:05
B        1.1.4.0/24 [20/0] via 1.1.14.4, 00:06:36
```

​	•	R1 입장에서 BGP로 학습한 목적지:

​	•	1.1.2.0/24, 1.1.3.0/24 → **Next-hop: 1.1.12.2 (R2 방향)**

​	•	1.1.4.0/24 → **Next-hop: 1.1.14.4 (R4 방향)**



즉,

​	•	R1에서 R2나 R3의 네트워크로 갈 때는 R2 쪽 링크(e0/0)로 패킷이 나갑니다.

​	•	R1에서 R4의 네트워크로 갈 때는 R4 쪽 링크(e0/1)로 나갑니다.

**7. 패킷 이동 흐름 예시**

​	1.	**R1 → R2(1.1.2.2) 루프백 통신**

​	•	R1은 BGP 라우팅 테이블에서 1.1.2.0/24의 다음 홉을 1.1.12.2로 알고 있음

​	•	따라서 R1 → (e0/0) → R2 로 직접 전달

​	2.	**R1 → R3(1.1.3.3) 루프백 통신**

​	•	R1 BGP 테이블에 따르면 1.1.3.0/24도 R2(1.1.12.2) 경유

​	•	R1 → R2로 패킷 전달 후, R2는 OSPF를 통해 R3 경로(1.1.23.3) 알고 있으므로 R3로 전달

​	3.	**R1 → R4(1.1.4.4) 루프백 통신**

​	•	R1은 BGP로 1.1.4.0/24의 다음 홉을 1.1.14.4로 알고 있음

​	•	R1 → (e0/1) → R4로 직접 전달

​	4.	**R3 → R1(1.1.1.1) 루프백 통신**

​	•	R3 BGP 테이블에서 1.1.1.0/24 경로가 2개 (R2 경유, R4 경유)

​	•	IGP metric 등으로 인해 R4 경유(메트릭 11)가 Best → R3 → R4 → R1 흐름

**8. 구조상 중요한 포인트 요약**

​	1.	**OSPF와 BGP 혼합 구조**

​	•	R2–R3–R4는 OSPF로 내부 라우팅(IGP)을 수행.

​	•	R1은 AS 1로서 별도이며, R2/4와 eBGP 연결을 맺어 외부(BGP) 경로를 주고받음.

​	2.	**R2, R3, R4는 모두 AS 2(가정)**

​	•	따라서 R2 ↔ R3, R3 ↔ R4는 **iBGP**.

​	•	iBGP에서 풀메시(peering)를 줄이기 위해 **R3를 Route Reflector**로 설정하고, R2와 R4를 RR 클라이언트로 둠.

​	•	이렇게 하면 R2와 R4가 직접 iBGP 세션을 맺지 않아도, R3가 BGP 경로를 반사해 준다.

​	3.	**next-hop-self, update-source lo0**

​	•	iBGP 세션 시 Loopback을 소스로 잡는 것은 링크 장애 시 세션 안정성을 높이는 일반적인 관행.

​	•	next-hop-self는 eBGP ↔ iBGP 경로 전달 시 유용. R4가 R3로부터 배운 외부 경로를 다시 iBGP 이웃에게 보낼 때 자신의 인터페이스 IP를 next-hop으로 세팅한다.

​	4.	**passive-interface(OSPF)**

​	•	R2에서 passive-interface e0/0를 통해 R1과는 OSPF 인접을 형성하지 않음.

​	•	R1은 OSPF를 쓰지 않으므로, 불필요한 OSPF 헬로우 트래픽을 막기 위함.

​	5.	**BGP 경로 선정**

​	•	동일 AS 내부(iBGP)에서 학습한 경로는 우선 **Local Preference**가 같다면, 다음으로 IGP cost 등을 비교하게 되며, 그 결과 R3에서 R4를 통해 들어온 경로를 더 선호하게 되는 장면이 show ip bgp 1.1.1.0에서 확인됨.

​	6.	**AS 번호 불일치 이슈(주의)**

​	•	실제 설정에서 R4가 router bgp 4라고 되어 있고, R1에서는 neighbor 1.1.14.4 remote-as 2로 설정되어 있으므로, 그대로라면 BGP 세션이 수립되지 않습니다.

​	•	실습상 R4도 AS 2로 변경하거나, R1 설정에서 R4를 remote-as 4로 바꾸는 식으로 일관되게 맞춰야 합니다.

**결론**

​	•	**인터페이스/IP**: 물리적 연결을 위한 기본 세팅(각 라우터 간 p2p 서브넷 + Loopback).

​	•	**OSPF**: R2–R3–R4 내부 라우팅용 IGP, R1은 미참여.

​	•	**BGP**:

​	•	R1(AS 1) ↔ R2/4(AS 2) eBGP

​	•	R2(AS 2) ↔ R3(AS 2) iBGP

​	•	R3(AS 2) ↔ R4(AS 2) iBGP (R3가 Route Reflector, R2/4는 RR Client)

​	•	**패킷 이동**:

​	•	R1에서 R2·R3 쪽 목적지는 R2 인터페이스로, R4 쪽 목적지는 R4 인터페이스로 나감.

​	•	R3에서 R1 쪽 목적지는 R4 경유가 더 낮은 IGP 비용으로 선택됨.

​	•	**핵심 개념**: Route Reflector(풀메시 최소화), eBGP vs iBGP 구분, OSPF–BGP 혼합 디자인, next-hop-self와 update-source lo0 등의 활용.



이로써 주어진 코드 전반에 대한 해설, 구조상의 중요 포인트, 그리고 실제 트래픽 흐름(라우팅 결정 과정)을 모두 살펴보았습니다.

실제 실습 환경에서 동작을 검증하려면, **AS 번호 정합**(R4가 AS 2인지 AS 4인지)을 먼저 맞추고 나서 show ip bgp summary, show ip route, ping/traceroute 등을 통해 최종 동작을 확인하시면 됩니다.