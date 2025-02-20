---
title: <h0>Origin Type Path</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-20-cisco]
tags: [Network,BGP]















---

# 오리진 타입을 이용한 입력 경로 조정





> [!IMPORTANT]
>
> ## **1. BGP Origin Type이란?**
>
> 
>
> BGP 경로 정보에는 “이 경로가 어떻게 BGP에 들어오게 되었는지”를 나타내는 **Origin 코드**가 포함됩니다. 표준 BGP에서 정의하는 Origin 코드는 다음 세 가지입니다.
>
> 1. **i (IGP)**
>
> ​	•	network 명령으로 **동일 AS 내부에서** 직접 등록한 경로이거나, **redistribute connected** 등을 통해 **정상적으로** BGP에 진입한 경우.
>
> ​	•	가장 **선호도**가 높습니다(다른 조건이 동일하다면).
>
> 2. **e (EGP)**
>
> ​	•	예전 프로토콜(EGP)에서 유래한 경로.
>
> ​	•	현대에는 거의 사용되지 않으므로 실제 장비에서 볼 일은 드물지만, 규정상 IGP 다음으로 선호됩니다.
>
> 3. **? (incomplete)**
>
> ​	•	**정확한 출처를 알 수 없는** 경로. 예를 들어 **redistribute static**이나 **route-map**을 통해 강제로 BGP에 삽입했을 때.
>
> ​	•	**가장 선호도가 낮습니다**(다른 조건이 동일하다면).
>
> 
>
> **BGP 경로 선택 규칙**에서 **Origin Type**은 Weight, Local Preference, AS-Path, MED 등 우선순위가 높은 속성들이 모두 동일할 때 비교됩니다. 즉, “**Origin Type: IGP(i)** < EGP(e) < Incomplete(?)” 순으로 **i**가 가장 선호되고, **?**가 가장 덜 선호됩니다.
>
> 오리진 타입을 이용한 입력 경로 조정은 특정 네트워크의 오리진 타입을 서로 다르게 하여 다른 AS의 BGP 경로 설정에 영향을 미치는 것을 말합니다.

------

![IMG_0530](/assets/cisco_post_img/2025-02-20-Origin type path/IMG_0530.jpeg)

AS 2에서 네트워크를 BGP에 포함시킬 때 모두 network 명령어를 사용했기 때문에 모든 네트워크의 BGP 오리진 타입이 IGP입니다. 따라서, R4에서만 오리진 타입을 'INCOMPLETE'로 조정하면 AS 1 에서 BGP 경로 결정시 상대적으로 우선 순위가 높은 R2로 라우팅됩니다.

------

## **시나리오 개요**

**AS 1**: R1

Loopback: 1.1.1.0/24

**AS 2**: R2, R3, R4

R2: 1.1.2.0/24

R3: 1.1.3.0/24

R4: 1.1.4.0/24



------

R1은 AS 1, R2/R3/R4는 AS 2에 속해 있으며, R1과 R2(1.1.12.x/24), R1과 R4(1.1.14.x/24)로 eBGP 세션이 맺혀 있다고 가정합니다.

​	•	R2와 R4는 **동일한 AS(AS 2) 내**에서 1.1.2.0/24, 1.1.3.0/24, 1.1.4.0/24를 BGP로 R1에게 광고합니다.

​	•	보통은 **Origin Type**이 모두 i(IGP)로 동일하게 잡혀서 R1이 AS-Path, MED 등을 비교합니다.



**“오리진 타입을 달리 설정”하는 목적**

​	•	R4에서 **Origin을 ? (incomplete)**로 바꿔서 광고하면,

​	•	R1 입장에서는 **R2(Origin i)**와 **R4(Origin ?)** 중에 **동일한 AS-Path 길이**라면, Origin이 i인 R2 경로를 **더 선호**하게 됩니다.

​	•	즉, **인바운드 트래픽**이 R2 쪽으로 몰리도록 유도할 수 있습니다.

------

### **R4에서 Origin을 ?로 설정**

