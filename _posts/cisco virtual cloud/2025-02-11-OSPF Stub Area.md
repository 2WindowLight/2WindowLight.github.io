---
title: <h0>OSPF Stub Area/h0>
author: cotes   
categories: [cisco virtual cloud]
tags: [Network]







---

## OSPF 스텁 에어리어

OSPF 스텁 에어리어에서는 ABR이 내부 라우터에게 외부 경로에 대한 LSA를 차단하고 대신 디폴트 루트를 전달한다.

결과적으로 라우팅 테이블의 크기를 대폭 감소되어 네트워크의 안정성이 향상되고, 라우팅 성능도 좋아지며, 장애처리가 쉬워진다.

스텁 에어리어의 종류

* 스텁 에어리어
* 완전 스텁
* 에어리어 및 NSSA

------

** OSPF Stub Area 종류, 설정 명령어, 차단되는 네트워크**

| **Stub Area 종류**            | **설정 명령어**           | **차단되는 네트워크 (LSA)**                 | **설명**                                                     |
| ----------------------------- | ------------------------- | ------------------------------------------- | ------------------------------------------------------------ |
| **Stub Area**                 | area <ID> stub            | Type 5 (External LSA)                       | - ASBR이 생성한 외부 경로 차단- 대신 **Type 3 Summary LSA**로 기본 경로(Default Route) 제공 |
| **Totally Stubby Area**       | area <ID> stub no-summary | Type 5 (External LSA), Type 3 (Summary LSA) | - ABR이 생성하는 **Type 3 Summary LSA도 차단**- 대신 **기본 경로(Default Route, 0.0.0.0)만 전파** |
| **NSSA (Not-So-Stubby Area)** | area <ID> nssa            | Type 5 (External LSA)                       | - Stub과 유사하지만 ASBR에서 **외부 경로를 Type 7 NSSA External LSA로 허용** |
| **Totally NSSA**              | area <ID> nssa no-summary | Type 5 (External LSA), Type 3 (Summary LSA) | - **NSSA에서 Type 3 Summary LSA도 차단**- 대신 기본 경로(Default Route, 0.0.0.0)만 사용 |

**✅ 차단되는 LSA 요약**

​	•	**Stub** → Type 5 차단 (외부 경로 X, 요약 경로 O)

​	•	**Totally Stubby** → Type 5 + Type 3 차단 (기본 경로 O)

​	•	**NSSA** → Type 5 차단하지만 Type 7 허용 (외부 경로 O)

​	•	**Totally NSSA** → Type 5 + Type 3 차단하고 Type 7 허용 (기본 경로 O)

### 제약사항

- **백본 에어리어가 될 수 없다.**
- **완전 스텁 에어리어**
- **NSSA**
- **NSSA 완전 스텁 에어리어**



 **Stub 영역 설정 시 show ip ospf database로 확인 가능!** 

------

### 완전 스탑 에어리어 설정

```bash
R1
#router ospf 1
#area 1 stub

R2
#router ospf 1
#area 1 stub no-summary

R3
#router ospf 1
#area 1 stub no-summary
```

OSPF에서 **Stub Area**는 **외부 경로(E2)를 차단하고, 내부 경로(O IA)는 허용**합니다.

하지만, **Totally Stubby Area (stub no-summary)**는 내부 경로(O IA)도 차단하고, **기본 경로(Default Route, 0.0.0.0/0)만 제공**합니다.



**왜 no-summary를 사용하는가?**



✔ **라우팅 테이블 크기 줄이기** → OSPF 내부 경로까지 차단하므로 라우팅 테이블이 작아짐

✔ **단순한 라우팅** → 내부 경로를 받지 않고 기본 경로(0.0.0.0/0) 하나만 사용

✔ **CPU & 메모리 절약** → 불필요한 경로가 없으므로 라우터 성능 향상

------

**📌 NSSA vs. NSSA ASBR 차이점**



✅ **NSSA (Not-So-Stubby Area)**: OSPF Stub과 유사하지만 ASBR을 허용하는 영역

✅ **NSSA ASBR (Not-So-Stubby Area ASBR)**: NSSA 내부에서 **외부 경로를 수용하여** OSPF에 광고하는 라우터

**🔹 차이점 요약**

