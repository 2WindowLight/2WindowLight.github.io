---
title: <h0>2/03 Switch Setting</h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]





---

## 1. 스위치 기초 설정

```
en
conf t
hostname SW1
no ip domain-lookup
!
line vty 0 4
pass cisco
!
line con 0
exec-time 0
logging sync
```



------



##  2. SW vlan 및 interface 할당

```
en
conf t
vlan 2
int f0/3
switchport mode access // trunk로 만들겠다.
swithcport acess vlan 2

```

### 2-1 Switch 끼리 trunk mode access

```
SW2(config)#int f0/1
SW2(config-if)#sw
SW2(config-if)#switchport mode trunk


SW2(config-if)#switchport trunk native vlan 256 // native 설정
```



------

## 3. 라우터 초기 설정

```
Router(config)#hostname R1
R1(config)#no ip domain-lookup
R1(config)#line vty 0 4
R1(config-line)#pass cisco
R1(config-line)#line con 0
R1(config-line)#exec-time 0
R1(config-line)#logging
R1(config-line)#logging sync
```



------

## 4. 라우터 서브인터페이스 설정

```
R1(config)#int f0/0.1
R1(config-subif)#encapsulation dot1Q 1 // 라우터의 서브인터페이스 Vlan 1에 연결하는 기능
R1(config-subif)#ip add 10.10.1.1 255.255.255.0
R1(config-subif)#exit
```

​	•	encapsulation → 이 인터페이스에서 **트래픽을 태그(Tagging)할 방법을 설정**

​	•	dot1Q → **IEEE 802.1Q 프로토콜**을 사용하여 VLAN 태깅 활성화

​	•	1 → 이 서브인터페이스는 **VLAN 1과 연결**됨을 의미

```
R1(config)#int f0/0.2
R1(config-subif)#encapsulation dot1Q 2
R1(config-subif)#ip add 10.10.2.1 255.255.255.0
```

```
R1(config)#int f0/0
R1(config-if)#no sh
```

