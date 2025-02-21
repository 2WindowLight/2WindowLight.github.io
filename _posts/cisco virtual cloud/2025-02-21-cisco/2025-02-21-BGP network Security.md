---
title: <h0>BGP Network Security</h0>
author: cotes   
categories: [cisco virtual cloud, 2025-02-21-cisco]
tags: [Network, BGP]















---

# BGP 네트워크 보안

BGP 네트워크는 다른 조직의 네트워크와 연결되므로 보안이 중요하다.

보안 대책

* eBGP 설정시 TTL 값 제한
* BGP 메시지 인증
* 최대 수신 프리픽스 수 제한



------

BGP 인증

eBGP 간의 네이버 인증은 거의 필수적이며 메시지 인증하는 방법은 MD5 인증 기능만을 사용한다.

BGP 네이버 인증을 하려면 네이버 설정시 password 옵션을 사용한다.

### 예제

```bash
router bgp 1
neighbor 1.1.12.2 password VerySecure
```

------

## 최대 수신 네트워크 제한

특정 네이버를 통해서 광고받는 최대 BGP 네트워크의 수를 제한할 수 있다. 최대 수신 네트워크 제한시 다음과 같은 옵션들을 사용할 수 있다.

```bash
R4(config-router)#nei 1.1.24.2 maximum-prefix 100 ?
  <1-100>       Threshold value (%) at which to generate a warning msg
  restart       Restart bgp connection after limit is exceeded
  warning-only  Only give warning message when limit is exceeded
  <cr>
```

