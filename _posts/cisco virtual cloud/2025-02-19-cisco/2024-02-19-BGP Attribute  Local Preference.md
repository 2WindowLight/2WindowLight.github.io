---
title:Attribute  Local Preference/h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-19-cisco]
tags: [Network, BGP]










---

# 로컬 프리퍼런스

![IMG_0524](/assets/cisco_post_img/2024-02-19-BGP Attribute  Local Preference/IMG_0524.jpeg)

**로컬 프리퍼런스는 As 외부로 가는 경로를 결정할 대 사용된다. 즉, 인접 As에서 들어오는 경로를 조정할 때 사용되는 MED와 반대로, 로컬 프리퍼런스는 인접 AS로 나가는 경로를 조정한다.**

MED와는 달리 로컬 프레퍼런스는 값이 높은 것이 우선한다.

eBGP 네이버로 라우팅 정보를 전송할 때에는 로걸 프레퍼런스 값을 알리지 않는다.

즉, 로컬 프리퍼런스(Local Preference, Local_Pref)는 **BGP 내부(IBGP)에서** 경로를 선택하는 중요한 속성이다.

​	•	값이 클수록 우선순위가 높다.

​	•	같은 AS 내부에서 **어떤 경로를 우선 사용할지** 결정할 때 사용된다.

​	•	기본값은 **100**이며, 직접 설정 가능하다.



**예제**:

​	•	Local Preference **1000** → 우선순위가 매우 높음

​	•	Local Preference **50** → 우선순위가 낮음

​	•	Local Preference **200** → 100보다 높아 이 경로를 우선 사용

------

## 로컬 프레퍼런스 값 설정

```bash
route-map HigherPreference
set local-preference 1000
exit
roter bgp 34
neighbor 2.2.31.1 router-map HigherPreference in
```

------



```
route-map HigherPreference
```

✔ **Route-map 정의**

​	•	**이름이 HigherPreference인 라우트 맵**을 생성한다.

​	•	route-map은 특정 조건을 만족하는 트래픽에 대해 **경로 속성을 변경하는 필터** 역할을 한다.

```
set local-preference 1000
```

✔ **Local Preference 값 설정**

​	•	Local Preference 값을 **1000**으로 설정하여, **이 경로의 우선순위를 높인다.**

```
exit
```

✔ **라우트 맵 설정 종료**

​	•	exit를 입력하여 route-map HigherPreference 설정을 마친다.

```
router bgp 34
```

✔ **BGP 프로세스 시작**

​	•	**AS 번호 34**에서 BGP 라우팅을 설정한다.

​	•	이 AS 내부의 라우터들이 IBGP로 연결됨.

```
neighbor 2.2.31.1 route-map HigherPreference in
```

✔ **BGP 네이버에 라우트 맵 적용**

​	•	2.2.31.1 (BGP 피어)에서 **들어오는(in) BGP 업데이트**에 HigherPreference 라우트 맵을 적용

​	•	즉, 2.2.31.1이 보내는 BGP 경로들의 **Local Preference 값을 1000으로 변경**

​	•	같은 AS 내부의 다른 BGP 경로보다 **우선순위를 높게 설정**하여, 이 경로를 선호

**결론**



이 설정을 통해, **2.2.31.1에서 학습한 BGP 경로는 Local Preference가 1000으로 설정되어 다른 경로보다 우선적으로 선택됨**.

------

