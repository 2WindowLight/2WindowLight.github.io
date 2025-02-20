---
title: <h0>BGP Default route</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-20-cisco]
tags: [Network, BGP]













---

# BGP 디폴트 루트



ISP가 아닌 소규모의 AS라면 상세 네트워크 정보를 다 가질 필요 없이 디폴트 루트(default route)만 광고받으면 된다.

> [!TIP]
>
> ### **BGP 디폴트 루트를 만드려면 network 0.0.0.0 명령어를 사용하거나, 재분배 또는 neighbor default-originate 명령어를 사용할 수 있다.**

> [!IMPORTANT]
>
> **일반적으로 대규모 ISP 환경이 아닌, 소규모 AS에서는 모든 외부 네트워크에 대한 상세 경로 정보를 전부 수집할 필요가 없다. 이때는 디폴트 루트(Default Route) 하나만 받아서 “나머지 모든 목적지”를 한 경로로 보내도록 처리하면 편리하다.**
>
> **BGP에서도 이런 디폴트 루트를 설정하거나 광고할 수 있는 방법이 여러 가지가 존재한다.**



------

### network 0.0.0.0 명령어를 사용하여 디폴트 루트 생성

```bash
R1(config)#ip route 0.0.0.0 0.0.0.0 null 0
R1(config)#router bgp 1
R1(config-router)# network 0.0.0.0
```

* BGP는 라우팅 테이블에 있는 네트워크만 광고 된다. 따라서 디폴트 루트(0.0.0.0/0) 를 networ 명령어로 BGP 에 포함시키기 전에 먼저 라우팅 테이블에 저장시키기 위하여 이 명령어를 사용한다. 디폴트 루트를 이용하여 전달받은 트래픽을 다시 다른 곳으로 전하려면 null 0 인터페이스 대신 해당 네트워크로 가는 넥스트 홉을 지정하면 된다.

```bash
ip route 0.0.0.0 0.0.0.0 null 0
```

​	•	“0.0.0.0/0” 경로를 **Null0 인터페이스**로 라우팅하도록 **정적 라우트**를 설정한다.

​	•	BGP에서 network 명령어로 광고하려면, 먼저 해당 네트워크(0.0.0.0/0)가 라우팅 테이블에 존재해야 한다.

​	•	Null0 대신 실제 넥스트 홉을 지정하면, 외부 트래픽을 특정 인터페이스나 라우터로 전달할 수도 있다.

```bash
network 0.0.0.0
```

​	•	BGP 프로세스에 “0.0.0.0/0” 네트워크를 **광고**하도록 지시한다.

​	•	BGP는 라우팅 테이블에 존재하는 네트워크만 광고하므로, 위에서 설정한 정적 디폴트 라우트를 기반으로 0.0.0.0/0을 BGP에 포함시킨다.



------

### R1의 BGP 테이블

```bash
R1(config-router)#do sh ip bgp 0.0.0.0
BGP routing table entry for 0.0.0.0/0, version 40
Paths: (1 available, best #1, table default)
  Advertised to update-groups:
     1         
  Refresh Epoch 1
  Local
    0.0.0.0 from 0.0.0.0 (1.1.1.1)
      Origin IGP, metric 0, localpref 100, weight 32768, valid, sourced, local, best
      rx pathid: 0, tx pathid: 0x0
R1(config-router)#
```

**R1이 network 0.0.0.0을 통해 디폴트 루트를 BGP에 광고하면, R1의 BGP 테이블에는 0.0.0.0/0가 Local로 표시된다.**

------

### R2의 라우팅 테이블

```bash
R2(config-router)#do sh ip bgp
BGP table version is 20, local router ID is 1.1.2.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>i 0.0.0.0          1.1.4.4                  0    100      0 1 i
 *>  1.1.0.0/16       0.0.0.0                            32768 i
 s>  1.1.1.0/24       1.1.12.1                 0    200      0 1 i
 s>  1.1.2.0/24       0.0.0.0                  0         32768 i
 r>i 1.1.3.0/24       1.1.3.3                  0    100      0 i
R2(config-router)#
```

**R2나 다른 라우터들은 이 디폴트 루트를 iBGP나 eBGP로 학습하고, “모든 목적지에 대한 경로가 없으면 0.0.0.0/0로 보낸다”라는 동작을 하게 된다.**

------

## 재분배를 이용한 BGP 디폴트 루트 생성

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 null 0
R1(config)# router bgp
R1(config-router)# redistribute static
R1(config-router)# default-information originate
```

```
ip route 0.0.0.0 0.0.0.0 null 0
```

**0.0.0.0/0 정적 라우트를 라우팅 테이블에 등록한다.**

```
redistribute static
```

**BGP가 정적 라우트를 BGP 업데이트로 재분배하여 광고하게 만든다.**

**디폴트 라우트를 포함해 등록된 모든 정적 경로가 광고될 수 있다.**

```bash
default-information originate
```

**“디폴트 정보(0.0.0.0/0)를 무조건 BGP로 originate(기원)하도록” 강제한다.**

**다른 라우터들은 R1로부터 디폴트 루트를 확실히 수신할 수 있게 된다.**

------

## **neighbor default-originate 명령어를 사용하는 방법**

**neighbor <IP> default-originate** 명령어를 사용하면, 특정 이웃에게 **디폴트 루트를 originate**해서 보낼 수 있다.

예를 들어, neighbor 1.1.12.2 default-originate라고 하면, R1은 이웃인 1.1.12.2(R2)에게 디폴트 루트를 광고한다.

이웃 라우터는 0.0.0.0/0을 BGP 업데이트로 받게 되며, 라우팅 테이블에 0.0.0.0/0 경로가 생긴다.

보통 0.0.0.0/0 정적 라우트가 이미 라우팅 테이블에 있어야 제대로 작동한다.

------

**명령어별 기능 요약**

**ip route 0.0.0.0 0.0.0.0 <next-hop or null0>**

​	•	디폴트 정적 라우트를 라우팅 테이블에 등록한다.

​	•	BGP network 0.0.0.0 명령이나 redistribute static 등을 통해 광고하기 위한 기반이 된다.

**router bgp <AS> / network 0.0.0.0**

​	•	라우팅 테이블에 있는 0.0.0.0/0을 BGP가 광고하도록 지시한다.

​	•	BGP는 “라우팅 테이블에 존재하는 네트워크”만 광고한다.

**redistribute static**

​	•	정적 라우트를 BGP로 재분배한다.

​	•	디폴트 라우트를 포함해 등록된 모든 정적 경로를 광고할 수 있다.

**default-information originate**

​	•	“디폴트 루트를 무조건 BGP로 광고”하도록 강제한다.

​	•	redistribute static과 함께 사용하면, 디폴트 라우트가 항상 BGP에 포함된다.

**neighbor <IP> default-originate**

​	•	특정 이웃에게만 디폴트 루트를 originate하도록 지시한다.

​	•	이웃 라우터는 0.0.0.0/0을 BGP 업데이트로 받게 된다.