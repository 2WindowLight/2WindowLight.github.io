---
title: <h0>Configure Extended ACLs - Scenario 1/h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]














---



## **Configure Extended IPv4 ACLs - Scenario 2**

### **PC1, PC2, PC3의 특정 서비스 접근을 차단하는 Named Extended ACL을 설정**

![image-20250210162244363](/Users/changhee/2WindowLight.github.io/_posts/cisco virtual cloud/assets/image-20250210162244363.png)

**요구사항 분석**



**PC1 → Server1, Server2**

​	•HTTP (포트 80) 및 HTTPS (포트 443) **차단**

**PC2 → Server1, Server2**

​	•FTP (포트 21) **차단**

**PC3 → Server1, Server2**

​	•ICMP (ping) **차단**

------

### **ACL 생성 시작**

```bash
RT1(config)# ip access-list extended ACL
```

ip access-list extended ACL → **이름이 ACL인 확장 ACL을 생성**

------

### **PC1 → HTTP, HTTPS 차단**

```bash
RT1(config-ext-nacl)# deny tcp host 172.31.1.101 host 64.101.255.254 eq 80
RT1(config-ext-nacl)# deny tcp host 172.31.1.101 host 64.101.255.254 eq 443
RT1(config-ext-nacl)# deny tcp host 172.31.1.101 host 64.103.255.254 eq 80
RT1(config-ext-nacl)# deny tcp host 172.31.1.101 host 64.103.255.254 eq 443
```

**PC1에서 Server1 & Server2로 가는 HTTP(80), HTTPS(443) 트래픽 차단**

**TCP 프로토콜을 사용하여 특정 포트(80, 443)만 필터링*

------

### **PC2 → FTP 차단**

```bash
RT1(config-ext-nacl)# deny tcp host 172.31.1.102 host 64.101.255.254 eq 21
RT1(config-ext-nacl)# deny tcp host 172.31.1.102 host 64.103.255.254 eq 21
```

**PC2에서 Server1 & Server2로 가는 FTP(포트 21) 트래픽 차단**

 **TCP 프로토콜을 사용하여 FTP만 차단하고, 다른 서비스는 허용 가능**

------

### **PC3 → ICMP(ping) 차단**

```bash
RT1(config-ext-nacl)# deny icmp host 172.31.1.103 host 64.101.255.254
RT1(config-ext-nacl)# deny icmp host 172.31.1.103 host 64.103.255.254
```

**PC3에서 Server1 & Server2로 가는 ping(icmp) 차단**

**ICMP는 ping 요청에 사용되므로, ping만 차단 가능**

------

### **모든 다른 트래픽 허용**

```bash
RT1(config-ext-nacl)# permit ip any any
```

**기본적으로 ACL에는 deny any any가 존재하므로, 원하는 트래픽이 차단되지 않도록 추가**

**이제 특정 차단 규칙을 제외한 모든 트래픽이 정상적으로 전달됨**

------

### **Part 2: ACL 적용 및 검증**

```bash
RT1(config)# interface GigabitEthernet 0/0
RT1(config-if)# ip access-group ACL in
```

**ACL을 RT1의 G0/0 인터페이스(PC들이 연결된 인터페이스)에 적용**

**트래픽이 라우터에 들어오는(inbound) 방향에서 필터링됨**

------

### **ACL 적용 확인**

```bash
RT1# show access-lists
```



```bash
R1# show ip nat translations
```



```bash
Extended IP access list ACL
10 deny tcp host 172.31.1.101 host 64.101.255.254 eq www
20 deny tcp host 172.31.1.101 host 64.101.255.254 eq 443
30 deny tcp host 172.31.1.101 host 64.103.255.254 eq www
40 deny tcp host 172.31.1.101 host 64.103.255.254 eq 443
50 deny tcp host 172.31.1.102 host 64.101.255.254 eq ftp
60 deny tcp host 172.31.1.102 host 64.103.255.254 eq ftp
70 deny icmp host 172.31.1.103 host 64.101.255.254
80 deny icmp host 172.31.1.103 host 64.103.255.254
90 permit ip any any
```



------

