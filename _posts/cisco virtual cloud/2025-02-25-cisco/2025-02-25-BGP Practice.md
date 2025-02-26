---
title: <h0>BGP Practice</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-25-cisco]
tags: [Network, BGP]













---

# BGP Practice



![image-20250225120943999](/assets/cisco_post_img/2025-02-25-BGP Practice//image-20250225120943999.png)



## 인터페이스 설정

```bash
********************************* GW1 ************************************ 
conf t
interface Loopback0
 ip address 192.168.1.1 255.255.255.255

!
interface Loopback1
 ip address 192.168.11.19 255.255.255.240

!
interface Ethernet0/0
 description Link to GW2
 ip address 172.16.12.1 255.255.255.252

!
interface Ethernet0/1
 description Link to ISP1
 ip address 209.165.201.1 255.255.255.252

!
interface Ethernet0/2
 ip address 209.165.201.17 255.255.255.252

!
router ospf 1
 passive-interface Loopback0
 network 172.16.12.1 0.0.0.0 area 1
 network 192.168.1.1 0.0.0.0 area 1



********************************* GW2 ************************************ 
conf t
interface Loopback0
 ip address 192.168.2.2 255.255.255.255

!
interface Loopback1
 ip address 192.168.22.200 255.255.255.128

!
interface Ethernet0/0
 description Link to GW1
 ip address 172.16.12.2 255.255.255.252

!
interface Serial2/0
 description Link to ISP2
 ip address 209.165.201.5 255.255.255.252

 serial restart-delay 0
!
router ospf 1
 network 172.16.12.2 0.0.0.0 area 1
 network 192.168.2.2 0.0.0.0 area 1


********************************* ISP1 ************************************ 
conf t
interface Loopback0
 ip address 209.165.201.21 255.255.255.255

!
interface Ethernet0/0
 description Link to ISP3
 ip address 209.165.201.9 255.255.255.252

!
interface Ethernet0/1
 description Link to GW1
 ip address 209.165.201.2 255.255.255.252

!
router bgp 65100
 bgp log-neighbor-changes
 neighbor 209.165.201.1 remote-as 65000
 neighbor 209.165.201.1 password BGPPASSWORD
 neighbor 209.165.201.10 remote-as 4279500803


********************************* ISP2 ************************************ 
conf t
interface Loopback0
 ip address 209.165.201.22 255.255.255.255

!
interface Ethernet0/0
 description LInk to ISP3
 ip address 209.165.201.13 255.255.255.252

!
interface Ethernet0/1
 ip address 209.165.201.18 255.255.255.252

!
interface Serial2/0
 description Link to GW2
 ip address 209.165.201.6 255.255.255.252

 serial restart-delay 0
!
router bgp 65200
 bgp log-neighbor-changes
 neighbor 209.165.201.5 remote-as 65000
neighbor 209.165.201.5 password BGPPASSWORD
 neighbor 209.165.201.14 remote-as 4279500803
 neighbor 209.165.201.17 remote-as 65000
 neighbor 209.165.201.17 password BGPPASSWORD



********************************* ISP3 ************************************ 
conf t
interface Loopback0
 ip address 209.165.201.23 255.255.255.255

!
interface Loopback1
 ip address 203.0.112.1 255.255.255.0

!
interface Loopback2
 ip address 203.0.113.1 255.255.255.0

!
interface Loopback3
 ip address 203.0.114.1 255.255.255.0

!
interface Loopback4
 ip address 203.0.115.1 255.255.255.0

!
interface Ethernet0/0
 description Link to ISP1
 ip address 209.165.201.10 255.255.255.252

!
interface Ethernet0/1
 description Link to ISP2
 ip address 209.165.201.14 255.255.255.252

!
router bgp 4279500803
bgp log-neighbor-changes
 network 203.0.112.0
 network 203.0.113.0
 network 203.0.114.0
 network 203.0.115.0
 neighbor 209.165.201.9 remote-as 65100
 neighbor 209.165.201.13 remote-as 65200
```



------

## 1. GW1과 GW2 라우터들에서 다음을 확인하고 IBGP를 구성

* **GW1의 router ID는 1.1.1.1**
* **GW2의 router ID는 2.2.2.2**
* **OSPF 라우팅은 수정하지 마십시오**

```bash

GW1
router ospf 1
router-id 1.1.1.1
int lo 0
ip ospf 1 area 1
int lo 1
ip ospf 1 area 1
int e0/0
ip ospf 1 area 1

GW2
router ospf 1
router-id 2.2.2.2
int lo 0
ip ospf 1 area 1
int lo 1
ip ospf 1 area 1
int e0/0
ip ospf 1 area 1

```



------

## 2. AS 65000과 두 개의 ISP 사이에 EBGP 를 구성하십시오

* **peer-group 을 사용하되 peer group 이름은 ISP를 사용하십시오**
* **설정 후, log 메시지를 확인하여 추가 구성이 필요하다면 설정**
* **GW1과 GW2 라우터들의 루프백 1 인터페이스 네트워크 정보만 advertise 하십시오**
* **1번과 2번 설정이 완료되면, GW1과 GW2에서 ISP3의 루프백 1 ~ 4 네트워크가 라우팅 테이블에서 반드시 확인되야 합니다.**
* **그 외 GW1, GW2 라우터에서 상대방의 루프백 1 네트워크도 보여야 합니다.**

* **! ----- 이미 존재하는 인터페이스/OSPF 설정은 수정 없이 유지 -----**

```bash
GW1

router bgp 65000
 bgp log-neighbor-changes
 neighbor 172.16.12.2 remote-as 65000
 neighbor ISP1 peer-group
 neighbor ISP1 remote-as 65100
 neighbor 209.165.201.2 peer-group ISP1
 neighbor 209.165.201.2 password BGPPASSWORD

 neighbor ISP2 peer-group
 neighbor ISP2 remote-as 65200
 neighbor 209.165.201.18 peer-group ISP2
 neighbor 209.165.201.18 password BGPPASSWORD
 network 192.168.11.16 mask 255.255.255.240

GW2 (없어도 됨)
! ----- 이미 존재하는 인터페이스/OSPF 설정은 수정 없이 유지 -----

router bgp 65000
 bgp log-neighbor-changes
 neighbor 172.16.12.1 remote-as 65000
 neighbor ISP2 peer-group
 neighbor ISP2 remote-as 65200
 neighbor 209.165.201.6 peer-group ISP2
 neighbor 209.165.201.6 password BGPPASSWORD
 network 192.168.22.128 mask 255.255.255.128
```



------

## 3. 정책 설정

* ISP1 가입자들과 ISP2 가입자들의 통신은 ISP3를 통해 통신해야 합니다.
* 즉, AS 65000을 거쳐 통신해서는 안된다는 뜻입니다.
* AS-path filter를 사용하여 구현하십시오.

```bash

ISP1
ISP1(config)# ip as-path access-list 1 deny _65000_
ISP1(config)# ip as-path access-list 1 permit .*
ISP1(config)# router bgp 65100
ISP1(config-router)# neighbor 209.165.201.1 filter-list 1 in
ISP1(config-router)# exit
ISP1(config)# do clear ip bgp * soft


ISP2
conf t
ISP2(config)# ip as-path access-list 1 deny _65000_
ISP2(config)# ip as-path access-list 1 permit .*
ISP2(config)# router bgp 65200
ISP2(config-router)# neighbor 209.165.201.5 filter-list 1 in
ISP2(config-router)# exit
ISP2(config)# do clear ip bgp * soft

```



------

## 4. 다음 조건에 맞춰 AS 65000에서 외부로 나가는 트래픽을 제어하십시오

* GW1 - ISP1의 링크를 주로 사용
* GW1 - ISP1의 링크가 down 되면 GW1- ISP2 링크를 사용
* GW1 - ISP의 링크가 down 되면, GW2 - ISP2 링크를 사용

```bash

1. GW1 - ISP1의 링크를 주로 사용
2. GW1 - ISP1의 링크가 down되면, GW1 - ISP2 링크를 사용
3. GW1 - ISP2의 링크가 down되면, Gw2 - ISP2 링크를 사용

처음 방식 (파기)

주 경로(GW1 → ISP1)를 가장 높은 Local Preference 값으로 설정
대체 경로(GW1 → ISP2)는 Local Preference를 낮게 설정
최후의 대체 경로(GW2 → ISP2)는 가장 낮은 Local Preference를 설정



처음 썼던 방식 ------------------------------

ip prefix-list GW1 permit 209.165.201.1/30
route-map four
match ip add prefix-list GW1
set local-preference 200
exit
router bgp 65100
neighbor 209.165.201.1 route-map four out
----------------------------------------------

주 경로(GW1 → ISP1)를 가장 높은 Local Preference 값으로 설정


두 번째 방식

GW1 → ISP1 (MED 값 낮게 설정)--------------------

GW1

conf t
ip prefix-list GW1 permit 0.0.0.0/0
route-map four permit 10
 match ip address prefix-list GW1
 set metric 50
exit
router bgp 65000
 neighbor 209.165.201.2 route-map four out  
do clear ip bgp * soft

GW1 → ISP2 (MED 값 높게 설정)

최종 설정---------------------------------

GW1
ip prefix-list GW1-to-ISP1 permit 0.0.0.0/0 le 32
ip prefix-list GW1-to-ISP2 permit 0.0.0.0/0 le 32

route-map EGRESS1 permit 10
 match ip address prefix-list GW1-to-ISP1
 set local-preference 300
!
route-map EGRESS2 permit 10
 match ip address prefix-list GW1-to-ISP2
 set local-preference 200
!

router bgp 65000
 neighbor 209.165.201.2 route-map EGRESS1 in
 neighbor 209.165.201.18 route-map EGRESS2 in
end

clear ip bgp * soft


```



------

## 5. 외부에서 AS 65000으로 들어오는 트래픽은 GW1-ISP2 사이의 인터페이스를 사용하도록 설정

* **ISP2 에서 차례대로 다음 명령어를 입력**
  * **clear ip bgp 209.165.201.5**
  * **clear ip bgp 209.165.201.17**
  * **clear ip bgp 209.165.201.14**
* **ISP 테이블에서 BGP 테이블을 확인합니다. ISP2에서 best path는 어떤 경로이고, 왜 그 경로가 best로 선택이 됐는지 이유를 생각해봅니다.**
* **적당한 path attribute를 사용하여 AS 65000 으로 들어오는 트래픽을 GW1- ISP2 사이의 링크를 사용하도록 설정하십시오**

```bash


GW1
ip prefix-list ISP1-to-GW1 permit 0.0.0.0/0 le 32
ip prefix-list ISP2-to-GW1 permit 0.0.0.0/0 le 32

route-map INGRESS1 permit 10
 match ip address prefix-list ISP1-to-GW1
 set metric 10

route-map INGRESS2 permit 10
 match ip address prefix-list ISP2-to-GW1
 set metric 5

router bgp 65000
 neighbor ISP1 route-map INGRESS1 out
 neighbor ISP2 route-map INGRESS2 out
end

clear ip bgp * soft

GW2
ip prefix-list ISP2-to-GW2 permit 0.0.0.0/0 le 32

route-map INGRESS3 permit 10
 match ip address prefix-list ISP2-to-GW2
 set metric 10

router bgp 65000
 neighbor ISP2 route-map INGRESS3 out
end

clear ip bgp * soft
```

### 5번 문제에서 이해가 안됐던 부분

BGP에서 **“Out 방향”**으로 set metric(MED) 값을 설정해주면, 내 라우터(GW1)가 **상대방(= ISP)**에게 보내는 **BGP 업데이트**에 “이 경로의 MED는 X입니다”라고 표시해주는 것입니다.

​	•	**결과적으로**, **상대(= ISP)** 라우터가 **그 MED 값을 수락**하고 **경로 선택 시 MED를 반영**해야만 실제로 상대방 쪽 라우팅에 변화가 생기게 됩니다.

​	•	한마디로 **“내가 MED를 보낸다”**와 **“상대가 그 MED를 존중한다(= 적용)”**는 별개의 문제인 것이죠.

**왜 ISP2는 반응했는데, ISP1은 반응하지 않았나?**

1. **ISP2는 Inbound MED를 해석하도록 설정되어 있음**

​	•	예를 들어, **bgp always-compare-med** 가 켜져 있거나, **ISP2** 내부에서 Inbound MED를 반영하도록 정책이 잡혀 있을 수 있습니다.

​	•	따라서 GW1이 MED(예: set metric 5)를 보내면, **ISP2는 그 MED를 받아서 경로 우선순위를 바꿀** 수 있습니다.

2. **ISP1은 Inbound MED를 무시하거나, 로컬 정책이 우선임**

​	•	**기본 BGP 동작** 상, 서로 다른 AS에서 들어오는 MED를 비교하지 않을 수도 있고,

​	•	ISP1 라우터가 **Local Preference** 또는 **Weight** 등을 더 높게 설정해 두었다면, MED 값이 들어와도 반영되지 않습니다.

​	•	또는 **bgp always-compare-med**가 꺼져 있어서, ISP1은 MED를 고려하지 않는 상태일 수 있습니다.

3. **Out 방향 set metric은 그냥 “내가 MED 주겠다”**라는 뜻일 뿐

​	•	**상대방(=ISP1, ISP2)이 그 MED를 실제로 받아들이고 우선순위를 바꿔줘야** 비로소 라우팅에 변화가 생깁니다.

**간단히 비유하자면**

​	•	**내가 편지를 쓴다고(= set metric out) 해서 상대방(= ISP)이 그 내용을 반드시 수용하는 것은 아님**

​	•	**ISP2는 편지를 읽고(= MED를 적용) 행동**을 바꿨지만, **ISP1은 편지를 읽고도 무시(= 다른 우선순위가 높거나 MED 적용 설정 안 됨)** 하는 상황입니다.

**정리**

​	1.	**ISP2는 MED를 받아들여 경로 우선순위를 바꿨을 가능성이 높다.**

​	2.	**ISP1은 MED를 반영하지 않거나, 다른 정책(예: Local Pref)이 더 우선이라 변화가 없다.**

​	3.	**Out 방향 MED 설정은 “내가 MED를 보낸다” 정도로 이해**하면 되고, **실제 적용은 “상대방이 그 MED를 존중해야”** 이루어진다.



따라서 **“왜 ISP1은 안 바뀌나?”** → **ISP1 측 정책 혹은 BGP 설정이 Inbound MED를 받아들이지 않기 때문**이라고 이해하시면 됩니다. 



------

## 6. ISP1에서 다음 조건에 맞게 default route를 advertise 하십시오

* **ISP1의 라우팅 테이블에 0.0.0.0 이 없음을 확인합니다.**
* **Neighbor GW1로만 default route를 advertise 하십시오.**

```bash
ISP1
router bgp 65100
neighbor 209.165.201.1 default-originate
```

------

## 7. 다음 조건에 맞춰 네트워크 정보를 summary 하십시오 ISP3에서 루프백 인터페이스 정보들을 summary 하십시오

* **ISP3 라우터에서 루프백 1 ~ 4 인터페이스 네트워크를 확인하고 그 정보들을 summary 하십시오**
* **subnet Mask 를 최대한 일치시키십시오.**
* **GW1과 GW2에서 Summary 된 경로 이외의 상세 네트워크 정보가 보여서는 안 됩니다.**

```bash
ISP3
router bgp 4279500803
 aggregate-address 203.0.112.0 255.255.252.0 summary-only
```

