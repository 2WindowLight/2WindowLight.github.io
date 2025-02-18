---
title: <h0>BGP Attribute</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-18-cisco]
tags: [Network]








---

# BGP Attribute



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

R4
router bgp 34
bgp router-id 2.2.4.4
neighbor 2.2.3.3 remote-as 34
neighbor 2.2.3.3 update-source loopback 0
net 2.2.4.0 mask 255.255.255.0
```

