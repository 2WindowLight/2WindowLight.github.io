---
title: <h0>BGP Community analize</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-21-cisco]
tags: [Network, BGP]












---

# BGP Community 정리



아래는 R3에서 BGP 커뮤니티를 설정하는 전체 과정을 아주 세세하게 설명한 내용이다. 각 단계별로 명령어의 기능, 구성 구조, 정책 적용 과정 및 최종 결과 분석까지 상세하게 다루었다.

**1. BGP 커뮤니티의 기본 개념**

**BGP 커뮤니티**는 BGP 경로에 선택적 속성(optional attribute)으로 추가되는 태그이다.

**목적:**

​	•	경로를 그룹화하거나 분류하여, 피어에서 일관된 정책(예: 필터링, 트래픽 엔지니어링)을 적용할 수 있다.

​	•	예를 들어, **no-export** 커뮤니티는 해당 경로를 외부 AS로 광고하지 말라는 의미이며, **no-advertise**는 모든 피어에게 경로를 전파하지 않도록 한다.

**사용자 정의 커뮤니티:**

​	•	예: “3:16”, “3:17” 등의 값은 네트워크별로 서로 다른 정책을 적용하는 데 사용된다.

**전파:**

​	•	기본적으로 BGP는 커뮤니티 값을 전달하지 않으므로, 피어링 시 **send-community** 명령어를 통해 커뮤니티 속성을 포함시켜야 한다.

------

**2. 구성 구조 및 네트워크 환경**



**2.1 라우터 및 AS 배치**

​	**R1 (AS 12)**

​	**Loopback5:** 1.1.1.1/24

​	**Interface:** e0/0 (1.1.12.1/24), e0/1 (1.1.13.1/24)

​	**R2 (AS 12)**

​	**Loopback5:** 1.1.2.2/24

​	**Interface:** e0/0 (1.1.12.2/24), e0/2 (1.1.24.2/24)

​	**R3 (AS 3)**

​	**Loopback5:** 1.1.3.3/24

​	**Interface:** e0/1 (1.1.13.3/24)

​	**R4 (AS 4)**

​	**Loopback5:** 1.1.4.4/24

​	**Interface:** e0/2 (1.1.24.4/24)



------

**2.2 IGP (OSPF) 및 BGP 피어링**

​	**IGP 구성 (OSPF):**

​	각 라우터는 Loopback5와 연결 인터페이스를 OSPF Area 0에 포함하여 기본 네트워크 연결을 확보한다.

​	일부 인터페이스는 passive로 설정하여 불필요한 OSPF 헬로우 패킷 전송을 차단한다.

​	**BGP 피어링:**

​	R1은 AS 12 내부의 R2와, AS 3에 속하는 R3와 피어링을 구성한다.

​	R2는 R1과 내부 피어링을 구성하고, AS 4에 속하는 R4와도 피어링한다.

​	R3는 AS 3의 피어로서 R1과 피어링하며, R4는 AS 4의 피어로서 R2와 피어링한다.

------

**3. R3에서 커뮤니티 설정을 위한 전체 설정 과정**

R3는 기본 BGP 설정 외에 추가로 광고할 네트워크에 대해 커뮤니티 값을 부여하는 정책을 구현한다.

------

**3.1 추가 네트워크 구성 (Loopback16 생성)**

R3는 추가 네트워크를 광고하기 위해 Loopback16 인터페이스를 생성하고 여러 IP 주소를 할당한다.

```bash
R3(config)# int lo16
R3(config-if)# ip add 1.1.16.1 255.255.255.0
R3(config-if)# ip add 1.1.17.1 255.255.255.0 sec
R3(config-if)# ip add 1.1.18.1 255.255.255.0 sec
R3(config-if)# ip add 1.1.19.1 255.255.255.0 sec
R3(config-if)# exit
```

​	**기능 및 목적:**

​	•	Loopback16 인터페이스를 활성화하여 1.1.16.0/24, 1.1.17.0/24, 1.1.18.0/24, 1.1.19.0/24 네트워크를 구성한다.

​	•	이 네트워크들은 이후 BGP에 광고되며, 각 네트워크마다 서로 다른 커뮤니티 값이 부여되어 정책에 따라 분류된다.



------

**3.2 BGP 네트워크 광고**

```bash
R3(config)# router bgp 3
R3(config-router)# net 1.1.16.0 mask 255.255.255.0
R3(config-router)# net 1.1.17.0 mask 255.255.255.0
R3(config-router)# net 1.1.18.0 mask 255.255.255.0
R3(config-router)# net 1.1.19.0 mask 255.255.255.0
R3(config-router)# exit
```

​	•	**기능 및 목적:**

​	•	구성한 네 개의 네트워크를 BGP 프로세스에 광고하여, 외부에 해당 네트워크가 존재함을 알린다.



------

**3.3 Prefix-list 생성**