| **구분**                     | **NSSA (영역)**                                              | **NSSA ASBR (라우터)**                                       |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **역할**                     | Type 5 LSA를 차단하지만 Type 7 LSA를 허용하는 OSPF 영역      | NSSA 내부에서 **외부 네트워크를 받아 OSPF로 광고하는 라우터** |
| **Type 5 LSA**               | 차단됨 (OSPF 외부 경로 직접 수용 불가)                       | 생성 불가 (Type 7으로 변환됨)                                |
| **Type 7 LSA**               | 허용됨 (ASBR이 NSSA 내부에서 외부 경로를 OSPF로 배포할 때 사용) | **외부 네트워크를 받아 Type 7 LSA로 변환하여 OSPF에 전달**   |
| **ASBR 필요 여부**           | ASBR이 있을 수도 있고 없을 수도 있음                         | **반드시 ASBR 역할 수행**                                    |
| **ABR 역할**                 | NSSA의 경계를 관리하고 Type 7을 Type 5로 변환 가능           | ABR이 아님 (영역 간 요약 수행 X)                             |
| **기본 경로(Default Route)** | 기본적으로 없음 (default-information-originate 필요)         | 기본 경로 제공 가능 (default-information-originate 설정)     |

**🔹 주요 개념**

​	•	**NSSA** = OSPF Stub 영역과 비슷하지만 ASBR을 허용

​	•	**NSSA ASBR** = NSSA 내부에서 외부 네트워크를 OSPF로 가져오는 라우터

​	•	**NSSA ASBR이 생성한 Type 7 LSA는 ABR을 통해 Type 5로 변환 가능**

**🔹 예제 (NSSA + NSSA ASBR 설정)**



**✅ 결론**

✔ **NSSA는 OSPF 영역 개념**, **NSSA ASBR은 특정 라우터**

✔ **NSSA는 Type 5 LSA를 차단하지만, Type 7 LSA는 허용**

✔ **NSSA ASBR은 OSPF 외부 네트워크를 Type 7 LSA로 변환하여 전파**

✔ **ABR이 Type 7 LSA를 Type 5 LSA로 변환 가능**



 **“NSSA = Stub + ASBR 가능” / “NSSA ASBR = 외부 경로를 Type 7로 광고하는 라우터”** 

------



### 디폴트 루트의 디폴트 코스트 변경하기

```
area 1 default-cost 10
```

------

**📌 OSPF Stub Area에서 차단되는 네트워크 유형 (E2, O IA) 개념 정리**

| **타입**                                | **설명**                                                     | **Stub에서 차단 여부** | **쉽게 말하면**                                              |
| --------------------------------------- | ------------------------------------------------------------ | ---------------------- | ------------------------------------------------------------ |
| **E2 (External Type 2)**                | OSPF 외부에서 유입된 경로 (예: BGP, EIGRP 등). 메트릭이 고정되어 변경되지 않음. | ✅ 차단됨               | **“OSPF 외부에서 들어오는 고정된 경로 (Stub에서 차단)”**     |
| **O IA (OSPF Inter-Area - Type 3 LSA)** | 다른 OSPF 영역(예: Area 0)에서 온 내부 OSPF 경로             | ❌ 차단되지 않음        | **“다른 OSPF 영역에서 넘어온 내부 OSPF 경로 (Stub에서도 허용)”** |



### E2 - OSPF 가 아닌 다른 외부 경로부터 들어온 것 

### O IA  - 같은 OSPF 이지만 , 다른 영역이라서 내부라고 구분



1️⃣ **E2 (External Type 2) 경로란?**

✔ **OSPF 외부(BGP, EIGRP, 정적 라우팅 등)에서 들어온 경로**

✔ **ASBR이 외부 경로를 OSPF로 가져올 때 Type 5 LSA로 생성함**

✔ **Stub에서는 외부 경로(Type 5 LSA)를 차단하므로 E2 경로도 차단됨!**

✔ **따라서 Stub Area 내부에서는 E2 경로를 볼 수 없음**



📌 **Stub이 E2를 차단하는 이유?**

✅ **Stub은 OSPF 내부 경로만 사용하고 싶음**

✅ **외부 경로(E2)를 받아들이면 라우팅 테이블이 커지고 네트워크 부하 증가**

✅ **그래서 Stub은 E2(외부 경로)를 차단하고 기본 경로(default route)만 제공**



2️⃣ **O IA (OSPF Inter-Area - Type 3 LSA) 경로란?**

✔ **다른 OSPF 영역에서 들어온 내부 OSPF 경로**

✔ **예를 들어, Area 1에 있는 네트워크가 Area 0(백본 영역)을 거쳐서 Area 2로 전달됨**

✔ **이때 ABR이 Type 3 LSA를 생성해서 다른 Area에 전달함**

✔ **Stub에서는 O IA 경로(Type 3 LSA)는 차단되지 않음!**



📌 **Stub이 O IA를 허용하는 이유?**

✅ **Stub은 OSPF 내부 경로는 계속 사용할 수 있어야 함**

✅ **Stub이 E2를 차단하지만 O IA(Type 3 LSA)는 차단하지 않음**

✅ **즉, 다른 OSPF 영역에서 넘어온 내부 경로(O IA)는 정상적으로 사용 가능!**

------

### NSSA 설정하기

```bash
R4#router ospf 1
R4#area 45 nssa default-information-originate

R5#router ospf 1
R5#area 45 nssa
```

