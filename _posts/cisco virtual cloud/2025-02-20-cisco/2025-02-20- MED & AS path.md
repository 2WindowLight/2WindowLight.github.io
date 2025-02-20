---
title: <h0> MED & AS path</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-20-cisco]
tags: [Network, BGP]










---

# MED를 이용한 입력 경로 조정



![IMG_0528](/assets/cisco_post_img/2025-02-20-path/IMG_0528.jpeg)

**2.1 MED란?**

​	•	**Multi-Exit Discriminator**

​	•	**다중 경로**가 존재할 때, **원격 AS**가 어느 경로를 선호할지 결정하도록 **“상대방”에게 힌트를 주는 BGP 속성**입니다.

​	•	**값이 낮을수록** 선호되는 경로가 됩니다.

​	•	eBGP 구간에서, Local Preference나 Weight가 동일하다면 **다음 우선순위**로 비교되는 속성입니다.

------

### **R2에서 MED = 100 설정**

```bash
R2(config)#ip as-path access-list 1 permit ^$
R2(config)#route-map INGRESS 
R2(config-route-map)#match as-path 1  
R2(config-route-map)#set metric 100
R2(config-route-map)#exit
R2(config)#router bgp 2
R2(config-router)#nei 1.1.12.1 route-map INGRESS out
R2(config-router)#end
R2#cla
*Feb 20 02:06:24.851: %SYS-5-CONFIG_I: Configured from console by console
R2#clear ip bgp * soft 

```

​	**as-path access-list 1 permit ^$는 “AS-PATH가 없는(= 이 라우터에서 시작된) 네트워크”를 매치하는 예시입니다.**

​	**set metric 100에서 metric은 BGP에서 MED를 의미합니다.**

------

### **R4에서 MED = 200 설정**

```bash
R4(config)#ip prefix-list AS2-NETWORK permit 1.1.2.0/24
R4(config)#ip prefix-list AS2-NETWORK permit 1.1.3.0/24
R4(config)#ip prefix-list AS2-NETWORK permit 1.1.4.0/24
R4(config)#route-map INGRESS
R4(config-route-map)#match ip add pre
R4(config-route-map)#match ip add prefix-list AS2-NETWORK
R4(config-route-map)#set metric 200
R4(config-route-map)#exit
R4(config)#router bgp 2
R4(config-router)#nei 1.1.14.1 route-map INGRESS out
R4(config-router)#end
R4#
*Feb 20 02:08:04.058: %SYS-5-CONFIG_I: Configured from console by console
R4#clear ip bgp * soft

```

**R4는 AS 2의 내부 네트워크(1.1.2.0/24, 1.1.3.0/24, 1.1.4.0/24)를 광고할 때 MED를 200으로 설정.**

------

 **R1에서의 BGP 테이블**

```bash
R1(config)#do sh ip bgp
BGP table version is 12, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.1.0/24       0.0.0.0                  0         32768 i
 *   1.1.2.0/24       1.1.14.4               200             0 2 i
 *>                   1.1.12.2               100             0 2 i
 *>  1.1.3.0/24       1.1.12.2               100             0 2 i
 *                    1.1.14.4               200             0 2 i
 *>  1.1.4.0/24       1.1.12.2               100             0 2 i
 *                    1.1.14.4               200             0 2 i
R1(config)#

```

​	•	R1은 **동일한 Local Preference, 동일한 Weight** 조건에서 **MED가 낮은 쪽(=100)** 을 선호합니다.

​	•	따라서 R2를 통해 들어오는 경로(MED=100)가 **Best Path**로 선택됩니다.

​	•	**결과**: R1에서 AS 2(1.1.2.0/24, 1.1.3.0/24, 1.1.4.0/24)로 갈 때 **R2 경유**를 택합니다.



**MED**는 **원격 AS**가 나에게 들어오는 트래픽(즉, **내 AS로의 인바운드 트래픽**)을 어떤 라우터로 유도할지 결정하는 데 쓰입니다. 이 예시에서는 **R2가 MED=100**(낮게) 광고, **R4가 MED=200**(높게) 광고하여 R2 쪽이 더 우선시된 것입니다.

------



## **AS-Path Prepend를 이용한 입력 경로 조정**

![IMG_0529](/assets/cisco_post_img/2025-02-20-path/IMG_0529.jpeg)

**3.1 AS-Path Prepend란?**

​	BGP 경로 정보에는 **AS-Path**가 포함됩니다.

​	**AS-Path 길이가 짧을수록** 선호도가 높아집니다.

​	**AS-Path Prepend**는 인위적으로 **내 AS 번호를 여러 번 추가**(prepend)하여, **상대방이 보기엔 AS 경로가 더 길어지도록** 만들어 **우선순위를 낮추는 기법**입니다.

​	주로 **인바운드 트래픽**을 특정 경로로 몰고 싶을 때, “덜 선호”하려는 경로 쪽에서 AS를 추가 prepend 합니다.

