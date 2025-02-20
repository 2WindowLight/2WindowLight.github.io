---
title: <h0>BGP Local Preference for Outbound Path Selection</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-20-cisco]
tags: [Network, BGP]












---

# 출력 경로 조정

**1. 로컬 프리퍼런스(Local Preference)란?**

**정의**

​	•	BGP 경로 선택 시 **Weight** 다음으로 우선순위가 높은 속성(표준 BGP 속성).

​	•	**AS 내부(iBGP)**에서만 유효하며, **값이 높을수록** 선호되는 경로가 됩니다.

​	•	기본값은 보통 100(벤더마다 다를 수 있음).

​	•	한 라우터에서 설정한 Local Preference는 **iBGP를 통해 AS 내부 전체**에 전파되므로, **AS 내 모든 라우터가** 동일하게 그 경로를 선호하게 됩니다.

**주 사용 목적**

​	•	**Outbound 트래픽**(내 AS → 외부 목적지) 경로를 선택할 때, 특정 이웃(출구 라우터)을 더 선호하도록 만들기 위해 사용합니다.

​	•	예: “우리 AS에서 1.1.1.0/24로 가는 트래픽은 반드시 R2를 통해 나가게 하고 싶다.” → R2에서 Local Preference를 높게 설정

------

![IMG_0532](/assets/cisco_post_img/2025-02-20-route fix/IMG_0532.jpeg)

**2. 시나리오 개요**

​	•**AS 1**: R1 (Loopback 1.1.1.0/24)

​	•**AS 2**: R2, R3, R4

​	•R2와 R4가 R1과 eBGP 연결 (예: R2 ↔ R1: 1.1.12.x/24, R4 ↔ R1: 1.1.14.x/24)

​	•R2, R3, R4는 AS 2 내부에서 iBGP (또는 OSPF+BGP)로 연결



**목표**

​	•1.1.1.0/24(AS 1)로 **AS 2 내부**에서 나가는 트래픽(Outbound)을 **R2 경유**로 고정(더 선호)하고 싶음.

​	•따라서 **R2**가 R1으로부터 1.1.1.0/24 경로를 학습할 때, **Local Preference를 200**으로 설정하여 iBGP로 R3, R4에게 전달 → R3, R4 모두 “1.1.1.0/24로 갈 때 R2 쪽이 우선”이라 판단.

------

**R2에서 Local Preference 설정**

```bash
R2(config)#ip prefix-list R1 permit 1.1.1.0/24
!목적지에 대해 매치
R2(config)#route-map EGRESS
R2(config-route-map)#match ip add pre
R2(config-route-map)#match ip add prefix-list R1
R2(config-route-map)#set local-preference 200
R2(config-route-map)#exit
R2(config)#router bgp 2
R2(config-router)#nei 1.1.12.1 route-map EGRESS in
! R1(1.1.12.1)에서 들어오는 BGP 업데이트 적용
R2(config-router)#end
R2#clear ip bgp * soft
```

​	**이유**: neighbor X route-map Y in은 “**X**에서 **들어오는** BGP 경로”에 route-map을 적용한다는 의미.

​	•	R2가 R1으로부터 1.1.1.0/24를 학습할 때, **로컬 프리퍼런스를 200**으로 설정.

​	•	이후 R2가 이 경로를 iBGP로 R3, R4에게 리플렉트(혹은 단순 전파)할 때, Local Pref=200이 그대로 전달됩니다.

------

**검증 (R2, R3, R4에서의 경로 확인)**

**R2의 BGP 테이블**

```bash
R2#sh ip bgp 1.1.1.0
BGP routing table entry for 1.1.1.0/24, version 13
Paths: (1 available, best #1, table default)
  Advertised to update-groups:
     6         
  Refresh Epoch 4
  1
    1.1.12.1 from 1.1.12.1 (1.1.1.1)
      Origin IGP, metric 0, localpref 200, valid, external, best
      rx pathid: 0, tx pathid: 0x0
R2#

```

​	•	R2가 R1에서 받은 경로에 **Local Pref=200**이 적용되어 “best”로 선택됨.

------

**R3의 BGP 테이블**

```bash
R3(config)#do sh ip bgp 1.1.1.0
BGP routing table entry for 1.1.1.0/24, version 13
Paths: (1 available, best #1, table default)
  Advertised to update-groups:
     2         
  Refresh Epoch 3
  1, (Received from a RR-client)
    1.1.12.1 (metric 20) from 1.1.2.2 (1.1.2.2)
      Origin IGP, metric 0, localpref 200, valid, internal, best
      rx pathid: 0, tx pathid: 0x0
R3(config)#

```

R3는 iBGP로 R2에게서 전달받은 경로(Local Pref=200)를 학습.

다른 경로(예: R4 경유)는 Local Pref=100(기본값)이므로, R3는 200이 더 높아 R2를 통해 나가는 경로를 선호.

------

**R4의 BGP 테이블**

