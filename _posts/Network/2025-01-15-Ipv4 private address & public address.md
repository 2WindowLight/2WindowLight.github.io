---
title: <h0>Ipv4 private address & public address[Network]</h0>
author: cotes   
categories: [Network]
tags: [Network]

---
 
I**MAC 주소와 IP 주소의 역할 차이**

**MAC 주소와 IP 주소의 역할 차이**

## **IPv4 Private 주소**

**Private 주소**는 사설 네트워크(내부 네트워크)에서 사용되는 주소로, 인터넷에서는 직접 사용되지 않으며, 라우터를 통해 공인 IP로 변환된 뒤 인터넷에 접속한다.

private 주소 범위는 다음과 같다.

![image-20250115184829619](/Users/changhee/Documents/GitHub/2WindowLight.github.io/_posts/images/2025-01-15-Ipv4 private address & public address/image-20250115184829619.png)

![image-20250115184951271](/Users/changhee/Documents/GitHub/2WindowLight.github.io/_posts/images/2025-01-15-Ipv4 private address & public address/image-20250115184951271.png)

내부 네트워크에서만 사용되며, 인터넷에서는 라우팅되지 않고, NAT(Network Address Translation)을 통해 Public 주소로 변환하여 인터넷에 접속 한다.

이를 통해 보안이 강화된 환경을 제공하며, IP 주소 부족 문제를 해결하게 된다.

------

**NAT(Network Address Translation)**은 **사설 IP 주소를 공인 IP 주소로 변환**하여, **내부 네트워크 장치들이 인터넷과 통신할 수 있도록** 해주는 기술이다.

------

## **Pv4 Public 주소**



**Public 주소**는 인터넷에 직접 연결되는 장치에 할당되는 주소로, 세계적으로 고유하며, 인터넷 서비스 제공업체(ISP)가 관리하고 할당한다.

------
  
**Public 주소 범위**

Private 주소 범위를 제외한 모든 IPv4 주소가 Public 주소이다. 예를 들어 8.8.8.8 (Google DNS), 52.14.64.0 (AWS 서버) 기 있다.

**특징**으로는 인터넷에서 고유한 주소로, 전 세계 장치와 직접 통신 가능하며, 라우터와 스위치가 Public 주소를 기반으로 데이터 전송한다.

또한 주소가 제한적이어서 IPv4 고갈 문제가 발생할 수 있다.

------

**Private 주소와 Public 주소 비교**

![image-20250115185622246](/Users/changhee/Documents/GitHub/2WindowLight.github.io/_posts/images/2025-01-15-Ipv4 private address & public address/image-20250115185622246.png)
