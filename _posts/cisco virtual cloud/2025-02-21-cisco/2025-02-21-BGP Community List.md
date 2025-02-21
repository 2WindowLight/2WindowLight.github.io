---
title: <h0>BGP Community List -2</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-21-cisco]
tags: [Network, BGP]














---

# 커뮤니티 리스트 (커뮤니티 값 지정)



**커뮤니티 리스트는 앞서와 같이 루트 맵에서 커뮤니티 값을 설정하여 네이버에게 전송하면, 해당 네이버는 커뮤니티 값에 따라 네트워크를 조정할 수 있다.**

이 때, no-export, no-advertise, local-as 등과 같은 well known community 는 별도로 조정하지 않아도 그 값에 따라 동작한다.

**즉, 특정 커뮤니티 값을 허용(permit) 또는 거부(deny)하는 리스트를 생성한다.**

> [!WARNING]
>
> 그러나 well known community 가 아닌 것들은 커뮤니티 리스트를 사용하여 해당 커뮤니티를 지정한 다음, 루트 맵에서 match 명령어와
>
> set 명령어를 사용하여 적당한 동작을 지정해야 한다.



**route-map:** BGP 업데이트에 대해 match 조건과 set 동작을 지정하는 정책 집합이다.



------

## 예제)

### 커뮤니티 값이 3:16인 네트워크를 지정하기

```bash
R1(config)# ip community-list 1 permit 3:16
```



### '3:' 으로 시작하는 커뮤니티 값을 가진 네트워크를 모두 지정하기

```bash
R1(config)# ip community-list 100 permit 3:.*
```



544p 

매치를 안쓰면 모든 부분

------

## 커뮤니티 값 지정



 **OSPF:**

각 라우터는 Loopback5와 연결 인터페이스를 OSPF Area 0에 포함시켜 기본 네트워크 연결을 확보한다. 일부 인터페이스는 passive로 설정하여 OSPF 트래픽을 줄인다.

**BGP 피어링:**

​	•	R1은 AS 12 내부의 R2와, AS 3에 속한 R3와 피어링을 구성한다.

​	•	R2는 R1과 내부 피어링을 구성하며, 또한 AS 4의 R4와 피어링한다.

------

### R1

```bash
R1(config)#ip community-list 1 permit 3:16
R1(config)#
R1(config)#route-map Map2 10
R1(config-route-map)#match community 1
R1(config-route-map)#set local-preference 1000
R1(config-route-map)#exit
R1(config)#router bgp 12
R1(config-router)#nei 1.1.13.3 route-map Map2 in
```



```bash
R1(config)# ip community-list 1 permit 3:16
```

**기능:**

* **ip community-list 1 permit 3:16:**
  * 커뮤니티 리스트 번호 1에 대해 커뮤니티 값 “3:16”만 허용하도록 새롭게 정의한다.

* **목적:**
  * 정책에서 사용할 정확한 커뮤니티 값(3:16)만 남기고, 불필요한 항목을 제거한다.

------

### **Route-map Map2 구성**

```bash
R1(config)# route-map Map2 10
R1(config-route-map)# match community 1
R1(config-route-map)# set local-preference 1000
R1(config-route-map)# exit
```

**기능 및 상세 설명:**

* **route-map Map2 10:**

  *  “Map2”라는 이름의 정책을 시퀀스 10으로 시작한다.

  * 시퀀스 번호는 처리 순서를 결정하며, 낮은 번호부터 먼저 평가된다.

* **match community 1:**
  * BGP 업데이트에 포함된 커뮤니티 값이 커뮤니티 리스트 1 (즉, “3:16”)에 일치하는지 검사한다.

* **set local-preference 1000:**

  * 조건에 일치하는 경로의 local-preference 값을 1000으로 설정한다.

  * AS 내부에서 local-preference 값은 경로 선택 시 우선순위에 영향을 주므로, 1000은 기본값(예, 100)보다 높은 우선순위를 의미한다.

* **목적:**
  * 커뮤니티 값 “3:16”을 가진 경로가 AS 내에서 더 선호되도록 R1에서 local-preference를 1000으로 변경한다.

------

**Route-map Map2 적용**

```bash
R1(config)# router bgp 12
R1(config-router)# nei 1.1.13.3 route-map Map2 in
```

**기능:**

​	R1의 BGP 설정에서 피어 1.1.13.3 (예를 들어, R3와의 피어링)에 대해 inbound 업데이트에 Map2를 적용한다.

* **“in” 방향:**
  * 이 설정은 R1이 피어로부터 받는 BGP 업데이트에 대해 정책을 적용한다.

**목적:**  피어로부터 수신된 경로 중 커뮤니티 값 “3:16”이 있는 경로에 대해 local-preference를 1000으로 설정하여, AS 내부에서 이 경로를 우선 선택하도록 한다.