```bash
(config-router)#do sh ip bgp 1.1.1.0
BGP routing table entry for 1.1.1.0/24, version 18
Paths: (2 available, best #1, table default)
  Advertised to update-groups:
     9         
  Refresh Epoch 12
  1
    1.1.12.1 (metric 30) from 1.1.3.3 (1.1.3.3)
      Origin IGP, metric 0, localpref 200, valid, internal, best
      Originator: 1.1.2.2, Cluster list: 1.1.3.3
      rx pathid: 0, tx pathid: 0x0
  Refresh Epoch 12
  1
    1.1.14.1 from 1.1.14.1 (1.1.1.1)
      Origin IGP, metric 0, localpref 100, valid, external
      rx pathid: 0, tx pathid: 0

```

------

**R4의 BGP 라우팅**

```bash
R4(config-router)#do sh ip route        
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      1.0.0.0/8 is variably subnetted, 11 subnets, 2 masks
B        1.1.1.0/24 [200/0] via 1.1.12.1, 00:04:33

....
R4(config-router)#

```

BGP 경로로 1.1.1.0/24를 학습했으며, **Local Preference=200**인 경로(R2 경유)를 사용.

------

**동작 원리**

1. **R2 ↔ R1 eBGP**:

​	•	R2가 R1(AS 1)로부터 1.1.1.0/24 경로를 수신.

​	•	Route-map(EGRESS in)을 통해 이 경로의 Local Pref를 200으로 변경.

2. **iBGP 전파(R2 ↔ R3, R4)**:

​	•	R2는 이 경로를 iBGP로 R3, R4에게 전송하면서 **Local Pref=200**을 그대로 포함.

​	•	R3, R4는 “Local Pref=200”이 붙은 경로를 수신 → 같은 목적지에 대해 다른 경로가 있다면(예: R4가 R1과 eBGP 맺고 있다면), 보통 Local Pref=100(기본)보다 200이 우선

​	•	따라서 **R3, R4 모두 R2 → R1 경로**를 선택.

3. **결과**:

​	•	AS 2 내부에서 1.1.1.0/24로 향하는 **Outbound 트래픽**은 **R2**로 몰림.

​	•	Local Preference는 **AS 내부**에만 영향을 주므로, R1이 들어오는 경로(인바운드)는 이 설정으로 변화하지 않음.

------

**왜 “출력(Outbound) 경로 조정”인가?**

​	•	**Local Preference**는 **내 AS 내부**가 “어느 eBGP 라우터로 나가야 하는가”를 결정하게 합니다.

​	•	즉, **AS 2에서 AS 1로 가는 트래픽**(Out→)은 Local Pref가 높은 경로를 택하게 되므로, **출력 경로**가 R2로 고정됩니다.

​	•	반대로, **입력(Inbound) 경로**는 **상대방(AS 1)이 경로를 선택**하므로, Local Preference로는 제어할 수 없습니다(AS 1 라우터가 MED나 AS-Path Prepend 등을 봄).

> [!WARNING]
>
> **추가 개념 및 주의사항**
>
> 1. **neighbor ... in vs neighbor ... out**
>
> ​	•	**in**: R1 → R2로 **들어오는** 경로를 수정(여기서 Local Pref 설정)
>
> ​	•	**out**: R2 → R1로 **내보내는** 경로를 수정(MED, AS-Path, etc. 조정)
>
> ​	•	본 예시는 **AS 2에서 Outbound를 결정**하기 위해, **R1→R2** 경로에 Local Pref를 매겨서 AS 2 내부 전체가 그 경로를 선호하게 만듦.
>
> 2. **iBGP 동작**
>
> ​	•	Local Preference는 **iBGP 내**에서 공유됨.
>
> ​	•	R2가 설정하면, R3, R4도 자동으로 Local Pref=200인 경로를 학습.
>
> 3. **비교 우선순위**
>
> ​	•	BGP 경로 선택 규칙상, **Local Preference**는 **Weight**(Cisco 전용) 바로 다음으로 비교.
>
> ​	•	따라서 Weight가 0으로 같다면, Local Preference가 높은 쪽을 선택.

------

> [!NOTE]
>
> 1. **Local Preference**는 **내부 AS**가 “외부로 나갈 때” 어떤 BGP 경로를 더 선호할지 결정하는 **중요한 속성**.
>
> 2. **값이 높을수록** 우선순위가 높으며, **AS 전체**(iBGP)에서 공유됨.
>
> 3. **R2에서 neighbor ... in**으로 R1 경로를 수신할 때 Local Pref=200으로 설정 → **AS 2 내부** 라우터(R3, R4)도 이 경로를 “가장 선호” → **Outbound** 트래픽이 R2로 집중.
>
> 4. **출력 경로 조정**이란, **내 AS**가 외부 목적지로 나가는 경로를 어떻게 결정할지 설정하는 것 → Local Preference가 대표적인 방법.

​	