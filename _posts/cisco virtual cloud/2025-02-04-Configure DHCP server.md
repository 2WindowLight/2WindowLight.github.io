---
title: <h0>2/03 Configure DHCP server</h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]







---

## Stateless dhcpv6 설정

![image-20250204110103091](/Users/changhee/2WindowLight.github.io/_posts/images/2025-02-04-test/image-20250204110103091.png)

```bash
en
conf
ipv6 unicast-routing #IPv6 라우팅 활성화 (라우터가 IPv6 패킷을 포워딩할 수 있도록 설정).
!
ipv6 dhcp pool IPV6-STATELESS # "IPV6-STATELESS”라는 Stateless DHCPv6 풀 생성.
dns-server 2001:db8:acad:1:254 # DHCPv6 풀에서 제공할 DNS 서버 주소 설정 (클라이언트가 이 DNS 서버를 사용).	
domain-name example.com # DHCPv6 클라이언트에게 제공할 도메인 이름 설정.
!
interface g0/0/1
ipv6 add fe80::1 link-local # 링크-로컬 IPv6 주소 (fe80::1) 할당 (이 인터페이스에서만 사용되는 로컬 주소).
ipv6 add 2001:db8:acad:1::1/64 # 전역 유니캐스트 IPv6 주소 (2001:db8:acad:1::1/64) 할당.
ipv6 nd other-config-flag
# RA(Router Advertisement) 메시지의 O 플래그(Other-config-flag) 활성화
#→ 클라이언트가 SLAAC로 IP 주소를 설정하되, 추가 정보(DNS 등)는 Stateless DHCPv6 서버에서 가져오도록 안내.
ipv6 dhcp server IPV6-STATLESS
# 인터페이스를 Stateless DHCPv6 서버(“IPV6-STATELESS”)와 연결하여 추가 설정(DNS, 도메인 이름 등)을 제공.
no sh
```

------

## R3 설정 (Client)

```
en
conf
ipv6  unicast-routing
!
int g0/0/1
ipv6 enable
ipv6 add autoconfig
```

------

```
ipv6 enable
```

**해당 인터페이스에서 IPv6 기능을 활성화**.

이 명령어를 입력하면 **링크-로컬 주소(LLA, fe80::/10 범위)가 자동으로 생성됨**.

**IPv6 주소를 별도로 설정하지 않아도 인터페이스에서 기본적인 IPv6 통신이 가능**.

------

```
ipv6 address autoconfig
```

**SLAAC(Stateless Address Autoconfiguration)를 사용하여 자동으로 IPv6 주소 설정**.

해당 인터페이스가 **라우터로부터 RA(Router Advertisement) 메시지를 수신하면, 네트워크 프리픽스 정보를 기반으로 IPv6 글로벌 주소(GUA)를 자동 생성**.

추가적으로 **RA 메시지의 O 플래그(Other-config-flag)가 설정된 경우, DHCPv6 서버에서 추가적인 정보(DNS 등)를 가져올 수도 있음**.

------

### 정리

ipv6 enable → **IPv6 활성화 및 링크-로컬 주소 자동 생성**.

ipv6 address autoconfig → **SLAAC을 통해 글로벌 IPv6 주소 자동 설정**.

------



```bash
R3# show ipv6 interface brief
GigabitEthernet0/0/0   [up/up]
    unassigned
GigabitEthernet0/0/1   [up/up]
    FE80::2FC:BAFF:FE94:29B1
    2001:DB8:ACAD:1:2FC:BAFF:FE94:29B1
Serial0/1/0            [up/up]
    unassigned
Serial0/1/1            [up/up]
    unassigned
R3#
```



------

## Configure a Stateful DHCPv6 Server

 **Enable IPv6 routing.**

```bash
R1(config)# ipv6 unicast-routing
R1(config)# 
```

**Define a DHCPv6 pool name.**	

```bash
R1(config)# ipv6 dhcp pool IPV6-STATEFUL
R1(config-dhcpv6)#
```

**Step 3. Configure the DHCPv6 pool.**

```bash
R1(config-dhcpv6)# address prefix 2001:db8:acad:1::/64
R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
R1(config-dhcpv6)# domain-name example.com
R1(config-dhcpv6)#
```

**Bind the DHCPv6 pool to an interface.**

```bash
R1(config)# interface GigabitEthernet0/0/1
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# ipv6 dhcp server IPV6-STATEFUL
R1(config-if)# no shut
R1(config-if)# end
R1#
```



------