```bash
R3(config)# ip prefix-list List16 permit 1.1.16.0/24
R3(config)# ip prefix-list List17 permit 1.1.17.0/24
R3(config)# ip prefix-list List18 permit 1.1.18.0/24
R3(config)# ip prefix-list List19 permit 1.1.19.0/24
```

**기능 및 목적:**

​	•	각 네트워크를 식별하기 위한 목록을 생성한다.

​	•	List16은 오직 1.1.16.0/24만 허용하며, 나머지 리스트도 각각 해당 네트워크를 지정한다.

​	•	이 리스트들은 후에 route-map에서 match 조건으로 사용되어, 해당 네트워크에 정책을 적용하는 기준이 된다.



------

**3.4 BGP New-Format 활성화**

```bash
R3(config)# ip bgp new-format
```

​	•	**기능 및 목적:**

​	•	BGP 커뮤니티 출력 형식을 최신 형식으로 전환하여, 확장 커뮤니티나 다중 커뮤니티 값을 명확하게 확인할 수 있도록 한다.



------

**3.5 커뮤니티 설정을 위한 Route-map 구성 (Map1)**



R3는 네트워크별로 서로 다른 커뮤니티 값을 적용하기 위해 route-map을 여러 시퀀스로 구성한다.



------

**3.5.1 시퀀스 10 – 1.1.16.0/24에 대해 커뮤니티 3:16 설정**

```bash
R3(config)# route-map Map1 10
R3(config-route-map)# match ip address prefix-list List16
R3(config-route-map)# set community 3:16
R3(config-route-map)# exit
```

**기능 및 세부 설명:**

**route-map Map1 10**:

​		“Map1”이라는 정책의 첫 번째 시퀀스(번호 10)를 시작한다.

​		낮은 시퀀스 번호부터 평가되며, 이 시퀀스가 먼저 적용된다.

**match ip address prefix-list List16**:

​		BGP 업데이트의 목적지(prefix)가 List16에 정의된 1.1.16.0/24와 일치하는지 검사한다.

​		조건에 일치하는 경우에만 후속 동작이 실행된다.

**set community 3:16**:

​		match 조건에 맞는 경로의 커뮤니티 값을 “3:16”으로 설정한다.

​		이 값은 후속 정책이나 필터링에 사용할 수 있는 태그 역할을 한다.



------

**3.5.2 시퀀스 20 – 1.1.17.0/24에 대해 커뮤니티 “internet 3:16 3:17” 설정**

```bash
R3(config)# route-map Map1 20
R3(config-route-map)# match ip address prefix-list List17
R3(config-route-map)# set community internet 3:16 3:17
R3(config-route-map)# exit
```

**기능 및 세부 설명:**

**match ip address prefix-list List17**:

​	•	1.1.17.0/24 네트워크가 List17과 일치하는지 확인한다.

**set community internet 3:16 3:17**:

​	•	해당 경로에 “internet”이라는 well-known 커뮤니티와 함께 “3:16”, “3:17” 값을 부여한다.

​	•	“internet” 커뮤니티는 전 세계적으로 경로를 광고하라는 의미로 사용될 수 있으며, 추가 커뮤니티 값은 사용자 정의 정책에 활용된다.



------

**3.5.3 시퀀스 30 – 1.1.18.0/24에 대해 커뮤니티 “no-advertise 3:18” 설정**

```bash
R3(config)# route-map Map1 30
R3(config-route-map)# match ip address prefix-list List18
R3(config-route-map)# set community no-advertise 3:18
R3(config-route-map)# exit
```

​	•	**기능 및 세부 설명:**

​	•	**match ip address prefix-list List18**:

​	•	1.1.18.0/24 네트워크가 List18과 일치하는지 검사한다.

​	•	**set community no-advertise 3:18**:

​	•	해당 경로에 “no-advertise” 커뮤니티와 “3:18” 값을 설정한다.

​	•	“no-advertise”는 경로가 어떤 피어에도 광고되지 않도록 하는 정책이다.



------

**3.5.4 시퀀스 40 – 1.1.19.0/24에 대해 커뮤니티 “no-export” 설정**

```bash
R3(config)# route-map Map1 40
R3(config-route-map)# match ip address prefix-list List19
R3(config-route-map)# set community no-export
R3(config-route-map)# exit
```

​	•	**기능 및 세부 설명:**

​	•	**match ip address prefix-list List19**:

​	•	1.1.19.0/24 네트워크가 List19와 일치하는지 검사한다.

​	•	**set community no-export**:

​	•	해당 경로에 “no-export” 커뮤니티를 설정한다.

​	•	“no-export”는 경로가 외부 AS로 광고되지 않도록 제한하는 역할을 한다.



------

**3.5.5 시퀀스 50 – Route-map 종료**

```bash
R3(config)# route-map Map1 50
R3(config-route-map)# exit
```

