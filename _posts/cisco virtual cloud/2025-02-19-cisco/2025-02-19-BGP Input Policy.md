---
title: <h0>BGP Input Policy</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-19-cisco]
tags: [Network, BGP]












---

# 입력 경로 조정 TEST

![TalkMedia_i_2d6b89b4a282.png](/assets/cisco_post_img/2025-02-19-BGP Input Policy/TalkMedia_i_2d6b89b4a282.png.png)

**인터페이스 활성화 및 IP 주소 부여**

### R1

```bash
int lo0
ip add 1.1.1.1 255.255.255.0
no sh
int e0/0
ip add 1.1.12.1 255.255.255.0
no sh
int e0/1
ip add 1.1.14.1 255.255.255.0
no sh
```

### R2

```bash
int lo0
ip add 1.1.2.2 255.255.255.0
no sh
int e0/0
ip add 1.1.12.2 255.255.255.0
no sh
int e0/1
ip add 1.1.23.2 255.255.255.0
no sh
```

### R3

```bash
int lo0
ip add 1.1.3.3 255.255.255.0
no sh
int e0/1
ip add 1.1.23.3 255.255.255.0
no sh
int e0/0
ip add 1.1.34.3 255.255.255.0
no sh
```

### R4

```bash
int lo0
ip add 1.1.4.4 255.255.255.0
no sh
int e0/0
ip add 1.1.34.4 255.255.255.0
no sh
int e0/1
ip add 1.1.14.4 255.255.255.0
no sh
```



------

## OSPF 설정



### R2

```bash
router ospf 2
router-id 1.1.2.2
passive-interface e0/0 
int lo 0
ip ospf 2 area 0
ip ospf network point-to-point
int e0/0
ip ospf 2 area 0
int e0/1
ip ospf 2 area 0
```

### R3

```bash
router ospf 3
router-id 1.1.3.3
int lo 0
ip ospf 3 area 0
ip ospf network point-to-point
int e0/0
ip ospf 3 area 0
int e0/1
ip ospf 3 area 0
```

### R4

```bash
router ospf 4
router-id 1.1.4.4
int lo 0
ip ospf 4 area 0
ip ospf network point-to-point
int e0/0
ip ospf 4 area 0
int e0/1
ip ospf 4 area 0
```



------

## BGP 설정

**R1**

```bash
router bgp 1
bgp router-id 1.1.1.1
nei 1.1.12.2 remote-as 2
nei 1.1.14.4 remote-as 2
net 1.1.1.0 mask 255.255.255.0
```

**R2**

```bash
router bgp 2
bgp router-id 1.1.2.2
nei 1.1.12.1 remote-as 1
nei 1.1.3.3 remote-as 2
nei 1.1.3.3 update-source lo 0
net 1.1.2.0 mask 255.255.255.0
```

**R4**

```bash
router bgp 2
bgp router-id 1.1.4.4
nei 1.1.14.1 remote-as 1
nei 1.1.3.3 remote-as 2
nei 1.1.3.3 update-source lo 0
nei 1.1.3.3 next-hop-self
net 1.1.4.0 mask 255.255.255.0
```



------

## 피어 그룹 설정

```bash
R3(config-if)#router bgp 2
R3(config-router)#net 1.1.3.0 mask 255.255.255.0
R3(config-router)#nei IBGP peer-group 
R3(config-router)#nei IBGP remote-as 2  
R3(config-router)#nei IBGP update-source lo0
R3(config-router)#nei IBGP route-reflector-client 
R3(config-router)#nei 1.1.2.2 peer-group IBGP
R3(config-router)#nei 1.1.4.4 peer-group IBGP
R3(config-router)#
*Feb 19 08:36:39.820: %BGP-5-ADJCHANGE: neighbor 1.1.2.2 Up 
R3(config-router)#
*Feb 19 08:36:45.997: %BGP-5-ADJCHANGE: neighbor 1.1.4.4 Up 
R3(config-router)#exit
```

* IBGP 라는 이름을 가진 피어 그룹 생성
* 피어그룹 멤버들에게 적용시킬 출력정책을 설정



------

## R3의 BGP 테이블

```bash

R3(config)#do sh ip bgp 1.1.1.0
BGP routing table entry for 1.1.1.0/24, version 5
Paths: (2 available, best #1, table default)
  Advertised to update-groups:
     1         
  Refresh Epoch 1
  1, (Received from a RR-client)
    1.1.4.4 (metric 11) from 1.1.4.4 (1.1.4.4)
      Origin IGP, metric 0, localpref 100, valid, internal, best
      rx pathid: 0, tx pathid: 0x0
  Refresh Epoch 1
  1, (Received from a RR-client)
    1.1.12.1 (metric 20) from 1.1.2.2 (1.1.2.2)
      Origin IGP, metric 0, localpref 100, valid, internal
      rx pathid: 0, tx pathid: 0
R3(config)#

```



```bash
R3(config)#do sh ip bgp update-group
BGP version 4 update-group 1, internal, Address Family: IPv4 Unicast
  BGP Update version : 6/0, messages 0
  Route-Reflector Client
  Topology: global, highest version: 6, tail marker: 6
  Format state: Current working (OK, last not in list)
                Refresh blocked (not in list, last not in list)
  Update messages formatted 6, replicated 8, current 0, refresh 0, limit 1000
  Number of NLRIs in the update sent: max 1, min 0
  Minimum time between advertisement runs is 0 seconds
  Has 2 members:
   1.1.2.2          1.1.4.4         

R3(config)#

```



------

## 기본적인 BGP 네트워크의 입 출력 경로

```bash
R1(config-router)#do sh ip route bgp
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

      1.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
B        1.1.2.0/24 [20/0] via 1.1.12.2, 00:06:46
B        1.1.3.0/24 [20/0] via 1.1.12.2, 00:06:05
B        1.1.4.0/24 [20/0] via 1.1.14.4, 00:06:36

```

