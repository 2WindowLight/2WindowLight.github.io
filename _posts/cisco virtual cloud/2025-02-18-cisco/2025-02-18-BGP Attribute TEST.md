---
title: <h0>BGP Attribute TEST</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-18-cisco]
tags: [Network]








---

# BGP Attribute TEST



![IMG_0515](/assets/cisco_post_img/2025-02-18-BGP Attribute/IMG_0515.jpeg)

![BGP Attribute](/assets/cisco_post_img/2025-02-18-BGP Attribute/BGP Attribute.png)

## 구성 조건

1. **IP 설정** (2.2.x,x)
2. **AS 34에 IGP (OSPF) 설정**
   - **2.2.13.0 네트워크는 OSPF 라우팅 시키지 말 것**
3. **BGP 설정**
   - **EBGP**
   - **IBGP**
   - **모든 라우터의 LOOPBACK을 BGP로 ADVERTISE 할 것**



------

### 1.  IP 설정

```bash
R1
conf t
int lo0
ip add 2.2.1.1 255.255.255.0
no sh
int e0/0
ip add 2.2.12.1 255.255.255.0
no sh
int e0/1
ip add 2.2.13.1 255.255.255.0
no sh

R2
conf t
int lo 0
ip add 2.2.2.2 255.255.255.0
no sh
int e0/0
ip add 2.2.12.2 255.255.255.0
no sh

R3

conf t
int lo 0
ip add 2.2.3.3 255.255.255.0
int e0/1
ip add 2.2.13.3 255.255.255.0
no sh
int e1/1
ip add 2.2.34.3 255.255.255.0
no sh


R4
conf t
int lo 0
ip add 2.2.4.4 255.255.255.0
no sh
int e1/1
ip add 2.2.34.4 255.255.255.0
no sh

```

------

### 2.  AS 34에 IGP (OSPF) 설정

- ### 2.2.13.0 네트워크는 OSPF 라우팅 시키지 말 것

```bash
ospf

R1
router ospf 1
router-id 2.2.1.1
int lo 0
ip ospf 1 area 0
int e0/0
ip ospf 1 area 0

R2

router ospf 2
router-id 2.2.2.2
int lo 0
ip ospf 2 area 0
int e0/0
ip ospf 1 area 0


R3
R3(config)#router ospf 3
R3(config-router)#router-id 1.1.3.3
R3(config-router)#int lo 0
R3(config-if)#ip ospf 3 area 0
ip ospf network point-to-point
R3(config-if)#int e1/1
R3(config-if)#ip ospf 3 area 0
R3(config-if)#



R4

router ospf 4
router-id 2.2.4.4
int lo 0
ip ospf 4 area 0
int e1/1
ip ospf 4 area 0



```

------

### 3.  BGP 설정

- ### EBGP
- ### IBGP
- ### 모든 라우터의 LOOPBACK을 BGP로 ADVERTISE 할 것

```bash
R1
router bgp 1
bgp router-id 2.2.1.1
nei 2.2.12.2 remote-as 2
neighbor 2.2.13.3 remote-as 34
net 2.2.1.0 mask 255.255.255.0

R2
router bgp 2
bgp router-id 2.2.2.2
nei 2.2.12.1 remote-as 1
net 2.2.2.0 mask 255.255.255.0


R3
router bgp 34
bgp router-id 2.2.3.3
neighbor 2.2.13.1 remote-as 1
neighbor 2.2.4.4 remote-as 34
neighbor 2.2.4.4 update-source loopback 0
network 2.2.3.0 mask 255.255.255.0
neighbor 2.2.4.4 next-hop-self

R4
router bgp 34
bgp router-id 2.2.4.4
neighbor 2.2.3.3 remote-as 34
neighbor 2.2.3.3 update-source loopback 0
net 2.2.4.0 mask 255.255.255.0
```



**📌 네트워크 구성 및 설정 요약 (BGP + OSPF)**

**🔹 1. IP 설정**