------

### 값 변경 전

```bash
R1(config-router)#do sh ip bgp community 3:16
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

### 값 변경 후

```bash
R1(config-router)#do clear ip bgp * soft
R1(config-router)#do sh ip bgp commu 3:16       
BGP table version is 18, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path

 *>  1.1.16.0/24      1.1.13.3                 0   1000      0 3 i
 *>  1.1.17.0/24      1.1.13.3                 0   1000      0 3 i
```
------

------

## 커뮤니티를 이용한 AS 내부 정책 설정

![IMG_0536](/assets/cisco_post_img/2025-02-21-BGP Community List/IMG_0536.jpeg)

**커뮤니티를 이용하면 AS 내부에서 BGP 정책을 손쉽게 설정할 수 있다.** 

**예를 들어, 다음 그림과 같이 ISP인 AS 12 가 AS 3 및 AS 4 간의 트래픽만을 중계하기로 계약한 경우를 생각하면 As 3과 AS 4 는 AS 12를 통해서 상호간에 통신하며, AS 12를 통하여 다른 AS 와 통신을 할 수 없다.**

------

### 네트워크 추가 R4

```bash
R4(config)#int lo 32
R4(config-if)#ip add 1.1.32.1 255.255.255.0
R4(config-if)#ip add 1.1.33.1 255.255.255.0 sec
R4(config-if)#ip add 1.1.34.1 255.255.255.0 sec
R4(config-if)#ip add 1.1.35.1 255.255.255.0 sec
R4(config-if)#router bgp 4
R4(config-router)#net 1.1.32.0 mask 255.255.255.0
R4(config-router)#net 1.1.33.0 mask 255.255.255.0
R4(config-router)#net 1.1.34.0 mask 255.255.255.0
R4(config-router)#net 1.1.35.0 mask 255.255.255.0

```

------

### **R1에서 AS 내부 정책 적용 – FromAs3 및 ToAs3**

```bash
R1(config)#route-map FromAs3
R1(config-route-map)#set community 12:34
R1(config-route-map)#exit
R1(config)#ip community-list 34 permit 12:34
R1(config)#route-map ToAs3
R1(config-route-map)#match community 34
R1(config-route-map)#exit
R1(config)#router bgp 12
R1(config-router)#neighbor 1.1.13.3 route-map FromAs3 in
R1(config-router)#neighbor 1.1.13.3 route-map ToAs3 out
R1(config-router)#neighbor 1.1.2.2 send-community
R1(config-router)#do clear ip bgp * soft
R1(config-router)#

```

**기능 및 설명:**

* **FromAs3 route-map:**
  * R1는 AS 3로부터 들어오는 업데이트에 대해, set community 12:34를 적용하여 경로에 12:34 커뮤니티를 부여한다.

* **ip community-list 34 permit 12:34:**
  * 커뮤니티 리스트 34를 생성하여, 커뮤니티 값 12:34를 허용한다.

* **ToAs3 route-map:**
  * R1은 AS 3로 내보내는 업데이트에서 match community 34를 사용하여, 커뮤니티 값 12:34가 포함된 경로만 매치하도록 한다.

* **적용:**
  * R1의 BGP 피어링에서, 들어오는 업데이트에는 FromAs3 정책을, 내보내는 업데이트에는 ToAs3 정책을 적용한다.
  * 또한, neighbor send-community를 설정하여 커뮤니티 속성을 포함해 전송한다.

------

### 커뮤니티 값을 이용한 입력 및 출력 정책 R2

```bash
R2(config)#route-map FromAs4
R2(config-route-map)#set community 12:34
R2(config-route-map)#exit
R2(config)#ip community-list 34 permit 12:34
R2(config)#route-map ToAs4
R2(config-route-map)#match community 34
R2(config-route-map)#exit
R2(config)#router bgp 12
R2(config-router)#neighbor 1.1.24.4 route-map FromAs4 in
R2(config-router)#neighbor 1.1.24.4 route-map ToAs4 out
R2(config-router)#nei 1.1.1.1 send-community
R2(config-router)#do clear ip bgp * soft
R2(config-router)#