​	•	**기능 및 설명:**

​	•	추가 동작 없이 route-map 구성을 마무리한다.



------

**3.6 Route-map 적용 및 커뮤니티 전송**

```bash
R3(config)# router bgp 3
R3(config-router)# nei 1.1.13.1 route-map Map1 out
R3(config-router)# nei 1.1.13.1 send-community
```

**기능 및 세부 설명:**

​	**nei 1.1.13.1 route-map Map1 out**:

​	•	R3는 피어 R1(주소 1.1.13.1)으로 내보내는 BGP 업데이트에 대해 Map1 정책을 적용한다.

​	•	이로 인해, 앞서 설정한 각 시퀀스의 커뮤니티 값이 해당 경로에 추가된다.

​	**nei 1.1.13.1 send-community**:

​	•	R3가 BGP 업데이트를 보낼 때 커뮤니티 속성을 포함하도록 설정한다.

​	•	만약 이 명령어가 설정되지 않으면, 기본적으로 커뮤니티 속성이 피어에게 전송되지 않는다.

------

**4. 결과 분석**



**4.1 R1의 BGP 테이블 및 커뮤니티 확인**

​	**전체 경로 및 커뮤니티 출력:**

​	•	R1에서 sh ip bgp comm 명령어를 실행하면, R3로부터 수신한 업데이트에 포함된 커뮤니티 값을 확인할 수 있다.

​	•	출력 예시에서는 1.1.16.0/24, 1.1.17.0/24, 1.1.18.0/24, 1.1.19.0/24 네트워크와 함께 각각 “3:16”, “internet 3:16 3:17”, “no-advertise 3:18”, “no-export” 커뮤니티가 표시된다.



------

**4.2 특정 커뮤니티 필터링 및 확인**

​	**커뮤니티 3:16 확인:**

​	•	sh ip bgp commu 3:16 명령어를 사용하면, 커뮤니티 값 3:16을 포함하는 경로가 필터링되어 표시된다.

​	•	sh ip bgp commu 3:16 exact-match 명령어를 통해 정확히 3:16만 설정된 경로(예, 1.1.16.0/24)를 확인할 수 있다.

​	•	**개별 경로 확인:**

​	•	sh ip bgp 1.1.17.0 명령어를 실행하면, 1.1.17.0/24 경로의 BGP 업데이트에 대해 “Community: internet 3:16 3:17”이 표시되어, 시퀀스 20에서 설정한 정책이 제대로 적용되었음을 확인할 수 있다.



------

**4.3 정책의 효과**

​	•	R3에서 구성한 route-map Map1은 각 네트워크별로 구분된 커뮤니티 값을 부여하여, 나중에 다른 라우터(R1 등)가 이 값을 기반으로 정책적으로 경로를 분류하거나 필터링할 수 있도록 한다.

​	•	send-community 설정 덕분에, R1은 R3가 전송한 BGP 업데이트에서 커뮤니티 속성을 그대로 수신하며, 이를 통해 AS 간 또는 AS 내에서 세밀한 트래픽 엔지니어링 및 정책 적용이 가능해진다.

------

**5. 결론**



R3에서는 다음과 같은 순서로 BGP 커뮤니티 설정을 구현하였다.

​	1.	**추가 네트워크 구성:**

​			Loopback16을 사용하여 1.1.16.0/24, 1.1.17.0/24, 1.1.18.0/24, 1.1.19.0/24 네트워크를 생성하였다.

​	2.	**BGP 네트워크 광고:**

​			위 네트워크들을 BGP에 등록하여 경로로 광고하였다.

​	3.	**Prefix-list 생성:**

​			List16 ~ List19를 생성하여 각 네트워크를 식별할 수 있도록 하였다.

​	4.	**BGP New-Format 활성화:**

​			ip bgp new-format 명령어를 사용하여 최신 커뮤니티 형식을 적용하였다.

​	5.	**Route-map Map1 구성:**

​			시퀀스 10에서 1.1.16.0/24에 대해 커뮤니티 3:16,

​			시퀀스 20에서 1.1.17.0/24에 대해 “internet 3:16 3:17”,

​			시퀀스 30에서 1.1.18.0/24에 대해 “no-advertise 3:18”,

​			시퀀스 40에서 1.1.19.0/24에 대해 “no-export”를 설정하였다.

​	6.	**정책 적용:**

​			R3는 route-map Map1을 R1과의 BGP 피어링에 out 방향으로 적용하고, send-community를 활성화하여 커뮤니티 속성을 포함시켰다.

​	7.	**결과 확인:**

​			R1의 BGP 테이블 및 커뮤니티 관련 출력 명령어를 통해 각 네트워크의 커뮤니티 값이 정상적으로 반영된 것을 확인할 수 있으며, 이를 통해 AS 간 또는 AS 내에서 세밀한 경로 정책을 구현할 수 있다.



