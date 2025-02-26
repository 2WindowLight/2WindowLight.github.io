---
title: <h0>BGP Practice Answer Review</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-26-cisco]
tags: [Network, BGP]














---

# BGP Practice Answer Review



![image-20250225120943999](/assets/cisco_post_img/2025-02-25-BGP Practice//image-20250225120943999.png)

## 1. GW1과 GW2 라우터들에서 다음을 확인하고 IBGP를 구성

* **GW1의 router ID는 1.1.1.1**
* **GW2의 router ID는 2.2.2.2**
* **OSPF 라우팅은 수정하지 마십시오**

```bash
1.
*** gw1 ***
conf t
router bgp 65000
bgp router-id 1.1.1.1
nei 192.168.2.2 remote 65000
nei 192.168.2.2 up lo 0
nei 192.168.2.2 next-hop-s

sh ip bgp

*** gw2 ***
conf t
router bgp 65000
bgp router-id 2.2.2.2
nei 192.168.1.1 remote 65000
nei 192.168.1.1 up lo 0
nei 192.168.1.1 next-hop-s
```

* **IBGP를 직접 설정하여 GW1과 GW2 간 BGP 피어링을 구성**

* **Loopback 인터페이스를 사용하여 안정적인 피어링 유지** (update-source loopback 0)

* **경로 문제가 발생하지 않도록 next-hop-self를 설정**

* **GW1과 GW2가 서로 iBGP 피어 관계를 맺고 BGP 경로를 학습할 수 있음.**

* **OSPF는 별도로 건드리지 않고, BGP만으로 IBGP 피어링을 직접 설정한 방식이므로 더 효과적임.**

------

## 이 코드가 IBGP 라고 판단되는 이유

- **nei 192.168.2.2 remote 65000 (GW1)**

- **nei 192.168.1.1 remote 65000 (GW2)**

  * **두 개의 라우터(GW1과 GW2)가 같은 AS 65000에서 BGP 피어링을 설정 중**

  - **같은 AS(65000) 내에서 피어링을 하면 IBGP**

- **IBGP에서는 기본적으로 Next-Hop이 변경되지 않음 → 이 문제를 해결하기 위해 next-hop-self(next-hop-s) 사용**

------

## **next-hop-self를 사용한 이유**

* **eBGP (외부 BGP)**: 기본적으로 **Next-Hop 주소는 변경됨**

* **iBGP (내부 BGP)**: 기본적으로 **Next-Hop 주소는 변경되지 않음**
  * 즉, iBGP에서는 경로를 학습해도 Next-Hop이 바뀌지 않으므로, **경로 학습이 실패할 가능성이 있음.**

------

### **GW1과 GW2의 iBGP 구성 문제**

​	•	GW1이 ISP1과 eBGP 피어를 맺고 경로를 학습하면, 기본적으로 **Next-Hop이 ISP1의 주소로 유지됨.**

​	•	GW2는 iBGP를 통해 GW1에서 받은 경로를 학습하지만, **Next-Hop이 ISP1의 주소로 남아 있어 ISP1으로 직접 접근할 수 없으면 BGP 경로를 사용할 수 없음.**

​	•	따라서 **GW1에서 GW2로 BGP 경로를 전달할 때 Next-Hop을 자기 자신으로 변경해야 함.**

**(즉, neighbor 192.168.2.2 next-hop-self를 사용)**

------

## 중요 <나의 생각>

**"BGP 는 Next-Hop이 변경 되지 않아야하는데 Next-hop-self를 설정하지 않으면 외부 next-hop이 그대로 유지되면서 올바르게 라우팅 되지 못한다?"**

​	

​	✔ **IBGP는 기본적으로 Next-Hop을 변경하지 않는다.**

​	✔ **만약 IBGP에서 next-hop-self를 설정하지 않으면, 외부 BGP에서 받은 Next-Hop이 그대로 유지된다.**

​	✔ **이 경우, 내부 라우터(GW2)가 Next-Hop으로 설정된 외부 주소(예: ISP1)를 직접 접근할 수 없기 때문에 올바른 라우팅이 되지 않는다.**

​	✔ **그래서 GW1이 IBGP 피어(GW2)에게 경로를 전달할 때, next-hop-self를 설정하여 GW1을 Next-Hop으로 변경해야 한다.**

​	✔ **이렇게 하면 GW2는 ISP1을 향해 직접 가려고 하지 않고, GW1을 경유하여 정상적으로 외부로 나가는 경로를 사용할 수 있다.**

➡ **즉, IBGP에서는 Next-Hop을 기본적으로 변경하지 않기 때문에, 내부 라우팅이 깨지지 않도록 next-hop-self를 설정하여 내부 BGP 피어들이 올바르게 경로를 찾도록 해야 한다**

------

------

## 2. AS 65000과 두 개의 ISP 사이에 EBGP 를 구성하십시오

* **peer-group 을 사용하되 peer group 이름은 ISP를 사용하십시오**
* **설정 후, log 메시지를 확인하여 추가 구성이 필요하다면 설정**
* **GW1과 GW2 라우터들의 루프백 1 인터페이스 네트워크 정보만 advertise 하십시오**
* **1번과 2번 설정이 완료되면, GW1과 GW2에서 ISP3의 루프백 1 ~ 4 네트워크가 라우팅 테이블에서 반드시 확인되야 합니다.**
* **그 외 GW1, GW2 라우터에서 상대방의 루프백 1 네트워크도 보여야 합니다.**

* **! ----- 이미 존재하는 인터페이스/OSPF 설정은 수정 없이 유지 -----**

```bash
2 .
*** gw1 *** sh ip ro
conf t
router bgp 65000
nei ISP peer-group
nei ISP password BGPPASSWORD
nei 209.165.201.2 remote 65100
nei 209.165.201.2 peer-group ISP
nei 209.165.201.18 remote 65200
nei 209.165.201.18 peer-group ISP
network 192.168.11.16 mask 255.255.255.240

*** gw2 ***
conf t
router bgp 65000
nei 209.165.201.6 remot 65200
net 192.168.22.128 mask 255.255.255.128
```

------

### ** 피어 그룹을 두 개 만들지 않고 하나로만 형성할 수 있었던 이유**

BGP에서 **peer-group**은 여러 개의 neighbor에게 동일한 설정을 적용할 때 사용하는 기능임.

ISP1(AS 65100)과 ISP2(AS 65200)은 **다른 AS이지만, 동일한 설정**을 적용할 수 있는 부분이 있음.

​	•	예를 들어, password, update-source, route-map, soft-reconfiguration, default-originate 같은 설정들은 AS 번호와 관계없이 같은 값으로 설정 가능함.

​	•	즉, **AS 번호(remote-as)가 다르더라도 다른 값이 필요한 부분을 별도로 지정하면 하나의 피어 그룹을 사용할 수 있음.**

------

### **neighbor ISP1 remote-as 65100 이거에서 IP 주소와 AS 65100의 관계**

```bash
neighbor 209.165.201.2 remote-as 65100
neighbor 209.165.201.2 peer-group ISP
```

**여기서 neighbor 209.165.201.2 remote-as 65100 설정의 의미**

* BGP는 **각각의 네이버와 설정이 필요**함.

* neighbor 209.165.201.2 remote-as 65100 → “IP 주소 209.165.201.2는 AS 65100에 속한다”는 의미.

* 이후 neighbor 209.165.201.2 peer-group ISP → **해당 네이버를 ISP라는 피어 그룹에 추가함.**

1. **BGP는 네이버를 자동으로 찾지 않음 → 명시적으로 설정해야 함.**

2.  **neighbor 209.165.201.2 remote-as 65100을 설정하는 이유는, 해당 IP가 AS 65100에 속해 있음을 명확하게 지정하는 것.**

3. **이 설정을 하지 않으면 BGP 세션이 형성되지 않으며, BGP 라우팅 테이블에도 경로가 추가되지 않음.**

------

## if OSPF 없이 IBGP 세션을 구성할 경우

```bash
router bgp 65000
 neighbor 192.168.2.2 remote-as 65000
 neighbor 192.168.2.2 update-source lo0
 neighbor 192.168.2.2 next-hop-self
```

------



## 3. 정책 설정

* ISP1 가입자들과 ISP2 가입자들의 통신은 ISP3를 통해 통신해야 합니다.
* 즉, AS 65000을 거쳐 통신해서는 안된다는 뜻입니다.
* AS-path filter를 사용하여 구현하십시오.

```bash
3. 
*** gw1 ***
conf t
ip as-path access-list 1 permit ^$
router bgp 65000
nei ISP filter-list 1 out 
bgp asnotation dot

*** gw2 ***
conf t
ip as-path access-list 1 permit ^$
router bgp 65000
nei 209.165.201.6 filter-list 1 out
bgp asnotation dot

```

| **항목**           | **사용자 코드 (틀린 코드)**             | **정답 코드**                              |
| ------------------ | --------------------------------------- | ------------------------------------------ |
| **AS-Path 필터링** | deny _65000_ (AS 65000을 차단)          | permit ^$ (AS-Path가 비어있는 경우만 허용) |
| **적용 방향**      | neighbor X filter-list 1 in (수신 필터) | neighbor X filter-list 1 out (송신 필터)   |
| **적용 대상**      | ISP1, ISP2 개별 설정                    | ISP (공통 peer-group 적용)                 |
| **BGP 설정**       | 개별 neighbor로 필터 적용               | peer-group을 사용해 필터 적용              |

------

**주요 문제점 및 정정**

1. **AS-Path 필터 설정 오류**

​	•	사용자의 deny _65000_ 설정은 AS 65000이 포함된 경로를 수신하지 않도록 차단하는 방식이지만,

정답의 permit ^$ 설정은 AS-Path가 **비어있는 경우만 허용**하는 방식이다.

​	•	즉, 정답 코드에서는 AS 65000을 포함하지 않은 경로만 허용하고, 다른 모든 경로는 차단된다.

2. **필터 적용 방향 (in vs out)**

​	•	사용자 코드에서는 filter-list 1 in을 사용하여 **수신된 경로를 필터링**하고 있음.

​	•	하지만 정답 코드에서는 filter-list 1 out을 사용하여 **광고되는 경로를 필터링**한다.

​	•	따라서 ISP1, ISP2에서 받은 경로를 필터링하는 것이 아니라, **GW1과 GW2에서 보내는 경로를 조절**해야 한다.

3. **Peer-group 활용 차이**

​	•	사용자 코드에서는 neighbor 209.165.201.X 형태로 개별적으로 필터를 설정하고 있음.

​	•	반면 정답 코드에서는 peer-group ISP를 활용하여 공통 정책을 설정함.

​	•	이렇게 하면 중복 설정이 줄어들고 관리가 용이해진다.

**결론**

​	•	**사용자 코드 (deny _65000_)는 AS 65000을 포함한 경로의 수신을 차단하지만, 송신 경로는 제어하지 않는다.**

​	•	**정답 코드 (permit ^$)는 AS-Path가 없는 경로만 허용하여, AS 65000을 통해 트래픽이 전달되지 않도록 한다.**

​	•	**사용자의 filter-list 1 in 설정은 수신 필터이고, 정답의 filter-list 1 out 설정은 송신 필터이므로 적용 방식이 다르다.**

​	•	**Peer-group을 활용한 정답 코드가 관리 측면에서 더 효율적이다.**

------

### **AS-Path 필터가 어떻게 AS 65000을 포함하지 않은 경로만 허용하는지 설명**

```bash
GW1:
conf t
ip as-path access-list 1 permit ^$
router bgp 65000
nei ISP filter-list 1 out
bgp asnotation dot
```



> [!IMPORTANT]
>
> **1️⃣ ip as-path access-list 1 permit ^$**
>
> ​	•	^$ → **AS-Path가 비어있는 경로만 허용.**
>
> ​	•	즉, **AS-PATH가 없는 (자체 생성된, 즉 Local-origin) 경로만 광고할 수 있음**.
>
> ​	•	^ → AS-PATH의 시작을 의미.
>
> ​	•	$ → AS-PATH의 끝을 의미.
>
> ​	•	따라서 **AS-PATH가 비어있는 경로만 통과 가능**.
>
> **^$의 의미 (AS-PATH가 없는 경로만 허용)**
>
> ip as-path access-list 1 permit ^$는 **AS-PATH가 비어있는 경로만 허용한다는 의미**임.
>
> **📌 “AS-PATH가 비어 있다”는 의미?**
>
> ​	•	BGP에서 **자체적으로 생성한 네트워크 (Local-origin)** 의 경우 AS-PATH가 없음.
>
> ​	•	즉, **자기 자신이 생성한 네트워크**만 광고할 수 있음.
>
> ​	•	외부(EBGP)에서 배운 경로는 **AS-PATH에 다른 AS 번호가 포함**되기 때문에 ^$ 필터에 의해 차단됨.
>
> **

2️⃣ neighbor ISP filter-list 1 out (GW1)**

​	•	filter-list 1 out → **AS-PATH 필터링을 OUTBOUND 트래픽에 적용.**

​	•	즉, **GW1에서 외부로 나가는 BGP 업데이트에서 AS-PATH가 존재하는 경로를 제거.**

​	•	결과적으로 **AS 65000을 포함하는 모든 경로는 광고되지 않음.**

**4️⃣ bgp asnotation dot**

​	•	BGP AS 번호 표기 방식을 점(dot) 형식으로 설정.

------

------

## 4. 다음 조건에 맞춰 AS 65000에서 외부로 나가는 트래픽을 제어하십시오

* GW1 - ISP1의 링크를 주로 사용
* GW1 - ISP1의 링크가 down 되면 GW1- ISP2 링크를 사용
* GW1 - ISP의 링크가 down 되면, GW2 - ISP2 링크를 사용

```bash
4. 
*** gw1 ***
conf t
ip as-path access-list 2 permit ^65100_
!
route-map SET_LOC 
match as-path 2
set local-pre 200
route-map SET_LOC permit 20
set local-pre 150

router bgp 65000 
nei ISP route-map SET_LOC in

```

1. **AS-Path 필터링을 활용하여 올바른 경로를 매칭 (🚀 올바른 방식)**

   - ip as-path access-list 2 permit ^65100_

   - **AS 65100을 경유한 경로만 필터링하도록 설정**

   - ^65100_의 의미:

   - ^ → AS-PATH의 **시작**

   - 65100_ → **첫 번째 AS가 65100인 경로만 허용**

   - 따라서 **ISP1을 통해 들어온 경로만 필터링**됨

2. **Local Preference를 사용하여 우선순위 조정 (🚀 AS 65000 내부 트래픽 제어 가능)**

   - set local-preference 200 → ISP1을 기본 경로로 설정

   - set local-preference 150 → ISP1이 다운되면 ISP2를 선택

   - 이 설정을 통해 **AS 65000 내부에서 올바른 우선순위로 트래픽이 흐름**

3. **피어 그룹을 활용하여 유지보수 간소화 (🚀 효율적인 정책 적용)**

   - neighbor ISP route-map SET_LOC in

   - **피어 그룹을 활용하여 동일한 정책을 모든 ISP 네이버에게 적용**

   - 개별 네이버 설정을 줄여 유지보수가 쉬워짐

**내가 섰던 코드의 문제점 (틀린 이유)**

```bash
route-map EGRESS1 permit 10
match ip address prefix-list GW1-to-ISP1
set local-preference 300

router bgp 65000
 neighbor 209.165.201.2 route-map EGRESS1 in
 neighbor 209.165.201.18 route-map EGRESS2 in
end
```

1. **Prefix-List를 사용하여 경로를 매칭하고 있음 (🚨 잘못된 방식)**

​	•	ip prefix-list를 사용하면 **목적지 네트워크 기반으로 트래픽을 필터링**하게 됨

​	•	**하지만 문제 요구사항은 특정 AS를 통해 나가는 경로를 조정하는 것**이므로, **AS-Path를 기반으로 필터링해야 함**

​	•	prefix-list는 IP 주소 기반으로 동작하므로 AS-PATH 정보를 조작할 수 없음

2. **Local Preference 값이 잘못 설정됨 (🚨 AS 65000 내부에서만 적용됨)**

​	•	set local-preference는 **AS 65000 내부에서 경로 우선순위를 결정하는 값**

​	•	**ISP 측에서는 적용되지 않음**, 즉 외부 트래픽을 제어할 수 없음

​	•	**정답 코드에서는 AS-Path를 기반으로 한 필터링을 사용하여 트래픽을 조정**함



------

------

## 5. 외부에서 AS 65000으로 들어오는 트래픽은 GW1-ISP2 사이의 인터페이스를 사용하도록 설정

* **ISP2 에서 차례대로 다음 명령어를 입력**
  * **clear ip bgp 209.165.201.5**
  * **clear ip bgp 209.165.201.17**
  * **clear ip bgp 209.165.201.14**
* **ISP 테이블에서 BGP 테이블을 확인합니다. ISP2에서 best path는 어떤 경로이고, 왜 그 경로가 best로 선택이 됐는지 이유를 생각해봅니다.**
* **적당한 path attribute를 사용하여 AS 65000 으로 들어오는 트래픽을 GW1- ISP2 사이의 링크를 사용하도록 설정하십시오**

```bash
5. 
*** gw2 ***
conf t
route-map SET_MED 
set metric 50 
!
router bgp 6500
nei 209.165.201.6 route-map SET_MED out
```

**왜 MED 값을 조정했을까?**

✅ **MED(Multi-Exit Discriminator)**는 동일한 AS 내부에 여러 개의 경로가 있을 때, **외부 AS가 특정 경로를 우선적으로 선택하도록 하는 기준**이 됨.

✅ **MED 값이 낮을수록 선호됨**, 따라서 GW1-ISP2 경로의 MED 값을 더 낮게 설정하면, **외부 AS(ISP1, ISP2)가 이 경로를 Best Path로 선택하도록 유도**할 수 있음.

**📌 왜 Outbound 정책을 사용했을까?**

**✅ Inbound vs Outbound 정책 비교**

​	•	**Inbound 정책:** AS 65000 내부에서 들어오는 경로를 조정하는 방식 → **Local Preference 활용**

​	•	**Outbound 정책:** AS 65000에서 외부로 나가는 경로를 조정하는 방식 → **MED 활용**



**📌 1️⃣ GW1이 아니라 GW2에서 MED를 설정한 이유**

💡 **GW1-ISP2 경로를 우선 사용하도록 하기 위해, AS 65000의 외부(ISP2)에서 Best Path를 GW1 쪽으로 향하게 만들어야 함.**

💡 **하지만 MED는 AS 간 경로를 비교할 때 사용하는 값이므로, AS 65000 내부(GW1)에서 직접 설정한다고 해도 ISP2가 그것을 참고하지 않음.**

💡 **따라서 GW2에서 MED 값을 높게 설정하여, ISP2가 GW1을 우선적으로 선택하도록 유도한 것.**



**✅ 조정 전 (GW1과 GW2 MED 동일)**

​	•	ISP2가 **GW1과 GW2 중 어떤 경로를 선택할지 판단하는 기준이 없음**.

​	•	따라서 기본적으로 다른 Path Attribute를 보고 선택하게 됨.

**✅ 조정 후 (GW2의 MED 값 증가)**

​	•	**ISP2가 GW2로 향하는 경로의 MED 값을 높게 인식**하게 됨.

​	•	따라서 **MED 값이 더 낮은 GW1 경로를 Best Path로 선택**.

​	•	결과적으로, **외부에서 AS 65000으로 들어오는 트래픽이 GW1-ISP2 링크를 통해 들어오도록 유도**됨.

------

------

## 6. ISP1에서 다음 조건에 맞게 default route를 advertise 하십시오

* **ISP1의 라우팅 테이블에 0.0.0.0 이 없음을 확인합니다.**
* **Neighbor GW1로만 default route를 advertise 하십시오.**

```bash
6. 
*** ISP1 ***
conf t
router bgp 65100
nei 209.165.201.1 default-orig
```

## 7. 다음 조건에 맞춰 네트워크 정보를 summary 하십시오 ISP3에서 루프백 인터페이스 정보들을 summary 하십시오

* **ISP3 라우터에서 루프백 1 ~ 4 인터페이스 네트워크를 확인하고 그 정보들을 summary 하십시오**
* **subnet Mask 를 최대한 일치시키십시오.**
* **GW1과 GW2에서 Summary 된 경로 이외의 상세 네트워크 정보가 보여서는 안 됩니다.**

```bash
7. 
*** ISP3 ***
conf t
router bgp 65300.3
aggregate-address 203.0.112.0 255.255.252.0 summary-only
```

