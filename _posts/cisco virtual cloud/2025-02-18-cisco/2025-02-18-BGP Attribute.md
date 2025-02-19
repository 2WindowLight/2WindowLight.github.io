---
title: <h0>BGP Attribute</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-18-cisco]
tags: [Network]









---

# BGP Attribute



BGP 속성의 분류

**📌 BGP 속성(Attribute) 분류 및 설명**



BGP(Attribute, 속성)는 라우터가 최적의 경로를 선택하는 기준으로 사용됩니다.

BGP의 속성은 크게 **필수(Mandatory), 선택(Optional), Well-Known** 등의 카테고리로 분류됩니다.

**✅ 1. BGP 속성(Attribute) 분류**



BGP 속성은 아래와 같이 **4가지 주요 카테고리**로 분류됩니다.

| **구분**                     | **설명**                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| **Well-Known Mandatory**     | 모든 BGP 라우터가 반드시 이해하고 처리해야 하는 속성         |
| **Well-Known Discretionary** | 모든 BGP 라우터가 이해할 수 있지만, 적용 여부는 선택 가능    |
| **Optional Transitive**      | 일부 라우터에서만 이해할 수 있고, 이해하지 못하는 경우에도 그대로 전달 |
| **Optional Non-Transitive**  | 일부 라우터에서만 이해할 수 있으며, 이해하지 못하는 경우 다른 라우터에게 전달되지 않음 |

**✅ 2. BGP 속성 상세 설명 (표 포함)**

| **속성**                           | **분류 (Type)**          | **설명**                                                     | **경로 선택 기준 (우선순위)** |
| ---------------------------------- | ------------------------ | ------------------------------------------------------------ | ----------------------------- |
| **Weight**                         | Cisco 전용               | - 라우터에서 경로 우선순위를 결정하는 로컬 속성- 값이 클수록 우선순위 높음 | 1                             |
| **Local Preference (LocPref)**     | Well-Known Discretionary | - AS 내부에서 BGP 경로의 우선순위를 결정- 값이 클수록 우선순위 높음 | 2                             |
| **AS Path**                        | Well-Known Mandatory     | - 경로에 포함된 AS 경로 리스트- 경로가 짧을수록 우선순위 높음 | 3                             |
| **Origin**                         | Well-Known Mandatory     | - 경로가 어떻게 생성되었는지를 나타냄- **IGP(i) > EGP(e) > Incomplete(?)** 순으로 우선 | 4                             |
| **MED (Multi-Exit Discriminator)** | Optional Non-Transitive  | - 다른 AS로 들어오는 트래픽의 선호도를 설정- 값이 낮을수록 우선순위 높음 | 5                             |
| **Next-Hop**                       | Well-Known Mandatory     | - 목적지로 가는 다음 라우터의 IP 주소- 올바르게 설정되지 않으면 경로가 무시됨 | 필수                          |
| **Community**                      | Optional Transitive      | - 특정 그룹을 정의하여 정책을 일괄 적용 가능- Route Filtering에 주로 사용 | -                             |
| **Atomic Aggregate**               | Well-Known Discretionary | - AS 경로 요약이 발생했음을 나타냄                           | -                             |
| **Aggregator**                     | Optional Transitive      | - 경로 요약을 수행한 BGP 라우터의 정보를 포함                | -                             |

**✅ 3. BGP 경로 선택 순서 (우선순위)**



BGP 라우터는 아래 **우선순위 기준**에 따라 최적 경로를 선택합니다.

| **우선순위** | **BGP 속성**                       | **우선순위 기준 (낮을수록 우선)** |
| ------------ | ---------------------------------- | --------------------------------- |
| 1            | **Weight (Cisco 전용)**            | 값이 클수록 우선                  |
| 2            | **Local Preference**               | 값이 클수록 우선                  |
| 3            | **AS Path**                        | 경로가 짧을수록 우선              |
| 4            | **Origin**                         | IGP(i) > EGP(e) > Incomplete(?)   |
| 5            | **MED (Multi-Exit Discriminator)** | 값이 낮을수록 우선                |
| 6            | **eBGP > iBGP**                    | eBGP 경로가 iBGP보다 우선         |
| 7            | **최소 IGP Cost**                  | IGP에서 비용이 낮은 경로 선택     |
| 8            | **Router ID**                      | 값이 작은 라우터가 우선           |

**✅ 4. BGP 속성의 주요 기능 정리**

| **속성**             | **기능 및 역할**                                  |
| -------------------- | ------------------------------------------------- |
| **Weight**           | Cisco 전용 속성, 로컬 라우터 내에서 우선순위 결정 |
| **Local Preference** | AS 내부에서 최적 경로 선택                        |
| **AS Path**          | 경로의 AS 개수를 기준으로 짧은 경로를 우선        |
| **Origin**           | 경로의 출처를 정의 (i > e > ?)                    |
| **MED**              | 다른 AS로 들어오는 트래픽 경로 조정               |
| **Next-Hop**         | 목적지로 가는 다음 경로를 설정                    |
| **Community**        | 특정 경로를 그룹화하여 정책 적용 가능             |

**📌 최종 정리**

​	•	BGP 속성은 **경로 선택 및 트래픽 제어에 사용**되며, 다양한 **우선순위 기준**을 가짐

​	•	**Weight와 Local Preference**는 AS 내부의 경로 선택에 영향을 줌

​	•	**AS Path와 MED**는 AS 간 경로 선택을 조정함

​	•	**Next-Hop 속성이 잘못 설정되면 경로가 정상적으로 작동하지 않음**

​	•	**BGP 경로 선택 순서는 Weight > Local Preference > AS Path > Origin > MED 순으로 결정됨**



✅ **이제 BGP 속성을 활용하여 네트워크 경로를 최적화할 수 있음!** 🚀





오리진 igp 405







------

AS 경로 Path 408