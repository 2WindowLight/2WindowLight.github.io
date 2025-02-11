---
title: <h0>Configure Extended ACLs - Scenario 1/h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]













---



## **Configure Extended ACLs - Scenario 1**

### **확장(Extended) ACL**을 구성하여 **PC1과 PC2의 네트워크 접근을 제어**

## -ACL 이란?

컴퓨터 보안에서 액세스 제어 목록(ACL)은 시스템 리소스와 관련된 권한 목록이고, 방화벽을 구축하는 데 있어서 가장 중요한 요소이며 **트래픽 필터링(Traffic Filtering)의 기능**을 합니다. 
\- **허가(Permit)되지 않은 이용자가 라우터나 네트워크에 접근하려고 하는 것을 차단**하고 출발지 주소(Source address), 목적지 주소(Destination address), 포트 번호(Port number), 프로토콜(Protocol)로 특정 패킷을 필터링할 수 있고 허가 및 거부할 수 있습니다.

![image-20250210150301559](/Users/changhee/2WindowLight.github.io/_posts/cisco virtual cloud/assets/image-20250210150301559.png)

**요구사항 분석**



**PC1 → FTP(파일 전송) 허용, ICMP(ping) 허용, 다른 트래픽 차단**

**PC2 → HTTP(웹 접속) 허용, ICMP(ping) 허용, 다른 트래픽 차단**

**PC1과 PC2는 서로 통신할 수 없어야 함**



**Part 1: 확장 ACL 100 설정 (PC1에 대한 ACL 구성)**

 **PC1에서 서버로 가는 트래픽을 필터링하는 ACL 100을 구성**

```bash
R1(config)# access-list ?
<1-99>   IP 표준(standard) ACL 번호
<100-199> IP 확장(extended) ACL 번호
```



**FTP(파일 전송) 및 ICMP(ping) 트래픽 허용**

**(1) FTP 트래픽 허용 (PC1 → 서버)**

```bash
R1(config)# access-list 100 permit tcp 172.22.34.64 0.0.0.31 host 172.22.34.62 eq ftp
```

•	permit tcp 172.22.34.64 0.0.0.31 → **PC1이 속한 네트워크(172.22.34.64/27)의 모든 장치 허용**

•	host 172.22.34.62 → **서버(172.22.34.62)만 목적지로 설정**

•	eq ftp → **FTP(포트 21) 트래픽만 허용**

------

**tcp를 사용한 이유**는 **FTP가 TCP 기반 프로토콜이기 때문**



**✅ 1. FTP는 TCP를 사용**

​	•	FTP(File Transfer Protocol)는 **신뢰성 있는 연결을 보장하기 위해 TCP 기반으로 동작**합니다.

​	•	TCP는 **패킷 손실이 발생하면 재전송을 수행하는 연결 지향 프로토콜**입니다.

​	•	반면, **UDP는 비연결형 프로토콜**이며, FTP와 같이 중요한 데이터 전송에 사용되지 않습니다.



**✅ 2. 만약 ip를 사용했다면?**

​	•	만약 permit ip를 사용했다면, **TCP뿐만 아니라 UDP도 허용**됩니다.

​	•	하지만 FTP는 **UDP를 사용하지 않으므로, UDP까지 허용할 필요가 없음**.

​	•	**보안적인 이유**로 불필요한 프로토콜을 허용하지 않도록 tcp를 지정하는 것이 더 안전함.

------

**(2) ICMP(ping) 허용 (PC1 → 서버)**

```bash
R1(config)# access-list 100 permit icmp 172.22.34.64 0.0.0.31 host 172.22.34.62
```

•	permit icmp → **ping 허용**

•	**ICMP는 ping을 위한 프로토콜이므로 특정 포트를 지정할 필요 없음**

------

 **ACL을 라우터 인터페이스에 적용**

```bash
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip access-group in
```



**ACL 확인**

```bash
R1# show access-lists
```

```bash
Extended IP access list 100
    10 permit tcp 172.22.34.64 0.0.0.31 host 172.22.34.62 eq ftp
    20 permit icmp 172.22.34.64 0.0.0.31 host 172.22.34.62
```

 **PC1 → 서버로 FTP(포트 21) 및 ICMP(ping) 가능!**

 **다른 모든 트래픽은 기본적으로 차단됨 (deny any any 적용됨)**



------

## **Part 2: 확장 ACL HTTP_ONLY 설정 (PC2에 대한 ACL 구성)**

**ACL 이름을 사용하여 설정**

```bash
R1(config)# ip access-list extended HTTP_ONLY
```

ip access-list extended HTTP_ONLY → **이름이 “HTTP_ONLY”인 ACL 생성**



**HTTP(웹 접속) 및 ICMP(ping) 트래픽 허용**

**(1) HTTP 트래픽 허용 (PC2 → 서버)**

```bash
R1(config-ext-nacl)# permit tcp 172.22.34.96 0.0.0.15 host 172.22.34.62 eq www
```

​	•	permit tcp 172.22.34.96 0.0.0.15 → **PC2가 속한 네트워크(172.22.34.96/28)의 모든 장치 허용**

​	•	host 172.22.34.62 → **서버(172.22.34.62)만 목적지로 설정**

​	•	eq www → **HTTP(포트 80) 트래픽만 허용**

**(2) ICMP(ping) 허용 (PC2 → 서버)**

```bash
R1(config-ext-nacl)# permit icmp 172.22.34.96 0.0.0.15 host 172.22.34.62
```

permit icmp → **ping 허용**

**ACL을 라우터 인터페이스에 적용**

```bash
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip access-group HTTP_ONLY in
```

ip access-group HTTP_ONLY in → **G0/1 인터페이스에서 들어오는 트래픽을 HTTP_ONLY ACL로 필터링**



**질문해볼 만한 부분**

1. **왜 ACL을 ‘inbound’로 적용했을까?**

​	•	**라우터 입장에서 내부로 들어오는 패킷을 필터링**해야 하므로 **“in” 방향으로 설정**.

2. **PC1과 PC2가 서로 통신할 수 없는 이유는?**

​	•	ACL에서 **서버로 가는 트래픽만 허용하고, 다른 트래픽(PC1 ↔ PC2 통신)은 차단**했기 때문.