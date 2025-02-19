---
title: <h0>Network Advertise limit</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-19-cisco]
tags: [Network]











---

# 네트워크 광고 제어

네트워크 광고 제어는 특정 네이버에게 라우팅 정보를 전송할 때 또는 수신할 때 적용할 수 있다.

또, 특정 네트워크의 광고를 차단하거나, 특정 네트워크를 허용할 수 있다.



------

## 디스트리뷰트 리스트를 이용한 네트워크 광고 제어

디스트리뷰트 리스트를 이용하여 특정 네이버로부터 특정 네트워크에 대한 광고를 차단 또는 허용하려면 다음과 같이 BGP 설정 모드에서 distribute-list 명령어 다음에 차단 또는 혀용하고자 하는 네트워크를 지정한 엑세스 리스트를 설정하면 된다.

```bash
router bgp 1
neighbor 1.1.12.2 distribute-list 
```



예제 distribute-list를 사용한 네트워크 차단

```bash
access-list 1 deny 1.1.2.0 0.0.0.255
access-list 1 permit any

router bgp 1
neighbor 1.1.12.2 distribute-list 1 in
```

설정 후의 테이블은 1.1.2.0/24의 네트워크가 없다.

```

```

------

