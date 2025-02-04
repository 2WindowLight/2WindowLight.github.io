---
title: <h0>2/03 configure HSRP</h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]






---

# Configure HSRP

![sda](/Users/changhee/2WindowLight.github.io/_posts/images/2025-02-04-Configure HSRP/sda.png)

### SW1

```bash
en
conf
ip routing
interface range e0/0-3
no switchport
#	•	선택한 인터페이스를 Layer 3 모드(라우팅 가능)로 변경.
#•	기본적으로 스위치 포트는 Layer 2 모드이므로, no switchport 명령어를 사용하여 라우터 인터페이스처럼 작동하도록 설정.

int e0/0
ip add 1.1.14.1 255.255.255.0
no sh
int e0/1
ip add 1.1.100.201 255.255.255.0
no sh
```

### SW3

```
en
conf
ip routing
interface range e0/0-3
no switchport

int e0/1
ip add 1.1.34.3 255.255.255.0
no sh
int e0/0
ip add 1.1.100.203 255.255.255.0
no sh
```

### SW4

```
en
conf
ip routing
interface range e0/0-3
no switchport

int e0/0
ip add 1.1.14.4 255.255.255.0
no sh
int e0/1
ip add 1.1.34.4 255.255.255.0
no sh

int lo 0
ip add 1.1.4.4 255.255.255.255
```

### pc1

```
ip 1,1,100.1/24 1.1.100.254
```

### pc2

```
ip 1,1,100.2/24 1.1.100.254
```



```
default int 0/0 # 초기화
```

```
standby 1 track 5 de 100
```



------

## OSPF 에어리어 0 설정

### SW1

```
router ospf 1
network 1.1.14.1 0.0.0.0 a 0
network 1.1.100.201 0.0.0.0 a 0
```

SW3

```
router ospf 1
network 1.1.34.3 0.0.0.0 a 0
network 1.1.100.203 0.0.0.0 a 0
```

SW4

```
router ospf 1
network 1.1.4.4 0.0.0.0 a 0
network 1.1.14.4 0.0.0.0 a 0
network 1.1.34.4 0.0.0.0 a 0
```

