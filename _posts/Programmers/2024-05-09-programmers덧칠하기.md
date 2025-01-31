---
title: <h0>Programmers-덧칠하기 [java]</h0>
author: cotes   
categories: [Programmers-Level1]
tags: [Programmers,java,덧칠하기,프로그래머스]




---

## 프로그래머스 - 덧칠하기 Java1



> [!TIP]
>
> ###### 문제 설명
>
> 어느 학교에 페인트가 칠해진 길이가 `n`미터인 벽이 있습니다. 벽에 동아리 · 학회 홍보나 회사 채용 공고 포스터 등을 게시하기 위해 테이프로 붙였다가 철거할 때 떼는 일이 많고 그 과정에서 페인트가 벗겨지곤 합니다. 페인트가 벗겨진 벽이 보기 흉해져 학교는 벽에 페인트를 덧칠하기로 했습니다.
>
> 넓은 벽 전체에 페인트를 새로 칠하는 대신, 구역을 나누어 일부만 페인트를 새로 칠 함으로써 예산을 아끼려 합니다. 이를 위해 벽을 1미터 길이의 구역 `n`개로 나누고, 각 구역에 왼쪽부터 순서대로 1번부터 `n`번까지 번호를 붙였습니다. 그리고 페인트를 다시 칠해야 할 구역들을 정했습니다.
>
> 벽에 페인트를 칠하는 롤러의 길이는 `m`미터이고, 롤러로 벽에 페인트를 **한 번** 칠하는 규칙은 다음과 같습니다.
>
> - 롤러가 벽에서 벗어나면 안 됩니다.
> - 구역의 일부분만 포함되도록 칠하면 안 됩니다.

위의 문제 설명을 쉽게 풀이하면 

* n 은 한 벽면애 몇개의 구역이 있는지의 개수 - 즉 n이 8이면 1층부터 8층 까지 있는 셈이다.
* m 은 룰러의 길이 - 즉 m이 3이면 1층-3층 or 2층-5층 까지 덮을 수 있다
* section 은 페인트가 안칠해져 있는 벽면 층 - 2,3,5 이면 그 층들이 비어있고 m이 3이면 2,3,5 층을 1번에 채울 수 있음
* 위의 결과로 answer 은 1이다.

------

## 풀이 방법?

내가 생각해 놓은 방법은 

1. 그리디 알고리즘을 사용
2. hashmap 을 사용하여 그냥 배열에 boolean을 써서 구분

여기서는 그리디 알고리즘을 사용하는 것이 적합하지만, HashMap 을 이용하여 풀이해 보았다.

먼저, HashMap 의 Key는 Intger , Value 는 boolean 으로 정의 하였다.

그 다음 칠해져 있는 부분은 true & 칠해져 있지 않는 부분은 false로 저장한다.

해결 과정에서 해당 맵의 키 값이 false 이면 , 덧칠 횟수 증가 

첫 번째 층 영역에서 덧칠한 길이 만큼 증가하고 위의 과정을 반복한다.

또 값이 false 가 아니면   이미 칠한 영역에서 +1 만큼 증가한다.

------

### 전체 코드

```java
class Solution {
    public int solution(int n, int m, int[] section) {
        HashMap<Integer, Boolean> map = new HashMap<Integer, Boolean>();
        for(int i = 1; i < n + 1; i++){
            map.put(i,true);
        } 
        for(int j = 0; j < section.length; j++){
                    map.put(section[j], false);
            }
         
        int answer = overCoat(m,map);
        return answer;
    }
    public int overCoat(int m, HashMap map){
        int location = 1;
        int answer = 0;
        while (location <= map.size()){
            if (map.get(location).equals(false)){ 
                answer++;
                location += m;
            }else location++; 
        }
        return answer;
    }
}
```