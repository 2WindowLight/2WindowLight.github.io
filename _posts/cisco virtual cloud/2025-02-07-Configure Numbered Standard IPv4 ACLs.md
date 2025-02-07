---
title: <h0>Configure Numbered/Named Standard IPv4 ACLs</h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]










---

## Configure Numbered Standard IPv4 ACLs

![image-20250207173403597](/Users/changhee/2WindowLight.github.io/_posts/cisco virtual cloud/assets/image-20250207173403597.png)

### R2에서 pc2 쪽의 192.168.11.0 ~ 255 모든 ip 를 deny

```bash
R2(config)# access-list 1 deny 192.168.11.0 0.0.0.255
```

위에서 입력한 192.168.11.0 을 제외하고 모든 ip 허용

```bash
R2(config)# access-list 1 permit any
```

### deny/ permit 리스트 여부 

```bash
R2# show access-lists
```

```
Standard IP access list 1

10 deny 192.168.11.0 0.0.0.255

20 permit any
```

### 아웃바운드 명령어

```bash
R2(config)# interface GigabitEthernet0/0
R2(config-if)# ip access-group 1 out
```

**명령어 구조**

​	•	ip access-group → 인터페이스에 **ACL을 적용**하는 명령어

​	•	1 → 적용할 **ACL 번호 (여기서는 Standard ACL 1번)**

​	•	out → **아웃바운드 트래픽(출력 방향)** 에 적용

### 인바운드 명령어

```bash
R2(config-if)# ip access-group 1 out
```

**인터페이스로 들어오는(Ingress) 트래픽을 필터링**



------

## **Configure Named Standard IPv4 ACLs**

![image-20250207175756876](/Users/changhee/2WindowLight.github.io/_posts/cisco virtual cloud/assets/image-20250207175756876.png)

### Standard ACL 설정

```bash
R1(config)# ip access-list standard File_Server_Restrictions
R1(config-std-nacl)# permit host 192.168.20.4
R1(config-std-nacl)# permit host 192.168.100.100
R1(config-std-nacl)# deny any
```

**File_Server_Restrictions** 이라는 이름을 가진 표준 ACL 생성

```bash
R1(config)# ip access-list standard File_Server_Restrictions
```

​	•	**이름 기반 표준 ACL(Standard ACL)을 생성**

​	•	ACL의 이름은 **File_Server_Restrictions**

​	•	**숫자가 아닌 이름 기반으로 ACL을 설정하는 방식** (access-list <번호> 대신 사용 가능)

**192.168.20.4 와 192.168.100.100  IP 주소를 허용 (permit) 하고 나머지는 deny**

```bash
R1(config-std-nacl)# permit host 192.168.20.4
R1(config-std-nacl)# permit host 192.168.100.100
R1(config-std-nacl)# deny any
```

### 아웃바운드 명령어

```bash
R1(config-if)# ip access-group File_Server_Restrictions out
```

