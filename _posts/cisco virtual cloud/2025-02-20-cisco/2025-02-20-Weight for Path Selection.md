---
title: <h0>Weight for Path Selection</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-20-cisco]
tags: [Network, BGP]











---

# 웨이트를 이용한 출력경로 조정

![IMG_0533](/assets/cisco_post_img/2025-02-20-Weight for Path Selection/IMG_0533.jpeg)

### 

> [!CAUTION]
>
> ### 가정 시나리오
>
> **위 그림과 같이 AS 2 의 R2, R3에서는 목적지가 AS 1의 1.1.1.0 네트워크인 패킷을 R2 - R1 간의 링크로 라우팅을 시킨다.**
>
> **이를 위해서는 앞서처럼 R2에서 로컬 플레퍼런스를 조정한 후 R4에서는 다음과 같이 R1에 대한 웨이트를 R3보다 높게 설정한다.**

------

### R4에서 웨이트 조정하기

```bash
R4(config)#router bgp 2
R4(config-router)#nei 1.1.14.1 wei
R4(config-router)#nei 1.1.14.1 weight 1000
R4(config-router)#end
R4#clear ip bgp * soft
```

설정 후 R4의 BGP 테이블에서 1.1.1.0 네트워크에 대한 넥스트 홉이 1.1.14.1 로 변경된다.

```bash
R4#sh ip bgp
BGP table version is 28, local router ID is 1.1.4.4
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  0.0.0.0          1.1.14.1                 0          1000 1 i
 *>  1.1.0.0/16       0.0.0.0                            32768 i
 * i                  1.1.2.2                  0    100      0 i
 s>  1.1.1.0/24       1.1.14.1                 0          1000 1 i
 r>i 1.1.3.0/24       1.1.3.3                  0    100      0 i
 s>  1.1.4.0/24       0.0.0.0                  0         32768 i
R4#

```

**그러나 R4에서 웨이트를 사용하여 경로를 조정하였으므로, 다른 라우터인 R2와 R3의 출력 경로에는 영향을 미치지 않는다. R3의 BGP 테이블을 보면 다음과 같이 1.1.1.0 네트워크에 대한 넥스트 홉 IP가 로컬 프레퍼런스 값이 200인 1.1.12.1 로 변함이 없다.**

------

### 추가) 로컬 프레퍼런스와 웨이트를 동시에 조정하기

```bash
R4(config)# ip prefix-list R1 permit 1.1.1.0/24
R4(config)# route-map EGRESS
R4(config-route-map)# match ip address prefix-list R1
R4(config-route-map)# set local-preference 50
R4(config-route-map)# set weight 1000
R4(config-route-map)# exit
R4(config)# router bgp 2
R4(config-router)# neighbor 1.1.14.1 route-map EGRESS in
```

