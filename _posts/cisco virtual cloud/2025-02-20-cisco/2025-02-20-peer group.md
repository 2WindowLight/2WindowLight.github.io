---
title: <h0>peer group</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-20-cisco]
tags: [Network, BGP]














---

# 피어 그룹

피어 그룹(peer group) 이란 동일한 출력 정책이 적용되는 BGP 피어를 하나의 그룹으로 묶어서 설정하는 것을 말한다.

**피어 그룹을 사용하면, BGP 광고를 생성할 때 하나의 피어에 대해서만 루트맵 등 출력정책을 적용시키고, 그 결과로 얻어진 광고를 모든 피어 멤버에게 전송하므로, 광고 전송시 사용되는 CPU 와 메모리의 사용량을 감소시켜 시스템을 안정화 시킨다. 또 피어그룹은 BGP의 설정을 간편하게 한다.**

피어그룹의 원칙

* 피어 그룹의 멤버들은 루트 맵, 필터 리스트, 디스트리뷰트 리스트 등 모두 동일한 출력 정책을 가지고 있어야 한다. 그러나 디폴트 루트를 생성시키는 default-originate 명령어는 서로 달라도 된다.
* 입력 정책은 멤버간에 서로 다르게 설정해도 된다.
* 피어들은 모두 iBGP 피어이거나, eBGP 피어로 구성되어야 한다. eBGP 피어의 경우 멤버간 서로 다른 AS 번호를 가져도 된다. R3에서 피어그룹을 이용하여 BGP 를 설정한다.



------

### 예시

### R1

```bash
router bgp 2
nei 1.1.1.1 remote-as 2
nei 1.1.1.1 up lo 0
nei 1.1.1.1 route-map TEST out

nei 2.2.2.2 remote-as 2
nei 2.2.2.2 up lo 0
nei 2.2.2.2 route-map TEST out

nei 3.3.3.3 remote-as 2
nei 3.3.3.3 up lo 0
nei 3.3.3.3 route-map TEST out

nei 4.4.4.4 remote-as 2
nei 4.4.4.4 up lo 0
nei 4.4.4.4 route-map TEST out

# + 각각에 nei x.x.x.x route-reflector-clint 하면
# 아래 peer group 설정할 떼 nei TEST route-reflector-client 를 추가
```



### peer group 으로 설정

```bash
router bgp 2
nei TEST peer-group
nei TEST remote-as 2
nei TEST up lo 0
nei TEST route-map TEST out
nei 1.1.1.1 peer-group TEST
nei 2.2.2.2 peer-group TEST
nei 3.3.3.3 peer-group TEST
nei 4.4.4.4 peer-group TEST
```



------

