---
title: <h0>suppress map</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-20-cisco]
tags: [Network, BGP]











---

# 스프레스 맵을 이용한 입력 경로 조정



**1. 스프레스 맵(Suppress Map)과 요약(Aggregation)의 개념**



------

**1.1 BGP 요약(Aggregate Address)**

​		•	**aggregate-address X.X.X.X Y.Y.Y.Y**

​		•	BGP에서 **더 큰 범위**(예: /16)로 **요약 경로**를 생성하여 광고합니다.

​		•	예) aggregate-address 1.1.0.0 255.255.0.0 → 1.1.0.0/16 요약 경로를 생성

​		•	**summary-only 옵션**

​		•	해당 요약 범위에 속하는 **모든 세부(prefix) 경로를 자동으로 억제**하고, 오직 요약만 광고합니다.

​		•	하지만 **세부 경로 중 일부는 계속 광고**하고, 일부만 억제하고 싶을 때는 summary-only로는 불가능합니다.



------

**1.2 suppress-map 옵션**

​		•	**aggregate-address X.X.X.X Y.Y.Y.Y suppress-map <route-map>**

​		•	**특정 조건(프리픽스 리스트 등)에 매칭되는** 경로만 **개별 광고를 억제**(suppress)합니다.

​		•	매칭되지 않는 경로는 **개별 경로도 계속 광고**합니다.

​		•	결과적으로, **요약 + 일부 세부 경로**만 광고되고, **매치된 세부 경로**는 억제됩니다.



​	핵심 **suppress-map**을 쓰면, “어떤 경로는 요약에 포함되지만 **개별 광고는 안 함**(억제)” 하고, “다른 경로는 **개별 광고도 계속**” 할 수 있어, **정밀한** 경로 광고 제어가 가능합니다.

------

**3.1 R2 측 설정**

```bash
R2(config)#ip prefix-list SUPPRESS-THIS permit 1.1.4.0/24
R2(config)#route-map NOT-SEND-THIS
R2(config-route-map)#match ip add prefix-list SUPPRESS-THIS
R2(config-route-map)#exit
R2(config)#router bgp 2
R2(config-router)#aggregate-address 1.1.0.0 255.255.0.0 supp
R2(config-router)#$ddress 1.1.0.0 255.255.0.0 suppress-map NOT-SEND-THIS     
R2(config-router)#exit
R2(config)#do clear ip bgp * soft
```

------

**3.2 R4 측 설정**

```bash
R4(config)#ip prefix-list SUPPRESS-THIS permit 1.1.2.0/24
R4(config)#route-map NOT-SEND-THIS
R4(config-route-map)#match ip add prefix-list SUPPRESS-THIS
R4(config-route-map)#exit
R4(config)#router bgp 2
R4(config-router)#aggregate-address 1.1.0.0 255.255.0.0 supp
R4(config-router)#$ddress 1.1.0.0 255.255.0.0 suppress-map NOT-SEND-THIS     
R4(config-router)#exit

```

------

**4. 결과 확인 (R1 관점)**

```bash
R1(config)#do sh ip bgp
BGP table version is 17, local router ID is 1.1.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *   1.1.0.0/16       1.1.14.4                 0             0 2 i
 *>                   1.1.12.2                 0             0 2 i
 *>  1.1.1.0/24       0.0.0.0                  0         32768 i
 *>  1.1.2.0/24       1.1.12.2                 0             0 2 i
 *>  1.1.3.0/24       1.1.12.2                               0 2 i
 *                    1.1.14.4                               0 2 i
 *>  1.1.4.0/24       1.1.14.4                 0             0 2 i
R1(config)#

```

​	•	**1.1.0.0/16**: R2와 R4 모두에서 들어오지만, R1은 R2 경유를 Best로 선택(별도 속성이 같으니 IP 비교 등으로 결정).

​	•	**1.1.2.0/24**: R2만 개별 광고 → R1이 R2 경유로 인식

​	•	**1.1.4.0/24**: R4만 개별 광고 → R1이 R4 경유로 인식



즉, R1이 **1.1.2.0/24**로 트래픽을 보낼 때는 R2를, **1.1.4.0/24**로는 R4를 사용하게 되며, 그 외 1.1.x.x 주소대역은 요약(1.1.0.0/16)을 통해 R2가 Best Path가 됩니다. 이렇게 **인바운드 트래픽**을 원하는 라우터(R2 또는 R4)로 분산할 수 있습니다.

------

**5. R2의 BGP 테이블**

```bash
R2#sh ip bgp
BGP table version is 11, local router ID is 1.1.2.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  1.1.0.0/16       0.0.0.0                            32768 i
 *>i 1.1.2.0/24       1.1.4.4                  0    100      0 1 i
 s>i 1.1.4.0/24       1.1.4.4                  0    100      0 i
R2#

```

**s>i**: 여기서 **s**는 **“suppressed”**를 의미합니다.

​	•	즉, R2는 1.1.4.0/24 경로를 내부적으로는 iBGP 등으로 학습하고 있으나, **suppress-map**에 의해 **외부(eBGP)** 광고에서는 억제 중.

​	•	**>**: BGP 상에서 Best 경로로 선정됨.

​	•	**i**: Origin이 IGP(i).

​	•	이 표시는 R2 내부에서 BGP가 경로를 어떻게 인식하고 있는지 보여줄 뿐, **광고 억제**(suppress) 여부는 s로 표시되어 확인할 수 있습니다.



------

**정리**

1. **aggregate-address ... suppress-map ...**

​		•	요약(aggregate)과 함께 **특정 서브넷**을 **개별 광고에서 제외(suppress)** 하는 기법.

​		•	route-map으로 매칭된 prefix만 억제하고, 매칭되지 않은 prefix는 그대로 광고.

2. **목적**

​		•	**요약 경로**를 만들어 **라우팅 테이블 단순화** + **일부 경로**는 계속 세부(prefix)로 광고하여 **트래픽 경로**를 원하는 대로 조정.

3. **이 시나리오**

​		•	R2는 1.1.4.0/24 억제, R4는 1.1.2.0/24 억제 → R1은 각각 다른 라우터로 해당 서브넷을 학습 → **인바운드 트래픽 분산**.

4. **R2 BGP 테이블에서의 s>i**

​		•	s(suppressed) + >(best) + i(origin IGP) → R2가 내부적으로 학습한 경로지만, suppress-map에 의해 **외부로 광고하지 않는** 상태임을 표시.

5. **결과**

​		•	R1에서 BGP를 보면, 1.1.2.0/24는 R2 경유, 1.1.4.0/24는 R4 경유, 그리고 1.1.0.0/16 요약은 양쪽에서 받아 어느 한쪽을 best로 선택.

​		•	**인바운드 경로 조정**이 성공적으로 달성됨.