각 라우터의 **인터페이스 및 Loopback IP** 설정:

| **라우터** | **인터페이스** | **IP 주소** | **서브넷**    |
| ---------- | -------------- | ----------- | ------------- |
| **R1**     | Loopback0      | 2.2.1.1     | 255.255.255.0 |
|            | e0/0           | 2.2.12.1    | 255.255.255.0 |
|            | e0/1           | 2.2.13.1    | 255.255.255.0 |
| **R2**     | Loopback0      | 2.2.2.2     | 255.255.255.0 |
|            | e0/0           | 2.2.12.2    | 255.255.255.0 |
| **R3**     | Loopback0      | 2.2.3.3     | 255.255.255.0 |
|            | e0/1           | 2.2.13.3    | 255.255.255.0 |
|            | e1/1           | 2.2.34.3    | 255.255.255.0 |
| **R4**     | Loopback0      | 2.2.4.4     | 255.255.255.0 |
|            | e1/1           | 2.2.34.4    | 255.255.255.0 |



**현재까지의 구성 요약**

​	1.	**R1 ↔ R2 (EBGP)**

​	2.	**R1 ↔ R3 (EBGP)**

​	3.	**R3 ↔ R4 (IBGP, Loopback으로 Peer 맺음)**

​	4.	**R3, R4 내부 OSPF 설정 (2.2.13.0 제외)**

​	5.	**모든 라우터의 Loopback IP를 BGP로 Advertise**





## **BGP 속성(BGP Attributes) 동작 방식 및 효과**



**Local Preference (로컬 프리퍼런스)**

​	•	**설정 여부:** 없음 (기본값 100)

​	•	**동작 방식:**

​	•	**AS 내부에서 경로를 선호하도록 결정**하는 속성.

​	•	값이 **높을수록** 선호됨.

​	•	주로 **IBGP에서 경로 우선순위를 결정**할 때 사용.

​	•	**현재 설정의 효과:**

​	•	Local Preference 값을 조정하지 않았기 때문에 **기본값(100)** 이 사용됨.

​	•	R3과 R4 사이의 IBGP에서 **추가적인 우선순위 조정 없이 기본 BGP 경로 선택 방식**을 따르게 됨.

**AS Path (AS 경로)**

​	•	**설정 여부:** 없음 (자동 적용)

​	•	**동작 방식:**

​	•	경로의 **AS 갯수가 적을수록** 선호됨.

​	•	EBGP에서 경로를 학습할 때 **AS-PATH 속성이 증가**하면서 **멀리 있는 경로보다 짧은 경로를 선호**.

​	•	**현재 설정의 효과:**

​	•	**R1 ↔ R2 ↔ R4** 와 **R1 ↔ R3 ↔ R4** 경로 중 **AS-PATH가 짧은 경로가 우선적으로 선택**됨.

​	•	**멀리 있는 AS를 통한 경로는 덜 선호**될 가능성이 큼.

**3️⃣ Next-Hop (다음 홉)**

​	•	**설정 여부:** neighbor X.X.X.X next-hop-self (✔ 설정됨)

​	•	**동작 방식:**

​	•	BGP는 **경로의 Next-Hop을 변경하지 않음** → IBGP에서는 직접 연결되지 않은 라우터가 Next-Hop 정보를 학습할 수 없음.

​	•	next-hop-self을 설정하면 **자신을 Next-Hop으로 변경**하여 전달 가능.

​	•	**현재 설정의 효과:**

​	•	**R3이 R4에게 전달하는 경로에서 R3을 Next-Hop으로 지정** (✅ 올바르게 설정됨).

​	•	**R4가 R3을 통해 외부 네트워크에 접근 가능**.

**4️⃣ Weight (가중치)**

​	•	**설정 여부:** 없음 (기본값 0, 직접 학습한 경로는 32768)

​	•	**동작 방식:**

​	•	**Cisco 전용 속성**으로, 라우터가 직접 학습한 경로에 높은 가중치를 부여.

