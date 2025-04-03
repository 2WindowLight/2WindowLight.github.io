---
title: <h0>CCNA Enterprise Networking, Security, and Automation 8 IP Sec</h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-28-cisco]
tags: [Network]




---





## part 2

------



R1

```bash

R1(config)#crypto isakmp enable 
R1(config)#crypto isakmp policy 10
R1(config-isakmp)#hash sha
R1(config-isakmp)#authentication pre-share
R1(config-isakmp)#group 5
R1(config-isakmp)#lifetime 3600
R1(config-isakmp)#encryption aes 256
R1(config-isakmp)#exit
R1(config)#crypto isakmp key cisco123 add 10.2.2.1
R1(config)#crypto ipsec transform-set 50 esp-aes 256 esp-sha-hmac
R1(config)#exit
R1(config)#crypto ipsec sec
R1(config)#crypto ipsec security-association lifetime sec 1800
R1(config)#access-list 101 permit ip 192.168.1.0 0.0.0.255 192.168.3.0 0.0.0.255
R1(config)#crypto map CMAP 10 ipsec-isakmp 
R1(config-crypto-map)#match add 101
R1(config-crypto-map)#set peer 10.2.2.1
R1(config-crypto-map)#set pfs group5
R1(config-crypto-map)#set transform-set 50
R1(config-crypto-map)#set security-association lif sec 900
R1(config-crypto-map)#exit
R1(config)#

R1#sh crypto isakmp policy 

Global IKE policy
Protection suite of priority 10
        encryption algorithm:    AES - Advanced Encryption Standard (256 bit keys).
        hash algorithm:         Secure Hash Standard
        authentication method:  Pre-Shared Key
        Diffie-Hellman group:   #1 (768 bit)
        lifetime:               3600 seconds, no volume limit
R1#
R1(config)#interface G0/0/0
R1(config-if)#crypto map CMAP
R1(config)#end

```



R3

```
crypto isakmp enable 
crypto isakmp policy 10
hash sha
authentication pre-share
group 5
lifetime 3600
encryption aes 256
crypto isakmp key cisco123 address 10.1.1.1
crypto ipsec transform-set 50 esp-aes 256 esp-sha-hmac
exit
crypto ipsec security-association lifetime seconds 1800
access-list 101 permit ip 192.168.3.0 0.0.0.255 192.168.1.0 0.0.0.255
crypto map CMAP 10 ipsec-isakmp
match address 101
set peer 10.1.1.1
set pfs group5
set transform-set 50
set security-association lifetime seconds 900
exit
interface G0/0/1
crypto map CMAP
end
```



### 라우터 password 복구 방법

1. 라우터에 console 연결

2. 라우터 on/off 후, ctrl+break 

   rommon 1>

3. config register 값을 0x2142 로 변경

   rommon 2> confreg -x2142

4. rommon 3> reset

5. start-up 설정을 메모리로 복사

   copy start run

6. password 복구

   enable pass cisco

7. config-register 0x2102

8. 설정 저장 후 reload

   

copy start run nvram에 있는 것을 메모리로 이동