------

### **설정 예시 (R4에서 AS-Path Prepend)**

```bash
R4(config)#route-map MORE-AS
R4(config-route-map)#match ip add prefix-list AS-NETWORK
R4(config-route-map)#set as-path prepend 2 2
R4(config-route-map)#exit
R4(config)#router bgp 2
R4(config-router)#nei 1.1.14.1 route-map MORE-AS out
R4(config-router)#end
R4#
*Feb 20 02:27:58.169: %SYS-5-CONFIG_I: Configured from console by console
R4#clear ip bgp * soft
R4#

```

**set as-path prepend 2 2를 통해 광고할 경로에 AS 2를 두 번 더 붙여서 R1이 보게 됩니다(“2 2 2” 형태).**

------

### **R1에서의 BGP 테이블**

```bash
R1(config)#DO SH IP BGP
BGP table version is 15, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.1.0/24       0.0.0.0                  0         32768 i
 *   1.1.2.0/24       1.1.14.4                               0 2 2 2 i
 *>                   1.1.12.2                 0             0 2 i
 *>  1.1.3.0/24       1.1.12.2                               0 2 i
 *                    1.1.14.4                               0 2 2 2 i
 *>  1.1.4.0/24       1.1.12.2                               0 2 i
 *                    1.1.14.4                 0             0 2 2 2 i
R1(config)#

```

R4가 **AS 2를 세 번(2 2 2)** 포함한 경로를 R1에 광고

R1은 **AS-Path가 더 짧은**(단순히 “2”) R2 경유 경로를 우선으로 선택

**결과**: R1 → AS 2 트래픽은 R2 쪽으로 들어감.

만약 R4가 prepend를 더 많이 하거나, prepend를 해제하면 R1에서의 경로 선택이 달라질 수 있습니다.

------

## **두 기법의 비교 및 특징**

| **기법**            | **설정 위치**            | **우선순위 비교 시점**                                       | **작동 원리**                                                | **활용 사례**                                                |
| ------------------- | ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **MED**             | **내부 라우터**(광고 측) | Local Pref, Weight, Origin 등 이후                           | 상대에게 “이 경로로 들어올 때 우선순위 낮춰/높여라” 라고 힌트 | 다중 eBGP 연결 시 “이 경로(MED=100)가 더 좋음” 유도          |
| **AS-Path Prepend** | **내부 라우터**(광고 측) | Local Pref, Weight, Origin 동일 시, AS-Path 길이 비교에서 적용 | 내 AS 번호를 인위적으로 여러 번 추가 → 상대가 “AS-Path가 길다” 판단 → 덜 선호 | 인바운드 트래픽 분산: 한쪽 링크를 덜 선호하게 만들고 싶을 때 (AS-Path 길이 증가) |

​	1.	**MED**는 “**값이 낮을수록** 선호”

​	2.	**AS-Path Prepend**는 “**길이가 길수록** 비선호”

​	3.	둘 다 **인바운드 트래픽** 조정 기법이며, **원격 AS**(여기서는 R1)가 경로 선택을 할 때 **우선순위를 낮추거나 높이기** 위해 사용

​	4.	**Local Preference**나 **Weight**는 **내부(내 라우터)** 우선순위 결정에 쓰이는 것이며, **외부 AS**에게는 영향을 주지 않습니다.

​			-  따라서 **내 AS로 들어오는** 경로를 조정하려면 **MED**나 **AS-Path Prepend** 같은 속성을 건드려야 합니다(혹은 Community를 활용하여 상대방이 Local Pref를 변경하도록 요청).

**정리**

1. **MED (Multi-Exit Discriminator)**

​	•	\\\**값이 낮을수록** 우선순위가 높음

​	•	eBGP에서 **Local Preference, Weight**가 동일할 때 비교됨

​	•	“내가 광고하는 경로의 **MED**를 더 낮게(또는 높게) 설정” → **상대방**이 그 경로를 더 선호(또는 덜 선호)

2. **AS-Path Prepend**

​	•	**AS 경로**를 인위적으로 늘려, **상대방**이 “경로가 길다”고 판단하게 하여 **우선순위를 낮춤**

​	•	주로 “특정 링크로는 트래픽을 받기 싫을 때” prepend 횟수를 늘려 덜 선호하게 만듦

3. **입력 경로 조정**이란?

​	•	**원격 AS**가 나(우리 AS)로 들어오는 트래픽 경로를 **어떤 이웃 라우터**로 유도할지 결정하도록, **BGP 속성**을 수정하는 작업

​	•	이때 **MED**나 **AS-Path Prepend**가 대표적인 기법

4. **실무에서**

​	•	복수 ISP 연결, 멀티홈(Multihoming) 환경에서 **Inbound/Outbound 트래픽 엔지니어링**에 많이 활용

​	•	필요에 따라 **Community**를 사용해 원격 ISP가 Local Pref를 조정하게 만들 수도 있음.