```

**기능 및 설명:**

* R2는 AS 4와의 피어링에서, 들어오는 업데이트에 대해 FromAs4 route-map을 적용하여 커뮤니티 12:34를 부여하고, 내보내는 업데이트에서는 ToAs4 route-map으로 커뮤니티 값 12:34가 포함된 경로만 매치하도록 한다.

* send-community를 통해 커뮤니티 속성이 피어에게 전송되게 한다.

------

### AS 3 에서 수신한 네트워크만 저장되어 있는 R3 라우팅 테이블

```bash
R3(config-router)#do sh ip bgp
BGP table version is 31, local router ID is 1.1.3.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.3.0/24       0.0.0.0                  0         32768 i
 *>  1.1.4.0/24       1.1.13.1                               0 12 4 i
 *>  1.1.16.0/24      0.0.0.0                  0         32768 i
 *>  1.1.17.0/24      0.0.0.0                  0         32768 i
 *>  1.1.18.0/24      0.0.0.0                  0         32768 i
 *>  1.1.19.0/24      0.0.0.0                  0         32768 i
 *>  1.1.32.0/24      1.1.13.1                               0 12 4 i
 *>  1.1.33.0/24      1.1.13.1                               0 12 4 i
 *>  1.1.34.0/24      1.1.13.1                               0 12 4 i
 *>  1.1.35.0/24      1.1.13.1                               0 12 4 i
R3(config-router)#

```

**분석:**

* R3는 AS 3에서 수신한 경로들(1.1.3.0/24, 1.1.4.0/24, 1.1.16.0/24, …, 1.1.35.0/24)을 확인할 수 있으며, 각 경로에 대해 정책에 따라 커뮤니티 값이 적용되어 있다.

* 예를 들어, 1.1.16.0/24 경로는 R3에서 기본적으로 광고되며, R3의 정책으로 인해 커뮤니티 값가 “3:16”으로 설정되어 있다.

------

### AS 4 에서 수신한 네트워크만 저장되어 있는 R4 라우팅 테이블

```bash
R4(config-router)#do sh ip bgp
BGP table version is 26, local router ID is 1.1.4.4
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.3.0/24       1.1.24.2                               0 12 3 i
 *>  1.1.4.0/24       0.0.0.0                  0         32768 i
 *>  1.1.16.0/24      1.1.24.2                               0 12 3 i
 *>  1.1.17.0/24      1.1.24.2                               0 12 3 i
 *>  1.1.18.0/24      1.1.24.2                               0 12 3 i
 *>  1.1.19.0/24      1.1.24.2                               0 12 3 i
 *>  1.1.32.0/24      0.0.0.0                  0         32768 i
 *>  1.1.33.0/24      0.0.0.0                  0         32768 i
 *>  1.1.34.0/24      0.0.0.0                  0         32768 i
 *>  1.1.35.0/24      0.0.0.0                  0         32768 i
R4(config-router)#

```

**분석:**

* R4는 AS 4의 피어로부터 수신한 경로들이 기록되어 있으며, AS 내부의 정책에 따라 각 경로에 커뮤니티 값이 정상적으로 반영되어 있는지 확인할 수 있다.

* 예를 들어, R4는 1.1.32.0/24~1.1.35.0/24 경로를 광고하고, 이러한 경로가 AS 3이나 AS 12와의 피어링에서 설정된 정책에 따라 처리되었음을 볼 수 있다.

------

**6.3 AS 내부 정책 적용 결과 (R1, R2)**

* **R1:**

  * FromAs3와 ToAs3 route-map에 의해, AS 3로부터 들어오고 나가는 경로에 대해 커뮤니티 12:34가 설정되어 있다.

  * 정책에 따라 local-preference나 다른 속성과 결합해 경로 선택에 영향을 미친다.

* **R2:**

  * FromAs4와 ToAs4 route-map에 의해, AS 4와의 피어링에서 커뮤니티 12:34가 적용된 경로가 필터링되어 처리된다.

  * send-community 설정 덕분에 커뮤니티 값이 피어에게 전달되어 정책적 제어가 가능해진다.

------

**결론**

이번 설정은 두 가지 주요 측면을 보여준다.

​	1.	**커뮤니티 값 지정 및 전송**

​				R1에서는 기존 커뮤니티 리스트 항목을 제거하고, “3:16”만을 허용하는 커뮤니티 리스트 1을 생성하였다.

​				Route-map Map2를 통해, 커뮤니티 값 “3:16”을 가진 경로에 대해 local-preference를 1000으로 변경함으로써 AS 내부에서 해당 경로를 더 선호하도록 했다.

​				값 변경 전에는 기본 local-preference가 적용되었으나, 소프트 리셋 후 정책이 반영되어 local-preference가 1000으로 변경된 것을 확인하였다.

​	2.	**AS 내부 정책 설정**

​				R4는 추가 네트워크를 생성하여 BGP에 광고하였고, R1과 R2에서는 FromAs3/ToAs3 및 FromAs4/ToAs4 route-map을 이용해, 들어오는 및 나가는 업데이트에 대해 커뮤니티 값을 설정하고, 이를 기반으로 정책을 적용하였다.

​				send-community 명령어를 통해 커뮤니티 속성이 피어에게 전송되었으며, 최종적으로 각 AS의 BGP 테이블에서 커뮤니티 값이 정상적으로 반영되어 AS 내부 경로 선택 및 필터링에 활용할 수 있게 되었다.
