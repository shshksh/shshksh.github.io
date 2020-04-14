---
title: "알고리즘 오답노트 BOJ9375"
classes: wide
categories: 
  - Algorithm
tag:
  - algorithm
  - boj
read_time: false
last_modified_at: 2020-04-14T23:10:00+09:00
toc: true
---

**문제 출처**: [BOJ9375-패션왕 신해빈](https://www.acmicpc.net/problem/9375)

## 기존의 접근법

문제를 봤을때 이전에 프로그래머스에서 동일한 문제를 봤던 기억이 났다. 처음 생각한 방법은 다음과 같다.

의상의 종류를 **key**, 해당 종류 의상의 가짓수를 **value**로 한 **HashMap**을 사용하여 데이터를 저장한다. 이러면 의상의 종류당 몇가지의 의상이 있는지 알 수 있다. 이후 가능한 경우의 수를 구하는 로직에서 조금 막혔는데 의상의 종류가 k개일때, 1가지를 고르는 경우, 2가지를 고르는 경우, ... , k가지를 고르는 경우로 나눠서 생각하다가 **멱집합**으로 풀 수 있지 않을까 생각했다. 

main에서 데이터를 구하고 allCombOf 메서드에서 데이터의 멱집합을 구하여 각각의 경우당 가짓수를 더하여 반복하도록 구현했다.

```java
public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int t = Integer.parseInt(br.readLine());
        for (int i = 0; i < t; i++) {
            int clothesCnt = Integer.parseInt(br.readLine());
            HashMap<String, Integer> map = new HashMap<>();

            for (int j = 0; j < clothesCnt; j++) {
                String[] cloth = br.readLine().split(" ");
                map.put(cloth[1], map.getOrDefault(cloth[1], 0)+1);
            }

            int[] arr = new int[map.size()];
            int j = 0;
            for (Integer value : map.values()) {
                arr[j++] = value;
            }

            System.out.println(allCombOf(arr));

        }
    }

    private static int allCombOf(int[] data) {
        int sum = 0;
        for (int i = 1; i < 1 << data.length; i++) {
            int tmp = 1;
            for (int j = 1, k = 0; j <= i; j <<= 1, k++) {
                if((j&i) > 0) {
                    tmp *= data[k];
                }
            }
            sum += tmp;
        }
        return sum;
    }
}
```

바로 시간초과가 났다.

조건을 따져 봤으면 의상의 가짓수가 최악의 경우 30가지에 테스트 케이스가 최대 100개이므로 멱집합을 사용한다면 2^30 * 100으로 시간초과가 나는것이 자명했다...

프로그래머스에서 풀었을때도 동일하게 구현하여 풀었다가 다른 풀이를 보고 이해했던것 같은데 같은 실수를 해버렸다.



## 수정한 코드

```java
public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int t = Integer.parseInt(br.readLine());
        for (int i = 0; i < t; i++) {
            int clothesCnt = Integer.parseInt(br.readLine());
            HashMap<String, Integer> map = new HashMap<>();

            for (int j = 0; j < clothesCnt; j++) {
                String[] cloth = br.readLine().split(" ");
                map.put(cloth[1], map.getOrDefault(cloth[1], 1)+1);
            }

            int sum = 1;
            for (Integer value : map.values()) {
                sum *= value;
            }

            System.out.println(sum-1);
        }
    }
}
```



## 풀이

데이터를 구하는 for 루프에서 getOfDefault의 default 값이 0에서 1로 변경되었다.

이렇게 되면 의상의 종류당 의상의 수가 하나씩 늘어난채로 저장되게 된다. 이때 늘어난 의상은 **장착하지 않은 경우**를 의미한다. 만약 headgear 3개, eyewear 2개, face 4개인 경우 장착하지 않은 경우가 하나씩 추가되어 4, 3, 5가 된다. 이 경우에서 한가지를 고르는 모든 경우의 수는 4\*3\*5, 60이 된다. 이때 아무것도 장착하지 않은 경우의 수는 1개밖에 없으므로 하나라도 의상을 장착하는 경우는 60 -1인 59가 된다.



