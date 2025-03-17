---
title: <h0>VMware NSX Module 3-2 Lesson2 Navigating the NSX UI</h0>
author: cotes 
categories: [cisco virtual cloud,2025-03-17-cisco]
tags: [Network, Cloud, NSX]






---

#  

## NSX Manager Policy and Manager Views

![image-20250317121822932](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-2: Navigating the NSX UI //image-20250317121822932.png)

------

------

## About the Networking Tab

![image-20250317121847923](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-2: Navigating the NSX UI //image-20250317121847923.png)

------

------

## About the Security Tab

![image-20250317122058226](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-2: Navigating the NSX UI //image-20250317122058226.png)

------

------

## About the Inventory Tab

![image-20250317122408103](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-2: Navigating the NSX UI //image-20250317122408103.png)

------

## **NSX의 Plan & Troubleshoot(계획 및 문제 해결) 탭 개요**

![image-20250317122521198](/assets/cisco_post_img/2025-03-17-VMware NSX Module 3-2: Navigating the NSX UI //image-20250317122521198.png)

**Plan & Troubleshoot 탭**은 NSX 환경의 **모니터링 및 문제 해결 도구**를 제공한다.

이 탭에서는 **네트워크 트래픽 분석 및 문제 해결을 위해 다양한 기능을 활용**할 수 있다.



------



**1. 주요 기능**



Plan & Troubleshoot 탭에서 제공하는 **문제 해결 도구**는 다음과 같다.

1. **IPFIX(Internet Protocol Flow Information Export)**
   * **네트워크 흐름 데이터를 수집하고 분석**하여 트래픽 패턴을 모니터링할 수 있음.
   * **NSX 환경 내에서 흐름 기반 네트워크 가시성 제공**.

2. **Port Mirroring(포트 미러링)**
   * 특정 **논리 포트에서 송수신되는 트래픽을 복제**하여 네트워크 분석 도구로 보낼 수 있음.
   * **패킷 캡처 및 트러블슈팅(문제 해결) 목적**으로 활용됨.

3. **Traceflow(트레이스플로우)**
   * **패킷이 NSX 환경에서 전달되는 경로를 시뮬레이션**하여 네트워크 트래픽이 올바르게 전달되는지 확인 가능.
   * **IP 주소, 트래픽 유형(유니캐스트, 멀티캐스트), 프로토콜 유형(ICMP, TCP, UDP 등)을 설정하여 트래픽 흐름 분석**.
   * **Overlay 기반 NSX 환경에서만 사용 가능**.

4. **Consolidated Capacity(통합 용량 분석)**
   * **네트워크 리소스의 사용량을 모니터링**하여 용량을 효율적으로 관리 가능.
   * **가용 리소스 및 네트워크 대역폭 분석 기능 제공**.



------



2. **정책(Policy) 뷰 vs. 관리자(Manager) 뷰 차이점**

   * **정책(Policy) 뷰**
     * **Discover & Plan(발견 및 계획) 기능 포함**.
     * 네트워크 설계 및 보안 정책을 관리하는데 최적화됨.

   * **관리자(Manager) 뷰**
     * **Discover & Plan 기능 없음**.
     * NSX 인프라의 네트워크 모니터링 및 문제 해결에 초점.



------



**3. 결론**

​	•	**Plan & Troubleshoot 탭은 NSX 환경에서 네트워크 트래픽을 모니터링하고 문제를 해결하는 필수 도구**이다.

​	•	**Traceflow, Port Mirroring, IPFIX, Consolidated Capacity 등 다양한 기능을 통해 네트워크 흐름을 분석**할 수 있다.

​	•	**정책(Policy) 뷰에서는 Discover & Plan 기능을 제공하지만, 관리자(Manager) 뷰에서는 제공되지 않는다.**