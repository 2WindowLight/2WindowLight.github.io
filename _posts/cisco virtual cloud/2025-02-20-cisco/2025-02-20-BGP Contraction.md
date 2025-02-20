---
title: <h0>BGP Contraction</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-20-cisco]
tags: [Network, BGP]














---

# BGP Contraction(BGP 축약)



### 기본적인 인터페이스 & BGP & IGP 설정 돼 있다는 가정에서의 시나리오



> [!TIP]
>
> ### aggregate-address 명령어를 이용한 축약

## 예제 BGP 네트워크 축약

```bash
R2(config)# router bgp 100
R2(config-router)# aggregate-address 1.1.0.0 255.255.0.0

R4(config)# router bgp 100
R4(config-router)# aggregate-address 1.1.0.0 255.255.0.0
```

설정 후 R2와 R4 는 축약된 네트워크에 대한 광고를 R1으로 전송합니다.

------

![IMG_0534](/assets/cisco_post_img/2025-02-20-BGP contraction/IMG_0534.jpeg)

R2의 라우팅 테이블을 보면 축약된 네트워크가 인스톨 되며, 게이트웨이가  null 0으로 지정되어 있습니다.

이것은 OSPF, EIGRP 등과 마찬가지로 축약된 네트워크에 포함된 상세 네트워크가 다운되었을때 라우팅 방지하기 위함

```bash
R2# show ip route bgp
...
B 1.1.0.0/16 [200/0] via 0.0.0.0 00:02:24, NULL 0
```



### R1 BGP 테이블

```bash
R1(config)#do sh ip bgp
BGP table version is 17, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *   1.1.0.0/16       1.1.14.4                 0             0 2 i
 *>                   1.1.12.2                 0             0 2 i
 *>  1.1.1.0/24       0.0.0.0                  0         32768 i
 *>  1.1.2.0/24       1.1.12.2                 0             0 2 i
 *>  1.1.3.0/24       1.1.12.2                               0 2 i
 *                    1.1.14.4                               0 2 i
 *>  1.1.4.0/24       1.1.14.4                 0             0 2 i

```

### show ip bgp 명령어를 사용하면 축약된 네트워크 

```bash
     Network          Next Hop            Metric LocPrf Weight Path
*   1.1.0.0/16       1.1.14.4                 0             0 2 i
```

### 전체 라우터에 대한 상세 네트워크를 볼 수 있습니다.

```bash
     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.1.0/24       0.0.0.0                  0         32768 i
 *>  1.1.2.0/24       1.1.12.2                 0             0 2 i
 *>  1.1.3.0/24       1.1.12.2                               0 2 i
 *                    1.1.14.4                               0 2 i
 *>  1.1.4.0/24       1.1.14.4                 0             0 2 i
```

------

## 축약 네트워크만 광고하기

### 축약시 summary-only 옵션 사용하기

```bash
R2(config)# router bgp 100
R2(config-router)# aggregate-address 2.2.0.0 255.255.252.0 summary-only
```

설정 후 R2의 BGP 테이블을 보면 상세 네트워크 앞에는 s(suppressed) 표시가 있습니다. 이것은 summary-only 옵션을 사용했기 때문에 상세 네트워크는 모두 광고 전송을 억제(suppressed) 했다는 의미입니다.

```bash
R2# show ip bgp
...
*> 2.2.0.0/22 	0.0.0.0  32768 i
s i2.2.1.0/24 	1.1.4.4 0 100 0 1 i
...
```

R2(1.1.2.2)에서 수신한 2.2.0.0/22 네트워크에 대한 상세 네트워크는 없고, 축약된 네트워크만 저장된다.