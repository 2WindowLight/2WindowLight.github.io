---
title: <h0>BGP Community analize -2</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-21-cisco]
tags: [Network, BGP]













---



## **send-community 명령어의 역할:**

기본적으로 BGP는 커뮤니티 속성을 피어에게 전송하지 않는다.

“neighbor X.X.X.X send-community” 명령어를 사용하면, 해당 이웃으로 커뮤니티 속성이 포함된 BGP 업데이트를 전송하도록 설정할 수 있다.

**2. 구성 전과 후의 상태 비교**



------

**2.1 커뮤니티 값 전송 전**

​	•	**명령어 실행:**

```bash
R2(config)# do sh ip bgp community
```

​	이 명령어를 실행하면, R2의 BGP 테이블에서 커뮤니티 값이 포함되지 않은 상태를 확인할 수 있다.

​	즉, R1에서 전송된 업데이트에 커뮤니티 속성이 없다.



​	**원인:**

​	•	R1에서는 아직 “neighbor send-community”가 설정되지 않았기 때문에, 커뮤니티 정보가 피어(R2)에게 전송되지 않는다.



------

**2.2 “neighbor send-community” 명령어 적용**

​	**R1에서 명령어 적용:**

```bash
R1(config)# router bgp 12
R1(config-router)# neighbor 1.1.2.2 send-community
```

​	이 명령어를 통해 R1은 1.1.2.2 (즉, R2)에게 BGP 업데이트를 전송할 때 커뮤니티 값을 포함하도록 설정된다.



​	**동작 원리:**

​	•	BGP 업데이트 메시지에 커뮤니티 값이 첨부되어 전송되며, 이 설정은 R1에서 생성된 경로의 커뮤니티 속성이 피어에게 전파되도록 한다.



------

**2.3 커뮤니티 값 전송 후**

​	**명령어 실행:**

```bash
R2(config)# do sh ip bgp community
```

​	•	R2의 출력 예시에서는 이제 다음과 같이 커뮤니티 값이 포함된 경로들이 나타난다:

```bash
     Network          Next Hop            Metric LocPrf Weight Path
 *>i 1.1.16.0/24      1.1.13.3                 0    100      0 3 i
 *>i 1.1.17.0/24      1.1.13.3                 0    100      0 3 i
 *>i 1.1.19.0/24      1.1.13.3                 0    100      0 3 i
```



​	•	여기서, “*>i”는 해당 경로가 최적(best) 경로로 선택되었고, 내부 경로임을 나타낸다.

​	•	출력된 경로는 R1에서 send-community 설정으로 인해 커뮤니티 값이 포함된 상태로 R2에 전달되었음을 보여준다.



​	

------

**BGP Community와 Well-Known Community란?**



**BGP Community**는 특정 경로에 태그를 추가하는 기능으로, 이를 통해 BGP 정책을 더욱 세밀하게 조정할 수 있다.



**Well-Known Community**는 **RFC에서 정의된 표준적인 커뮤니티 값**으로, 모든 BGP 라우터가 인식하고 이해할 수 있다.

즉, 네트워크 운영자들이 공통적으로 사용할 수 있도록 정해진 커뮤니티 값이다.

**Well-Known Community의 종류**



✅ **RFC 1997**에 정의된 대표적인 **Well-Known Community** 값들은 다음과 같다.

| **Community 값** | **설명**                                                     |
| ---------------- | ------------------------------------------------------------ |
| no-export        | 이 경로는 **로컬 AS를 벗어나지 않음** (EBGP로 광고되지 않음). |
| no-advertise     | **어떤 네이버에게도 광고되지 않음** (IBGP, EBGP 포함).       |
| local-as         | **이중 AS 경로 피어링** 시, 해당 AS 내부에서만 사용되고 외부로 전파되지 않음. |
| internet         | 기본값. 해당 네트워크는 BGP를 사용하는 모든 라우터로 전파될 수 있음. |

**Well-Known Community 예제**

```
route-map BLOCK_EXPORT permit 10
 set community no-export
exit

router bgp 100
 neighbor 2.2.2.2 route-map BLOCK_EXPORT out
```

✔ **설명:**

​	•	route-map BLOCK_EXPORT permit 10 → no-export 태그를 설정

​	•	neighbor 2.2.2.2 route-map BLOCK_EXPORT out → 특정 네이버로 보낼 때 no-export 적용

​	•	결과적으로, **이 경로는 로컬 AS 내부에서만 사용되고 외부로 나가지 않음.**

**정리**

​	•	**BGP Community** = BGP 경로에 태그를 추가하여 정책을 조정하는 기능.

​	•	**Well-Known Community** = RFC에서 표준으로 정의된 공통 커뮤니티 값.

​	•	대표적인 Well-Known Community:

​	•	**no-export** → EBGP로 광고 금지

​	•	**no-advertise** → 모든 네이버에게 광고 금지

​	•	**local-as** → 특정 AS 내부에서만 사용

​	•	**internet** → 모든 BGP 네트워크로 전파 가능



👉 **Well-Known Community를 사용하면 특정 BGP 경로의 광고 범위를 쉽게 제한할 수 있음.**