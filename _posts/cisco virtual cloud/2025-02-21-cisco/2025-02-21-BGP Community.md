---
title: <h0>BGP Community</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-21-cisco]
tags: [Network, BGP]











---

# BGP Community



![IMG_0535](/assets/cisco_post_img/2025-02-21-BGP_Community/IMG_0535.png)

### 인터페이스 활성화 및 IP 주소 부여

```bash
R1
conf t
int lo5
ip add 1.1.1.1 255.255.255.0
no sh
int e0/0 
ip add 1.1.12.1 255.255.255.0
no sh
int e0/1
ip add 1.1.13.1 255.255.255.0
no sh

R2
conf t
int lo5
ip add 1.1.2.2 255.255.255.0
no sh
int e0/0
ip add 1.1.12.2 255.255.255.0
no sh
int e0/2
ip add 1.1.24.2 255.255.255.0
no sh


R3
conf t
int lo5
ip add 1.1.3.3 255.255.255.0
no sh
int e0/1
ip add 1.1.13.3 255.255.255.0
no sh

R4
conf t
int lo5
ip add 1.1.4.4 255.255.255.0
no sh
int e0/2
ip add 1.1.24.4 255.255.255.0
no sh


서로 다른 as 이기때문에 헬로패킷을 보낼 필요가 없음 - passive

```

### OSPF 설정하기

```bash
R1
router ospf 1
router-id 1.1.1.1
int lo5
ip ospf network point-to-point
ip ospf 1 area 0
int e0/0
ip ospf 1 area 0
int e0/1
ip ospf 1 area 0
router ospf 1
passive-interface e0/1

R2
router ospf 1
router-id 1.1.2.2
int lo5 
ip ospf 1 area 0
int e0/0
ip ospf 1 area 0

```

### BGP 설정하기

```bash
R1
router bgp 12
bgp router-id 1.1.1.1
nei 1.1.13.3 remote-as 3
nei 1.1.2.2 remote-as 12
nei 1.1.2.2 update-source lo5
net 1.1.1.0 mask 255.255.255.0

R2
router bgp 12
bgp router-id 1.1.2.2
nei 1.1.24.4 remote-as 4
nei 1.1.1.1 remote-as 12
nei 1.1.1.1 update-source lo5
nei 1.1.1.1 next-hop-self
net 1.1.2.0 mask 255.255.255.0

R3
router bgp 3
bgp router-id 1.1.3.3
nei 1.1.13.1 remote-as 12
net 1.1.3.0 mask 255.255.255.0

R4
router bgp 4
bgp router-id 1.1.4.4
nei 1.1.24.2 remote-as 12
network 1.1.4.0 mask 255.255.255.0


```

결과

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
 r*>i 1.1.2.0/24       1.1.2.2                  0    100      0 i
 *>  1.1.3.0/24       1.1.13.3                 0             0 3 i
 *>i 1.1.4.0/24       1.1.2.2                  0    100      0 4 i

```



------

커뮤니티 값 설정

```bash
R3(config)#int lo 16
R3(config-if)#
*Feb 21 02:21:55.582: %LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback16, changed state to up
R3(config-if)#ip add 1.1.16.1 255.255.255.0
R3(config-if)#ip add 1.1.17.1 255.255.255.0 sec
R3(config-if)#ip add 1.1.18.1 255.255.255.0 sec
R3(config-if)#ip add 1.1.19.1 255.255.255.0 sec
R3(config-if)#exit
R3(config)#router bgp 3
R3(config-router)#net 1.1.16.0 mask 255.255.255.0
R3(config-router)#net 1.1.17.0 mask 255.255.255.0
R3(config-router)#net 1.1.18.0 mask 255.255.255.0
R3(config-router)#net 1.1.19.0 mask 255.255.255.0
R3(config-router)#exit
R3(config)#ip prefix-list List16 permit 1.1.16.0/24
R3(config)#ip prefix-list List17 permit 1.1.17.0/24
R3(config)#ip prefix-list List18 permit 1.1.18.0/24
R3(config)#ip prefix-list List19 permit 1.1.19.0/24
R3(config)#ip bgp new-format 
R3(config)#route-map Map1 10
R3(config-route-map)#match ip add prefix-list List16                       
R3(config-route-map)#set community 3:16
R3(config-route-map)#exit
R3(config)#route-map Map1 20
R3(config-route-map)#match ip add prefix-list List17   
R3(config-route-map)#set community internet 3:16 3:17
R3(config-route-map)#exit
R3(config)#route-map Map1 30
R3(config-route-map)#match ip add prefix-list List18
R3(config-route-map)#set community no-advertise 3:18
R3(config-route-map)#exit
R3(config)#route-map Map1 40
R3(config-route-map)#match ip add prefix-list List19
R3(config-route-map)#set community no-export 
R3(config-route-map)#exit
R3(config)#route-map Map1 50
R3(config-route-map)#exit
R3(config)#router bgp 3
R3(config-router)#nei 1.1.13.1 route-map Map1 out
R3(config-router)#nei 1.1.13.1 send-community 
R3(config-router)#

```

커뮤니티 값을 가진 모든 네트워크 확인

```bash
R1(config)#ip bgp new-format 
R1(config)#end
R1#
*Feb 21 02:33:48.097: %SYS-5-CONFIG_I: Configured from console by console
R1#sh ip bgp comm
BGP table version is 13, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.16.0/24      1.1.13.3                 0             0 3 i
 *>  1.1.17.0/24      1.1.13.3                 0             0 3 i
 *>  1.1.18.0/24      1.1.13.3                 0             0 3 i
 *>  1.1.19.0/24      1.1.13.3                 0             0 3 i

```

커뮤니티 값 3:16 을 가진 네트워크 확인

```bash
R1#sh ip bgp commu 3:16
BGP table version is 13, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.16.0/24      1.1.13.3                 0             0 3 i
 *>  1.1.17.0/24      1.1.13.3                 0             0 3 i

```

커뮤니티 값 3:16 만을 가진 네트워크 확인하기

```bash
R1#sh ip bgp commu 3:16 exact-match
BGP table version is 13, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.16.0/24      1.1.13.3                 0             0 3 i
```

R1의 BGP 테이블 확인

```bash
R1#sh ip bgp 1.1.17.0
BGP routing table entry for 1.1.17.0/24, version 12
Paths: (1 available, best #1, table default)
  Advertised to update-groups:
     1         
  Refresh Epoch 2
  3
    1.1.13.3 from 1.1.13.3 (1.1.3.3)
      Origin IGP, metric 0, localpref 100, valid, external, best
      Community: internet 3:16 3:17
      rx pathid: 0, tx pathid: 0x0
R1#

```



------

## 커뮤니티 값 전송 (nighbor send-community)

**send-community 명령어의 역할:**

기본적으로 BGP는 커뮤니티 속성을 피어에게 전송하지 않는다.

“neighbor X.X.X.X send-community” 명령어를 사용하면, 해당 이웃으로 커뮤니티 속성이 포함된 BGP 업데이트를 전송하도록 설정할 수 있다.

------

커뮤니티 값을 전송하기 전 

```bash
R2(config)#do sh ip bgp community
```



```bash
R1(config)# router bgp 12
R1(config-router)# neighbor 1.1.2.2 send-community
```

전송 후의 결과

```bash
R2(config)#do sh ip bgp community
BGP table version is 13, local router ID is 1.1.2.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>i 1.1.16.0/24      1.1.13.3                 0    100      0 3 i
 *>i 1.1.17.0/24      1.1.13.3                 0    100      0 3 i
 *>i 1.1.19.0/24      1.1.13.3                 0    100      0 3 i

```

