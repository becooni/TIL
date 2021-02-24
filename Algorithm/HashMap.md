# HashMap

문제 출처: 프로그래머스 - 완주하지 못한 선수

혼자 힘으로 풀진 못했다

처음엔 민서의 의견을 참고하여 두 배열을 정렬 후 비교 하는 방법으로 품

```java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        Arrays.sort(participant);
        Arrays.sort(completion);
        int i;
        for ( i=0; i<completion.length; i++){

            if (!participant[i].equals(completion[i])){
                return participant[i];
            }
        }
        return participant[i];
    }
}
```

위 풀이는 아래 풀이에 비해 3~4배 정도 느리다

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public String solution(String[] participant, String[] completion) {
        String answer = "";

        HashMap<String, Integer> hashMap = new HashMap<>();

       for (String runner : participant) {
            hashMap.put(runner, hashMap.getOrDefault(runner, 0) + 1);
        }

        for (String winner : completion) {
            hashMap.put(winner, hashMap.get(winner) - 1);
        }

        for (Map.Entry<String, Integer> entry : hashMap.entrySet()) {
            if(entry.getValue() != 0) {
                answer = entry.getKey();
                break;
            }
        }

        return answer;
    }
}
```

- HashMap에 key는 runner를, value에는 동명이인 선수 runner key가 있으면 `n+1` 없으면 `0+1=1` 을 put하여 참가자 HashMap을 완성한다
- 앞서 만든 HashMap에 key는 winner를, value에는 `n-1` 하여 put한다.
- 이제 HashMap의 진가가 나온다. HashMap은 새로운 key가 기존 key와 중복이면 새로운 key로 대체된다.
- HashMap을 탐색하여 value가 0이 아닌것만 찾으면 미 완주자를 찾을 수 있다.