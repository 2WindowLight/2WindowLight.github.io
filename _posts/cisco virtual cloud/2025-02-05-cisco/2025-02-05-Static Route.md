---
title: <h0>Static Route</h0>
author: cotes   
categories: [cisco virtual cloud 2025-02-05-cisco]
tags: [Network]








---



## 정적 경로 (Static Route)

![111](/Users/changhee/2WindowLight.github.io/_posts/images/2025-02-05-Static Route/111.png)

해당 네트워크는 **R1 → R2 → R3 → R4** 순서로 연결된 라우터 환경이다.

R1과 R2의 **정적 경로(**ip route**) 설정이 다른 이유는, 각 라우터가 다른 네트워크를 직접 연결하고 있기 때문이다.**

### R2

```bash
int e0/3
ip address 1.1.32.2 255.255.255.0
no sh
```

### R3

```bash
int e0/3
ip address 1.1.32.3 255.255.255.0
```

### R1

```bash
ip route 1.1.2.0 255.255.255.0 1.1.12.2
ip route 1.1.3.0 255.255.255.0 1.1.12.2
ip route 1.1.4.0 255.255.255.0 1.1.12.2
ip route 1.1.23.0 255.255.255.0 1.1.12.2
ip route 1.1.32.0 255.255.255.0 1.1.12.2
ip route 1.1.34.0 255.255.255.0 1.1.12.2
```

•	R1은 **R2(R1과 직접 연결됨)**를 통해 다른 네트워크로 가야 합니다.

•	따라서, **모든 목적지(1.1.2.0~1.1.34.0)에 대한 경로를 R2(1.1.12.2)로 보냄**.

•	1.1.12.2는 **R2의 e0/0 인터페이스 IP**.

### R2

```bash
ip route 1.1.1.0 255.255.255.0 1.1.12.1
ip route 1.1.3.0 255.255.255.0 1.1.23.3
ip route 1.1.4.0 255.255.255.0 1.1.32.3
ip route 1.1.34.0 255.255.255.0 1.1.23.3
```

 **과정:**

​	•	ip route 1.1.1.0 255.255.255.0 1.1.12.1 → **1.1.1.0 네트워크(R1)를 가려면 1.1.12.1(R1)로 보냄**.

​	•	ip route 1.1.3.0 255.255.255.0 1.1.23.3 → **1.1.3.0 네트워크(R3)를 가려면 1.1.23.3(R3)로 보냄**.

​	•	ip route 1.1.4.0 255.255.255.0 1.1.32.3 → **1.1.4.0 네트워크(R4)를 가려면 1.1.32.3(R3)로 보냄**.

​	•	ip route 1.1.34.0 255.255.255.0 1.1.23.3 → **1.1.34.0 네트워크(R3)를 가려면 1.1.23.3(R3)로 보냄**.



**결론:**

​	•	**R1은 모든 목적지에 대해 R2(1.1.12.2)로 보냄** (R2가 경로를 알아서 처리).

​	•	**R2는 R1과 직접 연결된 네트워크(1.1.1.0)만 R1로 보내고, 나머지는 R3(R3를 거쳐야 하는 네트워크)로 전달**.



### **추가 R3, R4)**

### R3

```bash
ip route 1.1.1.0 255.255.255.0 1.1.23.2
ip route 1.1.1.0 255.255.255.0 1.1.32.2
ip route 1.1.2.0 255.255.255.0 1.1.23.2
ip route 1.1.2.0 255.255.255.0 1.1.32.2
ip route 1.1.4.0 255.255.255.0 1.1.34.4
ip route 1.1.12.0 255.255.255.0 1.1.23.2
ip route 1.1.12.0 255.255.255.0 1.1.32.2
```

### R4

```bash
ip route 1.1.1.0 255.255.255.0 1.1.34.3
ip route 1.1.2.0 255.255.255.0 1.1.34.3
ip route 1.1.3.0 255.255.255.0 1.1.34.3
ip route 1.1.12.0 255.255.255.0 1.1.34.3
ip route 1.1.23.0 255.255.255.0 1.1.34.3
ip route 1.1.32.0 255.255.255.0 1.1.34.3
```



## 정적 경로 라우터 구조 

![image-20250205174538092](/Users/changhee/2WindowLight.github.io/_posts/images/2025-02-05-Static Route/image-20250205174538092.png)

------

```bash
ip route 1.1.2.0 255.255.255.0 1.1.12.2
1.1.2.0 # 목적지 네트워크를 지정한다.

255.255.255.0 # 목적지 네트워크의 서브넷 마스크를 지정한다.

1.1.12.2 # 목적지 네트워크로 가기 위한 넥스트 홈 IP 주소를 지정한다.
# 정적 경로만을 이용하여 통신하려면 특정 라우터에 직접 접속되어 있지 않은 모든 네트워크에 대해서 정적 경로를 설정해주어야 한다.
# 설정 후 라우팅 테이블을 확인해 보면 다음과 같이 각 목적지 네트워크로 가는 경로가 설치되어 있다.
```



------

## floating static route

이중 링크간의 속도가 다른 네트워크가 있으면, 이 경우 저속 회전으로 연결되는 네트워크의 AD를 고속 회전보다 더 높게 설정해주면 된다. 

작은것을 라우팅 테이블로 올려서 높은 홉은 사라짐

### R2

```bash
ip route 1.1.3.0 255.255.255.0 1.1.32.3 10
```



```bash
IOU2(config)#ip route 1.1.3.0 255.255.255.0 1.1.32.3 10
IOU2(config)#do sh ip ro static
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

      1.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
S        1.1.1.0/24 [1/0] via 1.1.12.1
S        1.1.3.0/24 [1/0] via 1.1.23.3
S        1.1.4.0/24 [1/0] via 1.1.32.3
S        1.1.34.0/24 [1/0] via 1.1.32.3
                     [1/0] via 1.1.23.3
IOU2(config)#int e0/1
IOU2(config-if)#sh
IOU2(config-if)#
*Feb  5 07:01:27.876: %LINK-5-CHANGED: Interface Ethernet0/1, changed state to administratively down
*Feb  5 07:01:28.884: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to down

```



```bash
IOU2(config-if)#do sh ip ro static
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

      1.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
S        1.1.1.0/24 [1/0] via 1.1.12.1
S        1.1.3.0/24 [10/0] via 1.1.32.3
S        1.1.4.0/24 [1/0] via 1.1.32.3
S        1.1.34.0/24 [1/0] via 1.1.32.3
IOU2(config-if)#no sh             

```



```bash
IOU2(config-if)#do sh ip ro static
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

      1.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
S        1.1.1.0/24 [1/0] via 1.1.12.1
S        1.1.3.0/24 [1/0] via 1.1.23.3
S        1.1.4.0/24 [1/0] via 1.1.32.3
S        1.1.34.0/24 [1/0] via 1.1.32.3
                     [1/0] via 1.1.23.3

```

