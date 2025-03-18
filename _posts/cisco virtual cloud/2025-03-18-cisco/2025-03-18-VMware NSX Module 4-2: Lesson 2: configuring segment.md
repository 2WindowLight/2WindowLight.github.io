---
title: <h0>VMware NSX Module 4-2 Lesson 2 configuring segment</h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-18-cisco]
tags: [Network, Cloud, NSX]










---

## Creating Segments

![image-20250318155229553](/assets/cisco_post_img/2025-03-18-VMware NSX Module 4-2: Lesson 2: configuring segment//image-20250318155229553.png)

------

------

## **Creating Segments Workflow(세그먼트 생성 워크플로우)**

![image-20250318155624572](/assets/cisco_post_img/2025-03-18-VMware NSX Module 4-2: Lesson 2: configuring segment//image-20250318155624572.png)





세그먼트는 **NSX UI** 를 통해 생성되며, 내부적으로 여러 단계를 거쳐 데이터 패스(Data Path)에 적용된다.



------



**세그먼트 생성 과정**

​	1.	**사용자(User)가 NSX UI를 통해 세그먼트를 생성**.

​	2.	**Policy 역할** 이 세그먼트 구성을 **Manager 역할** 에 전달.

​	3.	**NSX Manager** 가 세그먼트 정보를 **Corfu 데이터베이스** 에 논리적 스위치(Logical Switch)로 저장.

​	4.	**Manager 역할** 이 세그먼트 정보를 **CCP(Central Control Plane)** 에 전달.

​	5.	**CCP** 가 해당 정보를 **APH(Appliance Proxy Hub)** 로 전송.

​	6.	**APH 서비스** 가 **로컬 컨트롤 플레인(nsx-proxy)** 에 **포트 1235** 를 통해 스위칭 설정 전달.

​	7.	**nsx-proxy 에이전트** 가 **nsxt-vdl2 커널 모듈** 에 스위칭 설정을 전달하여 데이터 패스에서 세그먼트를 생성 및 구성.



------



**그림 설명**



해당 그림은 **세그먼트 생성이 ESXi 호스트에 적용되는 과정** 을 나타낸다.

​	•	**사용자(User)** 가 NSX UI에서 세그먼트를 생성.

​	•	**Policy, Manager, CCP** 가 역할을 수행하며, 세그먼트 정보를 **APH(Appliance Proxy Hub)** 로 전달.

​	•	**APH** 가 **NSX-Proxy** 를 통해 **nsxt-vdl2 커널 모듈** 에 설정을 적용.



------



**결론**

​	•	**NSX의 논리적 스위칭 구성은 여러 단계의 컨트롤 플레인과 데이터 플레인을 거쳐 반영됨**.

​	•	**세그먼트 설정이 최종적으로 nsxt-vdl2 커널 모듈에 적용되어 데이터 패스에서 활성화됨**.



------

------

## Viewing Configured Segments

![image-20250318155921892](/assets/cisco_post_img/2025-03-18-VMware NSX Module 4-2: Lesson 2: configuring segment//image-20250318155921892.png)

------

------

## Attaching a VM to a Segment

![image-20250318160036214](/assets/cisco_post_img/2025-03-18-VMware NSX Module 4-2: Lesson 2: configuring segment//image-20250318160036214.png)

------

------

## **Workflow: Attaching a VM to a Segment(가상 머신을 세그먼트에 연결하는 워크플로우) (1)**

![image-20250318160301453](/assets/cisco_post_img/2025-03-18-VMware NSX Module 4-2: Lesson 2: configuring segment//image-20250318160301453.png)

**vCenter Server** 에 의해 관리되는 **VM을 NSX 세그먼트(Web-Segment)에 연결하는 과정** 은 다음과 같은 단계를 거친다.



------



**VM을 세그먼트에 연결하는 과정**

​	1.	**사용자(User)가 vCenter Server를 통해 VM을 세그먼트(Web-Segment)에 연결.**

​	2.	**vCenter Server가 요청을 ESXi 호스트로 전송.**

​	3.	**ESXi 호스트가 요청을 관리 플레인(Management Plane, MP)으로 전달.**



------



**그림 설명**

​	•	**사용자(User)** 가 vCenter Server를 통해 VM을 세그먼트(Web-Segment)에 연결.

​	•	**vCenter Server** 가 요청을 **ESXi 호스트** 로 전달.

​	•	**ESXi 호스트** 가 해당 요청을 **MP(Management Plane)** 으로 전송.



------



**결론**

​	•	**VM이 NSX 세그먼트에 연결되기 위해서는 vCenter Server와 NSX의 관리 플레인 간 통신이 필요**.

​	•	**ESXi 호스트가 요청을 처리하여 최종적으로 VM을 NSX 네트워크에 연결**.

------

------

## **Workflow: Attaching a VM to a Segment(가상 머신을 세그먼트에 연결하는 워크플로우) (2)**



**NSX Manager** 가 **VM을 NSX 세그먼트(Web-Segment)에 연결하는 과정** 은 다음과 같이 진행된다.



------



**VM 연결 과정**

​	4.	**NSX Manager가 세그먼트(Web-Segment)에서 논리 인터페이스 1(LIF 1)을 생성하고, 가상 인터페이스(VIF 1)를 연결.**

​	5.	**NSX Manager가 연결된 설정을 CCP(Central Control Plane)로 전달.**

​	6.	**CCP가 요청을 해당 VM이 존재하는 ESXi 호스트로 전송.**

​	7.	**Web-Segment에서 LIF 1이 생성되고, VIF 1이 연결됨.**



------



**그림 설명**

​	•	**NSX Manager** 가 **Web-Segment** 에 **LIF 1** 을 생성하고 **VIF 1** 을 연결.

​	•	**CCP** 가 해당 요청을 받아 **ESXi 호스트** 로 전달.

​	•	**ESXi 호스트** 가 요청을 받아 VM을 세그먼트에 연결.



------



**결론**

​	•	**VM이 NSX 세그먼트에 연결되기 위해서는 NSX Manager가 논리 인터페이스(LIF)와 가상 인터페이스(VIF)를 설정해야 함.**

​	•	**CCP가 연결된 정보를 전달하고, ESXi 호스트가 이를 최종적으로 처리하여 VM을 세그먼트에 연결.**

------

------

## Verifying the Segment Port Status

![image-20250318160803118](/assets/cisco_post_img/2025-03-18-VMware NSX Module 4-2: Lesson 2: configuring segment//image-20250318160803118.png)

------

------

## About Network Topology

![image-20250318160856092](/assets/cisco_post_img/2025-03-18-VMware NSX Module 4-2: Lesson 2: configuring segment//image-20250318160856092.png)

------

------

## Using Network Topology to Validate the Segment Configuration

![image-20250318160956985](/assets/cisco_post_img/2025-03-18-VMware NSX Module 4-2: Lesson 2: configuring segment//image-20250318160956985.png)