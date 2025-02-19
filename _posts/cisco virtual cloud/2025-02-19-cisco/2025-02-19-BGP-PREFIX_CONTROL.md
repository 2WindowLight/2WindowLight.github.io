---
title: <h0>BGP PREFIX Control</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-19-cisco]
tags: [Network]










---

# BGP Prefix Control

![TalkMedia_i_2d6b89b4a282.png](/assets/cisco_post_img/2025-02-19-BGP-PREFIX_CONTROL/TalkMedia_i_2d6b89b4a282.png.png)

인터페이스 설정

R1

```bash
int lo0
ip address 1.1.1.1 255.255.255.0
no sh
int 0/0
ip add 1.1.12.1 255.255.255.0
no sh
```

R2

```bash
int lo 0
ip add 1.1.2.2 255.255.255.0
no sh
int e0/0
ip add 1.1.12.2 255.255.255.0
no sh
int e1/0
ip add 1.1.23.2 255.255.255.0
no sh
```

R3

```bash
int lo 0
ip add 1.1.3.3 255.255.255.0
no sh
int e1/0
ip add 1.1.23.3 255.255.255.0
no sh
int e0/0
ip add 1.1.34.3 255.255.255.0
no sh
```

R4

```bash
conf t
int lo 0
ip add 1.1.4.4 255.255.255.0
no sh
int e0/0
ip add 1.1.34.4 255.255.255.0
no sh
```

------



BGP 설정

R1

```bash
conf t
router bgp 1
bgp router-id 1.1.1.1
neighbor 1.1.12.2 remote-as 2
net 1.1.1.0 mask 255.255.255.0
```



R2

```bash
router bgp 2
bgp router-id 1.1.2.2
nei 1.1.12.1 remote-as 1
nei 1.1.23.3 remote-as 3
net 1.1.2.0 mask 255.255.255.0
```



R3

```bash
router bgp 3
bgp router-id 1.1.3.3
nei 1.1.23.2 remote-as 2
nei 1.1.34.4 remote-as 4
net 1.1.3.0 mask 255.255.255.0
```



R4

```bash
router bgp 4
bgp router-id 1.1.4.4
nei 1.1.34.3 remote-as 3
net 1.1.4.0 mask 255.255.255.0
```

------

R1의 BGP 테이블

```bash
R1(config-router)#do sh ip bgp
BGP table version is 5, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.1.0/24       0.0.0.0                  0         32768 i
 *>  1.1.2.0/24       1.1.12.2                 0             0 2 i
 *>  1.1.3.0/24       1.1.12.2                               0 2 3 i
 *>  1.1.4.0/24       1.1.12.2                               0 2 3 4 i

```

R1의 라우팅 테이블

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

      1.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
B        1.1.2.0/24 [20/0] via 1.1.12.2, 00:00:21
B        1.1.3.0/24 [20/0] via 1.1.12.2, 00:00:21
B        1.1.4.0/24 [20/0] via 1.1.12.2, 00:00:21
R1(config-router)#

```

------

원격 네트워크와의 통신 확인

```bash
R1(config-router)#do ping 1.1.2.2 source 1.1.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.2.2, timeout is 2 seconds:
Packet sent with a source address of 1.1.1.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/4/5 ms
R1(config-router)#do ping 1.1.3.3 source 1.1.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.3.3, timeout is 2 seconds:
Packet sent with a source address of 1.1.1.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
R1(config-router)#do ping 1.1.4.4 source 1.1.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.4.4, timeout is 2 seconds:
Packet sent with a source address of 1.1.1.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/3/5 ms
R1(config-router)#

```

