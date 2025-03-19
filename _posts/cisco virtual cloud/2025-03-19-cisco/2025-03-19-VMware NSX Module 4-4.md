---
title: <h0>VMware NSX Module 4-4 NSX Lesson 4 Logical Switching Packet Forwarding </h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-19-cisco]
tags: [Network, Cloud, NSX]










---



# Lesson 4 Logical Switching Packet Forwarding

------

**NSX 패킷 포워딩, BUM 트래픽 관리, ARP 억제 개념 정리**



------



**1. 패킷 포워딩을 위한 테이블**



NSX는 패킷을 효율적으로 전달하기 위해 여러 개의 테이블을 활용함.



------



**2. BUM (Broadcast, Unknown Unicast, Multicast) 트래픽 관리**



BUM 트래픽을 효과적으로 제어해 네트워크 성능 저하 방지.



------



**3. ARP 억제 (ARP Suppression)**



ARP 트래픽을 줄이고 성능을 최적화.





------

------



## **TEP Table Update(TEP 테이블 업데이트) (1)**

![image-20250319104500821](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319104500821.png)

------

### 1단계: 초기 상태 (TEP 정보 없음)

* **각 ESXi 호스트(ESXi-A, ESXi-B, ESXi-C)에 VM이 배포되어 있고, VNI(Virtual Network Identifier) 73728을 통해같은 논리 네트워크에 속함**
* 1단계에서는 ESXi 호스트들 간에 **VTEP 정보가 공유되지 않은 상태**
* 따라서, 서로 다른 호스의 VM들이 Overlay 네트워크에서 직접 통신할 수 없다.

------

------

## TEP Table Update (2)

![image-20250319104807685](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319104807685.png)

### **2단계: CCP와 Management Network를 통한 초기 TEP 정보 요청**

* **CCP가 각 ESXi 호스트들의 TEP Table 정보를 업데이트하기 위해 TEP 정보를 요청한다.**
* **각 ESXi 호스트들은 자신의 TEP IP 정보를 관리 네트워크를 통해 CCP로 전송**
* 이 단계에서 **CCP는  각 ESXi 호스트에서 사용중인 TEP IP를 수집하여 TEP 테이블을 구축한다.**

------

------

## TEP Table Update (3)

![image-20250319104823713](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319104823713.png)

### 3단계: CCP에 TEP Table이 등록됨

* **CCP는 수집한 TEP 정보를 정리하여 TEP Table을 생성한다.**
* **이제 각 ESXI 호스트(ESXi-A,ESXi-B,ESXi-C)에 해당 TEP Table 을 전송할 준비가 된다.**

```apl
VNI 73728 -> TEP 10.20.10.11, 10.20.10.12, 10.20.10.13
```

**3개의 (Tep ip) 는 VNI 73728 에 매핑 됐고, 이러한 매핑 정보는 Overlay 터널을 통해 VM 간의 통신을 가능하게 한다.**

------

------

## TEP Table Update (4)

![image-20250319104846647](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319104846647.png)

### 4단계: 각 ESXi 호스트(하이퍼바이저)에 TEP Table을 업데이트

* CCP는 TEP Table을 각 **ESXi 호스트로 배포하여 Overlay 네트워크에서 VM 간 통신이 가능**하도록 한다.
* 각 **ESXi 호스트는 최신 TEP 정보를 반영**하고, 서로 다른 호스트에 있는 VM과의 통신을 설정한다.
* **VM1(ESXi-A) → VM2(ESXi-B) → VM3(ESXi-C) 간 VXLAN 터널을 통해 Overlay 네트워크가 동작할 수 있다.**

------

------

## FLOW 요약

 	1.	**초기 상태** – TEP 정보 없음, Overlay 네트워크 미완성
 	2.	**CCP가 TEP 정보를 수집** – ESXi 호스트에서 TEP 정보를 받아옴
 	3.	**CCP가 TEP Table 구축** – 모든 TEP 정보를 정리하여 테이블 생성
 	4.	**TEP Table 업데이트** – 각 ESXi 호스트에 배포하여 VM 간 VXLAN 터널 형성

------

------

## MAC Table Update (1)

