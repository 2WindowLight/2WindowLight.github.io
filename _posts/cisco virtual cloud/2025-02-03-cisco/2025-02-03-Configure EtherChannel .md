---
title: <h0>2/03 Configure EtherChannel</h0>
author: cotes   
categories: [cisco virtual cloud,2025-02-03-cisco]
tags: [Network]






---

## 1. **Configure EtherChannel** 



* **PAgP를 이용한 EtherChannel 구성 (S1과 S3)****

```bash
S1(config)# interface range f0/21 – 22	
S1(config-if-range)# shutdown

S1(config-if-range)# channel-group 1 mode desirable (채널 그룹에 할당 (PAgP Desirable 모드))
S1(config-if-range)# no shutdown

 

S3(config)# interface range f0/21 - 22
S3(config-if-range)# shutdown
S3(config-if-range)# channel-group 1 mode desirable
S3(config-if-range)# no shutdown
```

------



```bash
S1(config)# interface range f0/21 – 22	
S1(config-if-range)# shutdown
```

​	•	interface range f0/21 – 22 명령어로 f0/21부터 f0/22까지의 두 인터페이스를 한 번에 선택합니다.

​	•	**인터페이스를 shutdown** 하는 이유는 EtherChannel 구성 변경 시 트래픽 혼선을 방지하기 위함입니다.

```bash
S1(config-if-range)# channel-group 1 mode desirable (채널 그룹에 할당 (PAgP Desirable 모드))
```

​	•	channel-group 1 명령으로 선택한 인터페이스들을 그룹 번호 1로 묶습니다.

​	•	mode desirable는 Cisco의 PAgP(Port Aggregation Protocol) 프로토콜을 **적극적으로** 사용하여 협상(negotiation)을 시도한다.

​	•	양쪽 장비(여기서는 S1과 S3)가 모두 **desirable** 모드로 설정되어 있으면 PAgP를 통해 자동으로 EtherChannel이 형성됩니다.

**인터페이스 재활성화 (No shutdown)**

```bash
S1(config-if-range)# no shutdown
```

인터페이스를 다시 활성화하여 구성이 적용

------

**포트채널 인터페이스 구성**

```bash
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk

 

S3(config)# interface port-channel 1
S3(config-if)# switchport mode trunk

```

•	interface port-channel 1로 EtherChannel 인터페이스에 들어간 후,

•	switchport mode trunk 명령을 사용하여 이 채널이 **trunk** 모드로 동작하게 설정합니다.

•	이는 여러 VLAN의 트래픽을 한 채널을 통해 전달할 수 있게 합니다.

**EtherChannel 상태 확인**

```bash
S1# show etherchannel summary
Flags: D - down P - in port-channel
I - stand-alone s - suspended
H - Hot-standby (LACP only)
R - Layer3 S - Layer2
U - in use f - failed to allocate aggregator
u - unsuitable for bundling
w - waiting to be aggregated
d - default port


Number of channel-groups in use: 1
Number of aggregators: 1

Group Port-channel Protocol Ports
------+-------------+-----------+----------------------------------------

1 Po1(SU) PAgP F0/21(P) F0/22(P)
```

​	•	Po1(SU)는 Port-Channel 1이 **활성(S)** 상태이며 **Layer2** EtherChannel임을 나타냅니다.

​	•	각 포트(F0/21, F0/22)가 올바르게 집계(P)되어 있음을 확인할 수 있습니다.



------

## Configure an 802.3ad LACP EtherChannel

```
S1(config)# interface range g0/1 - 2
S1(config-if-range)# shutdown
S1(config-if-range)# channel-group 2 mode active
S1(config-if-range)# no shutdown
S1(config-if-range)# interface port-channel 2
S1(config-if)# switchport mode trunk
```



## Configure a Redundant EtherChannel Link

```
S2(config)# interface range f0/23 - 24
S2(config-if-range)# shutdown
S2(config-if-range)# channel-group 3 mode passive
S2(config-if-range)# no shutdown
S2(config-if-range)# interface port-channel 3
S2(config-if)# switchport mode trunk
```