​	•	가중치가 높은 경로가 우선적으로 선택됨.

​	•	**현재 설정의 효과:**

​	•	가중치를 따로 설정하지 않았으므로, **라우터가 직접 학습한 경로(32768)가 우선**.

​	•	IBGP를 통해 학습한 경로는 기본적으로 가중치 0을 가짐.

**5️⃣ MED (Multi-Exit Discriminator)**

​	•	**설정 여부:** 없음

​	•	**동작 방식:**

​	•	**두 개 이상의 EBGP 경로가 존재할 때 낮은 값이 우선됨.**

​	•	주로 **멀티홈(두 개의 ISP 연결) 시 특정 ISP 경로를 선호할 때** 사용됨.

​	•	**현재 설정의 효과:**

​	•	MED 값이 설정되지 않아, **기본적으로 AS-PATH가 짧은 경로를 우선 선택**.

**🔎 설정된 BGP 속성들의 효과 정리**

| **속성**             | **적용 여부**          | **동작 방식**                      | **현재 설정의 효과**                  |
| -------------------- | ---------------------- | ---------------------------------- | ------------------------------------- |
| **Local Preference** | ❌ (기본값 100)         | 내부 AS 내에서 경로 선호도 결정    | AS 내부에서 우선순위 조정 없음        |
| **AS Path**          | ✅ 자동 적용            | AS 경로가 짧을수록 선호            | 짧은 AS-PATH를 가진 경로가 우선       |
| **Next-Hop**         | ✅ next-hop-self 설정됨 | IBGP에서 Next-Hop 변경             | R3이 R4에 경로를 올바르게 전달 가능   |
| **Weight**           | ❌ (기본값 0)           | 직접 학습한 경로 우선              | 직접 학습한 경로(32768)만 선호됨      |
| **MED**              | ❌ (기본값 0)           | Multi-Exit 환경에서 낮은 값이 우선 | AS-PATH가 짧은 경로를 기본적으로 선호 |

**🔥 현재 설정의 개선점 및 추가 가능 설정**



💡 **1. Local Preference 조정**

​	•	만약 **R3 ↔ R4 연결을 더 선호**하고 싶다면, R3에서 Local Preference를 올려주는 것이 가능.

```
R3(config-router)# neighbor 2.2.4.4 route-map LOCAL_PREF in
R3(config-route-map)# set local-preference 200
```

➡ **결과:** R3 ↔ R4 경로가 더 선호됨.

💡 **2. MED 활용 (멀티 ISP 환경일 경우)**

​	•	EBGP에서 특정 ISP를 선호하려면, MED 값을 낮게 설정 가능.

```
R1(config-router)# neighbor 2.2.13.3 route-map SET_MED out
R1(config-route-map)# set metric 50
```

➡ **결과:** R1 → R3 → R4 경로가 더 선호됨.

💡 **3. 정교한 필터링 (Route-Map)**

​	•	특정 경로를 학습하지 않도록 필터링 가능.

```
R3(config-router)# neighbor 2.2.4.4 route-map BLOCK_ROUTE out
R3(config-route-map)# deny 10
```

➡ **결과:** 특정 경로가 R4에 전달되지 않음.

**✅ 결론**



현재 BGP 속성들은 **기본적인 동작을 따르고 있으며**, next-hop-self 설정을 통해 올바르게 IBGP 경로가 전달되고 있음.



하지만 추가적인 **Local Preference, MED, Route-Map** 등을 활용하면 **더 정교한 경로 제어**가 가능함.

📌 **추천 설정**

1️⃣ **R3에서 Local Preference 값을 올려서 R4 ↔ R3 경로를 더 선호**

2️⃣ **EBGP에서 특정 ISP를 선호하려면 MED 값 조정**

3️⃣ **필요 없는 경로는 Route-Map을 통해 필터링**

➡ **이렇게 하면 네트워크 트래픽 흐름을 최적화할 수 있음!** 🚀