```bash
R4(config)# ip prefix-list AS2-NETWORK permit 1.1.2.0/24
R4(config)# ip prefix-list AS2-NETWORK permit 1.1.3.0/24
R4(config)# ip prefix-list AS2-NETWORK permit 1.1.4.0/24

R4(config)#route-map CHANGE-ORIGIN-TYPE
R4(config-route-map)#match ip add prefix
R4(config-route-map)#match ip add prefix-list AS2-NETWORK
R4(config-route-map)#set origin incomplete
R4(config-route-map)#exit
R4(config)#router bgp 2
R4(config-router)#neighbor 1.1.14.1 route-map CHANGE-ORIGIN-TYPE out
R4(config-router)#end 
R4#so
*Feb 20 03:06:13.236: %SYS-5-CONFIG_I: Configured from console by console
R4#clear ip bgp * soft
R4#
```

prefix-list AS2-NETWORK로 AS 2의 내부 네트워크(1.1.2.0/24, 1.1.3.0/24, 1.1.4.0/24)를 매치

set origin incomplete 명령어로 **Origin Code를 ?(incomplete)**로 설정

R4가 R1(1.1.14.1)에게 경로를 광고할 때 이 설정을 적용(out)

------

 **R1에서의 결과 확인**

```bash
R1(config)#do sh ip bgp
BGP table version is 15, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.1.0/24       0.0.0.0                  0         32768 i
 *   1.1.2.0/24       1.1.14.4                               0 2 ?
 *>                   1.1.12.2                 0             0 2 i
 *>  1.1.3.0/24       1.1.12.2                               0 2 i
 *                    1.1.14.4                               0 2 ?
 *>  1.1.4.0/24       1.1.12.2                               0 2 i
 *                    1.1.14.4                 0             0 2 ?

```

R1은 1.1.2.0/24, 1.1.3.0/24, 1.1.4.0/24에 대해 **두 경로**를 받습니다.

​	•	R2가 광고하는 경로: **Origin = i**

​	•	R4가 광고하는 경로: **Origin = ?**

나머지 BGP 속성(Weight, Local Pref, AS-Path, MED)이 같다면, **Origin이 i인 경로를 더 선호**합니다.

따라서 R1은 **R2 경유**를 **Best Path**로 선택 → **인바운드 트래픽**이 R2 쪽으로 유입.

------

**5. 인바운드 경로 조정 측면에서의 의의**

​	•	**인바운드(입력) 경로**를 조정하려면, **원격 AS**(여기서는 R1)가 **어떤 경로를 더/덜 선호**하게끔 BGP 속성을 조정해야 합니다.

​	•	대표적으로 **MED**, **AS-Path Prepend**, **Community**(상대가 Local Pref를 변경하도록 요청), 그리고 **Origin Type**을 다르게 설정하는 방법이 있습니다.

​	•	**Origin Type**을 “incomplete”로 바꿔놓으면, 상대방(원격 AS)이 동일한 AS-Path 길이를 가진 경로 중에서 **Origin i**를 우선 선택하게 되어, **내가 원치 않는 경로**(여기서는 R4 경유)를 **덜 선호**하도록 만들 수 있습니다.



단, **Origin Type** 조정은 실무에서 **우선순위가 가장 높은 기법**은 아닙니다.

------

**. 정리**

1. **BGP Origin Type**

​	•	**i (IGP)**: 가장 선호

​	•	**e (EGP)**: 중간

​	•	**? (incomplete)**: 가장 덜 선호

​	•	BGP 경로 선택 규칙에서 **Weight, Local Pref, AS-Path**가 같다면 **Origin**을 비교해 경로를 결정.

2. **설정 원리**

​	•	route-map ... set origin incomplete로 특정 프리픽스의 Origin을 ?로 변경

​	•	**상대방(원격 AS)**은 이 경로를 다른 경로(i)보다 **덜 선호**하게 됨.

3. **인바운드 경로 조정 효과**

​	•	원격 AS에서 **i** 경로가 우선되므로, **내 AS로 들어오는** 트래픽을 다른 라우터(Origin i) 쪽으로 유도.

​	•	다른 기법(AS-Path Prepend, MED 등)과 조합하여 세밀하게 트래픽 경로를 조정 가능.

4. > [!WARNING]
   >
   > Origin Type은 **BGP 선택 규칙의 중간** 우선순위이며, Weight/Local Pref/AS-Path 길이 등이 우선적으로 비교됨.
   >
   > 실무에서는 **MED, AS-Path Prepend, Local Pref, Community** 등이 더 많이 쓰이지만, Origin Type도 **경로 우선순위**를 미세하게 조정할 때 유용할 수 있음.

