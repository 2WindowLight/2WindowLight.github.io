---
title: <h0> CLI EtherChannel</h0>
author: cotes   
categories: [cisco virtual cloud,2025-02-03-cisco]
tags: [Network]







---

## 논리적 채널(Port-Channel)로 묶기 위해 사용하는 **EtherChannel** 설정 명령어



```bash
Switch(config-if-range)# channel-group 3 mode <옵션>
```

여기서 **3**은 포트 채널 번호(그룹 번호)를 의미하며, **mode** 옵션은 EtherChannel을 구성할 때 어떤 방식이나 프로토콜을 사용할지 지정한다. 각 모드의 의미는 다음과 같다. 

------

```bash
	active     Enable LACP unconditionally
  auto       Enable PAgP only if a PAgP device is detected
  desirable  Enable PAgP unconditionally
  on         Enable Etherchannel only
  passive    Enable LACP only if a LACP device is detected
```

------

## **1. active**

**LACP (Link Aggregation Control Protocol)** 를 항상 활성화하여 EtherChannel을 하나로 묶는 결합 시도

​	**특징:**

​	•	스위치가 주기적으로 LACP 패킷을 전송합니다.

​	•	상대방 포트가 **active** 또는 **passive** 모드로 LACP를 지원하면 채널이 형성됩니다.

​	•	**사용 시점:**

​	•	IEEE 표준 LACP를 사용하여 자동으로 링크를 집계하고자 할 때 사용합니다.



#### 한쪽은 active여야 하며, 다른 한쪽은 active 또는 passive여야 함

------

## **2. passive**

**LACP 모드 중 하나로, 상대방이 LACP 패킷을 보낼 때만 응답**

​	**특징:**

​	•	스위치는 LACP 패킷을 주기적으로 보내지 않고, 상대방으로부터 LACP 패킷을 수신할 때만 채널 형성에 참여합니다.

​	•	상대방이 **active** 모드인 경우에만 EtherChannel이 형성됩니다.

​	•	**사용 시점:**

​	•	LACP를 사용하지만, 네트워크 환경에서 상대방이 이미 LACP를 **active** 모드로 설정되어 있을 때 사용합니다.

#### 반드시 상대방이 active여야 함

------

## **3. desirable**

**PAgP (Port Aggregation Protocol)** 를 항상 활성화하여 EtherChannel을 하나로 묶는 결합을 시도합니다.

​	**특징:**

​	•	스위치가 PAgP 패킷을 주기적으로 전송합니다.

​	•	상대방 포트가 PAgP를 지원하면, 자동으로 EtherChannel이 형성됩니다.

​	•	**사용 시점:**

​	•	Cisco의 독점 프로토콜인 PAgP를 사용하여 자동으로 링크 집계를 구성할 때 사용합니다.

#### 한쪽은 desirable여야 하며, 다른 한쪽은 desirable 또는 auto여야 함

------

## **4. auto**

**PAgP 모드 중 하나로, 상대방이 PAgP 패킷을 보낼 때만 채널 형성에 참여합니다.**

​	**특징:**

​	•	스위치는 PAgP 패킷을 주기적으로 전송하지 않습니다.

​	•	상대방이 **desirable** 모드와 같이 PAgP 패킷을 전송하는 경우에만 EtherChannel이 형성됩니다.

​	•	**사용 시점:**

​	•	PAgP를 사용하지만, 상대방이 이미 활성화되어 있을 때, 또는 자동 협상에만 참여하도록 할 때 사용합니다.

#### 반드시 상대방이 desirable여야 함

------

## **5. on**

**협상 프로토콜(PAgP 또는 LACP)을 사용하지 않고, 단순히 포트를 강제로 EtherChannel에 포함시킵니다.**

​	**특징:**

​	•	어떤 프로토콜도 사용하지 않고, 수동(static)으로 포트들을 묶습니다.

​	•	상대방 쪽에서도 동일하게 **on** 모드로 설정되어 있어야 채널이 정상적으로 형성됩니다.

​	•	프로토콜 결합이 없으므로, 설정이 맞지 않으면 채널이 제대로 형성되지 않을 수 있습니다.

​	•	**사용 시점:**

​	•	협상 과정을 생략하고, 단순히 포트를 그룹으로 묶고자 할 때 사용합니다.

​	•	양쪽 장비 모두 수동으로 구성되어 있는 경우 사용합니다.

##### 양쪽 모두 협상 없이 수동으로 on 모드로 설정되어야 함

------



**LACP를 사용할 경우:**

​	•	**active:** 스위치가 주도적으로 LACP 패킷을 전송

​	•	**passive:** 스위치는 수동적으로 상대방의 LACP 패킷에만 응답

**PAgP를 사용할 경우 (Cisco 전용):**

​	•	**desirable:** 스위치가 주도적으로 PAgP 패킷을 전송

​	•	**auto:** 스위치는 수동적으로 상대방의 PAgP 패킷에만 응답

​	•	**협상 프로토콜 없이 단순 묶기:**

​	•	**on:** 어떤 프로토콜도 사용하지 않고 포트를 강제로 EtherChannel에 포함



각 모드를 선택할 때는 네트워크의 환경과 상대방 장비의 설정이 일치하도록 주의해야 합니다. 예를 들어, LACP를 사용하려면 양쪽 모두 **active/passive** 모드여야 하고, PAgP를 사용할 경우에도 양쪽의 모드가 서로 호환되어야 EtherChannel이 올바르게 형성됩니다.