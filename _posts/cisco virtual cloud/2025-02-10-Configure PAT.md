---
title: <h0>Configure PAT/h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]






---

## Configure PAT

**PAT(포트 주소 변환, NAT Overload)란?**

​	•	**하나의 공인 IP를 여러 사설 IP가 공유하여 인터넷을 사용할 수 있도록 하는 기술**

​	•	**각 장치는 포트 번호를 활용하여 구분됨**

​	•	**홈 네트워크, 기업 네트워크에서 가장 일반적으로 사용됨**

![image-20250210180411279](/Users/changhee/2WindowLight.github.io/_posts/cisco virtual cloud/assets/image-20250210180411279.png)

### **Part 1: Dynamic NAT with Overload 구성 (R1)**

**ACL(Access Control List) 설정**

```bash
R1(config)# access-list 1 permit 172.16.0.0 0.0.255.255
```

**NAT 풀(Pool) 생성** - **R1에서 사용 가능한 공인 IP 주소 2개를 NAT 풀에 추가**

```bash
R1(config)# ip nat pool ANY_POOL_NAME 209.165.200.233 209.165.200.234 netmask 255.255.255.252
```

**ACL과 NAT 풀 연결 + Overload 활성화** - **여러 장치가 하나의 공인 IP를 공유하도록 설정 (PAT 방식)**

```bash
R1(config)# ip nat inside source list 1 pool ANY_POOL_NAME overload
```



**Single Address NAT Overload(단일 주소 NAT 오버로드) 방식**

```bash
R1(config)# ip nat inside source list 1 interface s0/0/1 overload
R1(config)# access-list 1 permit 172.16.0.0 0.0.255.255
```

* **단일 공인 IP 주소만 사용** — 여기서는 s0/0/1 인터페이스의 IP 주소
  * 여러 사설 IP들이 동일한 공인 IP의 **다른 포트 번호**를 사용해 NAT됨
  * 하나의 공인 IP 주소만 사용되므로, IP 자원이 적을 때 유리함
    * 단점: 트래픽이 많아질 경우 해당 IP에 부하 집중

| **구성 요소**    | **설명**                                               |
| ---------------- | ------------------------------------------------------ |
| ip nat inside    | 내부 → 외부 방향의 NAT 트래픽 지정 (사설 IP가 변환됨)  |
| source list 1    | ACL 1번에 정의된 IP만 NAT 대상이 됨                    |
| interface s0/0/1 | 공인 IP가 설정된 실제 인터페이스 (외부 방향)           |
| overload         | 여러 사설 IP가 하나의 공인 IP를 포트로 공유 (PAT 방식) |

 **NAT 인터페이스 설정** - **인터페이스에 inside(내부)와 outside(외부) 설정 적용**

```bash
R1(config)# interface s0/1/0
R1(config-if)# ip nat outside
R1(config-if)# exit

R1(config)# interface g0/0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

R1(config)# interface g0/0/1
R1(config-if)# ip nat inside
R1(config-if)# exit
```

------

### pc1 ... L2 에서 Server1 에 네트워크 테스트후 결과

```bash
R2(config)#do sh ip nat translations
```



```bash
icmp 209.165.202.130:1028172.17.11.11:1     209.165.201.5:1    209.165.201.5:1028
icmp 209.165.202.130:1029172.17.11.11:2     209.165.201.5:2    209.165.201.5:1029
icmp 209.165.202.130:1030172.17.11.11:3     209.165.201.5:3    209.165.201.5:1030
icmp 209.165.202.130:1031172.17.11.11:4     209.165.201.5:4    209.165.201.5:1031
icmp 209.165.202.130:1 172.17.10.11:1     209.165.201.5:1    209.165.201.5:1
icmp 209.165.202.130:2 172.17.10.11:2     209.165.201.5:2    209.165.201.5:2
icmp 209.165.202.130:3 172.17.10.11:3     209.165.201.5:3    209.165.201.5:3
icmp 209.165.202.130:4 172.17.10.11:4     209.165.201.5:4    209.165.201.5:4
```

​	•	**사설 IP** 172.17.11.11 → **공인 IP** 209.165.201.5 로 변환됨.

​	•	**포트 번호를 사용하여 다중 세션을 구분 (PAT 적용 확인됨).**

​	•	목적지 IP 209.165.202.130(서버)로 가는 패킷이 변환되었음.

​	•	변환된 패킷이 ICMP(핑) 요청이며, 패킷 식별자를 1028, 1029 등으로 구분.

​	•	원래 소스 IP 172.17.11.11이 209.165.201.5로 변경됨(즉, NAT이 정상적으로 작동).



### **Part 3: PAT (인터페이스 기반) 구성 (R2)**

**ACL 설정**

```bash
R2(config)# access-list 2 permit 172.17.0.0 0.0.255.255
```

**NAT 변환 규칙 적용** - **인터넷 연결 인터페이스를 공인 IP로 사용하며, 다중 사용자를 지원하도록 설정 (PAT 활성화)**

```bash
R2(config)# ip nat inside source list 2 interface s0/1/1 overload
```

**NAT 인터페이스 설정** - **인터페이스에 NAT 역할 지정 (내부/외부 구분)**

```bash
R2(config)# interface g0/0/0
R2(config-if)# ip nat inside
R2(config-if)# exit

R2(config)# interface g0/0/1
R2(config-if)# ip nat inside
R2(config-if)# exit

R2(config)# interface s0/1/1
R2(config-if)# ip nat outside
R2(config-if)# exit
```



### R2의 하위에서 네트워크 테스트 후 결과 확인

```bash
R2(config)#do sh ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
icmp 209.165.202.130:1024172.17.11.10:9     209.165.201.5:9    209.165.201.5:1024
icmp 209.165.202.130:1025172.17.11.10:10    209.165.201.5:10   209.165.201.5:1025
icmp 209.165.202.130:1026172.17.11.10:11    209.165.201.5:11   209.165.201.5:1026
icmp 209.165.202.130:1027172.17.11.10:12    209.165.201.5:12   209.165.201.5:1027
icmp 209.165.202.130:1028172.17.11.11:1     209.165.201.5:1    209.165.201.5:1028
icmp 209.165.202.130:1029172.17.11.11:2     209.165.201.5:2    209.165.201.5:1029
icmp 209.165.202.130:1030172.17.11.11:3     209.165.201.5:3    209.165.201.5:1030
icmp 209.165.202.130:1031172.17.11.11:4     209.165.201.5:4    209.165.201.5:1031
icmp 209.165.202.130:1 172.17.10.11:1     209.165.201.5:1    209.165.201.5:1
icmp 209.165.202.130:2 172.17.10.11:2     209.165.201.5:2    209.165.201.5:2
icmp 209.165.202.130:3 172.17.10.11:3     209.165.201.5:3    209.165.201.5:3
icmp 209.165.202.130:4 172.17.10.11:4     209.165.201.5:4    209.165.201.5:4
```

