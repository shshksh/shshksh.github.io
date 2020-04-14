---
title: "알고리즘 풀이 BOJ2004"
classes: wide
categories: 
  - Algorithm
tag:
  - algorithm
  - boj
  - unsolved
read_time: false
last_modified_at: 2020-04-15T01:40:00+09:00
toc: true
---

문제: [BOJ2004-조합 0의 개수](https://www.acmicpc.net/problem/2004)



접근 방법 자체를 틀려버렸다. 이전의 BOJ1676 문제를 완전탐색으로 풀어버려서 값을 계산하는데 초점을 맞춰버려서 진행이 되지 않았다.



## 풀이

```java
public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n, k;
        StringTokenizer st = new StringTokenizer(br.readLine());
        n = Integer.parseInt(st.nextToken());
        k = Integer.parseInt(st.nextToken());
        int t = n-k;

        int a = fiveCount(n);
        int b = fiveCount(t);
        int c = fiveCount(k);

        int f = a-b-c;

        a = twoCount(n);
        b = twoCount(t);
        c = twoCount(k);

        f = Math.min(f, a-b-c);

        System.out.println(f);
    }

    public static int twoCount(int n) {
        int sum = 0;
        for (long i = 2; i <= n; i*=2) {
            sum += n/i;
        }
        return sum;
    }

    public static int fiveCount(int n) {
        int sum = 0;
        for (long i = 5; i <= n; i*=5) {
            sum += n/i;
        }
        return sum;
    }
}
```

n!에서 0의 개수는 10이 곱해지는 횟수다. n!에서 0의 개수를 구하려면 2와 5가 곱해지는 횟수 중 낮은 값을 구하면 된다. 1부터 n까지 각각의 수가 곱해지는 횟수는 twoCount와 fiveCount로 구했다. n을 5로 나눈 몫은 5가 한번 곱해지는 횟수이다. 5의 제곱인 25의 배수는 5가 한번씩 더 곱해지기때문에 나누는 수를 5씩 곱해가며 모든 횟수를 구한다.

접근법만 보고 다시 풀었을때는 5가 나오는 횟수가 무조건 2가 나오는 횟수보다 작으니 twoCount에 대해선 구현을 안했는데 뺄셈의 결과는 달랐다.  n과 t가 125, 5인 경우에는 2가 남은 횟수가 5가 남은 횟수보다 더 작아지기 때문에 두 경우 모두 계산하여 작은 값을 찾아야 한다.

나머지 10의 경우에만 집중하여 다른 경우를 신경쓰지 못했다. 어려운 로직이 아닌데 아쉬움이 크다.