**전원이 켜진 VM이 세그먼트에 연결될 때:**

**VM의 MAC-TEP IP 매핑이 로컬 MAC 테이블의 Transport node에 등록됩니다.**

![image-20250319104932813](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319104932813.png)

### **1단계: VM이 세그먼트에 연결됨 (로컬 MAC 테이블 등록)**

* **전원이 켜진 VM이 세그먼트에 연결될 때**, 해당 **VM의 MAC-TEP IP 매핑이 로컬 MAC 테이블의 Transport Node에 등록된다.**
* 각 Transport Node는 자신에게 **직접 연결된 VM의 MAC 주소만 알고 있는 상태**이다.

| **Transport Node** | **로컬 MAC 주소** | **TEP IP**  |
| ------------------ | ----------------- | ----------- |
| ESXi-A             | MAC 1             | 10.20.10.11 |
| ESXi-B             | MAC 2             | 10.20.10.12 |
| ESXi-C             | MAC 3             | 10.20.10.13 |

------

------

## MAC Table Update (2)

**각 Transport node는 학습된 VM MAC-TEP IP 매핑 정보를 CCP에 업데이트합니다.**

![image-20250319105108869](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319105108869.png)

### **2단계: 각 Transport Node가 CCP에 MAC 정보 업데이트**

* **각 Transport Node는 학습된 VM MAC-TEP IP 매핑 정보를 CCP에 업데이트한다.**
* 이 과정은 **Management Network(관리 네트워크)를 통해 수행**되며, CCP는 모든 노드에서 수집한 정보를 기반으로 **MAC 테이블을 통합 관리**한다.
* **각 Transport Node는 여전히 자신이 직접 연결된 VM의 MAC 주소만 알고 있으며, 다른 Transport Node의 정보를 모른다.**

| **보고하는 Transport Node** | **MAC 주소** | **TEP IP**  | **보고 대상** |
| --------------------------- | ------------ | ----------- | ------------- |
| ESXi-A                      | MAC 1        | 10.20.10.11 | CCP           |
| ESXi-B                      | MAC 2        | 10.20.10.12 | CCP           |
| ESXi-C                      | MAC 3        | 10.20.10.13 | CCP           |

------

------

## MAC Table Update (3)

**CCP는 VM MAC-TEP IP 매핑의 통합된 항목을 유지합니다.**

![image-20250319105825601](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319105825601.png)

### **3단계: CCP가 MAC-TEP IP 매핑 정보를 통합 관리**

* **CCP는 모든 Transport Node에서 보고된 VM MAC-TEP IP 매핑 정보를 유지 및 통합 관리한다.**
* 이 MAC 테이블은 VXLAN 기반 Overlay 네트워크에서 **VM 간의 L2 통신을 지원하는 중요한 데이터베이스 역할**을 한다.



**CCP MAC 테이블**

| **MAC 주소** | **TEP IP**  |
| ------------ | ----------- |
| MAC 1        | 10.20.10.11 |
| MAC 2        | 10.20.10.12 |
| MAC 3        | 10.20.10.13 |

------

------

## MAC Table Update (4)

**CCP는 업데이트된 MAC 테이블을 VNI가 구현된 모든 Transport node에 전송합니다.**

![image-20250319123219276](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319123219276.png)

### **4단계: CCP가 업데이트된 MAC 테이블을 모든 Transport Node에 전송**

* **CCP는 업데이트된 MAC 테이블을 VNI가 구현된 모든 Transport Node에 전송한다.**
* **각 Transport Node는 전체 MAC 테이블을 수신한 후 이를 반영하여 VXLAN 기반 Overlay 네트워크에서 목적지 MAC을 학습하고 최적의 경로를 설정한다.**
* 이제 **각 Transport Node는 전체 네트워크의 MAC 정보를 갖게 되며**, VXLAN을 통한 Overlay 통신이 가능해진다.



**모든 Transport Node에 적용된 MAC Table**

| **MAC 주소** | **TEP IP**  |
| ------------ | ----------- |
| MAC 1        | 10.20.10.11 |
| MAC 2        | 10.20.10.12 |
| MAC 3        | 10.20.10.13 |

------

## **MAC Table Update 결과 및 최종 상태**

이제 모든 Transport Node는 Overlay 네트워크에서 **모든 VM의 MAC 주소와 TEP IP 정보를 공유**하게 되었다.

이를 통해 **VM 간의 L2 통신이 가능**해진다.

​	•	**VM1(ESXi-A) → VM2(ESXi-B)** VXLAN 터널을 통해 패킷 전달 가능

​	•	**VM2(ESXi-B) → VM3(ESXi-C)** 패킷 전달 가능

​	•	**NSX-T의 Overlay 네트워크가 정상적으로 동작**

------

## ** MAC Table update FLOW 요약**

1. **전원이 켜진 VM이 세그먼트에 연결됨** → Transport Node의 로컬 MAC 테이블에 등록됨.
2. **각 Transport Node가 CCP로 MAC 정보 보고** → 학습된 VM MAC-TEP IP 매핑 정보를 CCP에 업데이트.
3. **CCP가 MAC Table을 유지 및 통합 관리** → 모든 Transport Node의 MAC-TEP IP 정보를 통합 관리.
4. **CCP가 업데이트된 MAC Table을 모든 Transport Node에 배포** → 모든 노드가 최신 MAC 정보를 반영하여 Overlay 네트워크 내에서 L2 통신 가능.



이 과정을 통해 **NSX-T의 VXLAN 기반 Overlay 네트워크에서 VM 간 L2 통신이 정상적으로 이루어질 수 있다.**

------



## About the ARP Table

* ARP 억제(ARP Suppression) 기술은 세그먼트 내의 ARP 플러딩을 감소시킵니다.
* NSX는 **CCP에서 유지하는 ARP 테이블**을 활용하여 ARP 억제를 수행합니다.
* 전송 노드는 **ARP 및 DHCP 트래픽을 감시(Snooping)하여 MAC-IP 연관 정보를 학습**합니다.
* 매핑 정보는 VM이 통신을 시작(트래픽 송수신)할 때마다 기록됩니다.
* 학습된 정보는 각 전송 노드에서 **제어 평면(Control Plane)** 으로 전송됩니다.

------

**ARP Table Update 흐름도**



ARP Table Update 과정은 **NSX-T의 Transport Node가 VM의 IP-MAC 매핑 정보를 학습하고 이를 CCP(Controller Cluster)와 동기화하는 과정**이다.

이 과정이 완료되면, Overlay 네트워크의 모든 Transport Node가 **전체 ARP 테이블을 공유**하여 VM 간 L2/L3 통신이 원활해진다.

------

------

## ARP Table Update (1)

**각 전송 노드는 로컬 테이블에 VM의 IP-MAC 매핑 정보를 기록합니다.**

![image-20250319110120333](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319110120333.png)

------

------

## ARP Table Update (2)

**각 전송 노드는 학습된 VM IP-MAC 매핑 정보를 CCP에 전송합니다.**

![image-20250319110137468](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319110137468.png)

------

------

## ARP Table Update (3)

**CCP는 전송 노드에서 받은 VM IP-MAC 매핑 정보를 기반으로 ARP 테이블을 업데이트합니다.**

![image-20250319110154983](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319110154983.png)

------

------

## ARP Table Update (4)

**CCP는 업데이트된 ARP 테이블을 모든 전송 노드에 전송합니다.**

![image-20250319110214840](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319110214840.png)

------

------

## Unicast Packet Forwarding Across Hosts (1)

![image-20250319110551984](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319110551984.png)

------

------

## Unicast Packet Forwarding Across Hosts (2)

![image-20250319110610779](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319110610779.png)

------

------

## Unicast Packet Forwarding Across Hosts (3)

![image-20250319110630198](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319110630198.png)

------

------

## Unicast Packet Forwarding Across Hosts (4)

![image-20250319110711937](/assets/cisco_post_img/2025-03-19-VMware NSX Module 4-4: Lesson 4 Logical Switching Packet Forwarding//image-20250319110711937.png)

​	

------

------

물리 네트워크 가상네트워크의 차이 -  컨트롤 플레인(예시: arp)과 데이터 플레인(예시: 핑 트래픽) 으로 나누어짐
