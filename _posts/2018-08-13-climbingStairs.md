---
layout: post
title:  "2579. 계단 오르기"
date:   2018-08-13 11:57:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## **2579. 계단 오르기**

이번 문제는 제약이 있는 DP문제이다. 여기서 제약은

1. 연속으로 3칸을 올라갈 수 없다.
2. 마지막 계단에 반드시 올라가야 한다.
3. 한번에 1칸 또는 2칸을 올라갈 수 있다.

즉, 예제에서 칸수가 6인 계단의 점수가 10, 20, 15, 25, 10, 20으로 주어졌을 때, 10 -> 20 -> 15 -> 는 불가하며 마지막 20은 반드시 밟아야 된다는 것이다.

이런 제약이 주어진 문제들은 이 조건을 어떻게 하면 어기지 않으면서 식을 만들까를 고민해 봐야 한다. 대부분의 DP문제가 그러하듯이 뒤에서부터 고려하여 식을 짜는 것이 훨씬 편하다.



문제 에서 주어지는 계단을 살짝 변형하여 배열로 나타내면 아래와 같다.

![](https://user-images.githubusercontent.com/20294786/43999701-bec9a494-9e4c-11e8-883b-f7c84b4b38ac.png)



이 그림을 바탕으로 뒤에서부터 접근하여 조건들을 만족하는 식을 살펴보자.

먼저 맨 뒤 계단은 반드시 밟아야 된다. 따라서 맨 뒤 계단을 밟는 방법은 맨뒤 -1 계단에서 1칸 올라오는 경우, 맨뒤 -2번째 계단에서 2칸을 건너 올라오는 경우이다. 그 이전 경우들은 어떻게 하던 간에 결국 저 2가지 경우 중 하나에 속해지기 때문에 고려할 필요가 없다.



![](https://user-images.githubusercontent.com/20294786/43999700-bea46a3a-9e4c-11e8-8ea6-f4401f061536.png)



결국 이렇게 나오는 것이고, 이제 dp[i]를 정의해 주도록 한다. 우리가 구해야 할 것은 마지막 계단을 밟았을 때 얻을 수 있는 최대 점수이다. 따라서 dp[i] = i번째 계단에서 최대점수가 된다. 그렇다면 위 그림의 경우 dp[i] = (dp[i-2]와 dp[i-1]중 큰 값) + a[i]가 된다.



그런데 여기서 이렇게만 하면 "틀렸습니다."라는 결과를 받을 것이다. 그 이유는 바로 저런 경우 3칸 연속 오르는 경우를 배제할 수 없기 때문이다. 따라서 1차원 배열로만 생각했던 것을 2차원 배열로 확장해서 생각해보자.

```
(1) dp[i][0] = i번째 계단을 1칸 전에 올라와서 얻을 수 있는 최대 점수
(2) dp[i][1] = i번째 계단을 2칸 전에 올라와서 얻을 수 있는 최대 점수
```



이렇게 정했으면 다시 한번 생각해 보자. 어떻게 3칸 연속 올라오는 것을 방지할 수 있을까?

우선 (1)의 경우엔 이미 2칸을 올라왔기 때문에 다음 계단이 3연속 되는 계단인지 생각해줄 필요가 없다. 오히려 임시 계단(마지막 -2 계단)에 서있을 때, 여기까지 오는데 2칸 전에서 올라오는 경우와 1칸 전에서 올라오는 경우 중 어느 값이 큰 값인지를 고려해 줘야 한다. 그림으로 보면 아래와 같아.

![](https://user-images.githubusercontent.com/20294786/43999699-be7f7f86-9e4c-11e8-998f-ae6cc94abfe8.png)



따라서 (2)의 경우 점화식은 다음과 같다.

```
// i번째 계단을 2칸 전에 올라와서 얻을 수 있는 최대 점수
dp[i][1] = MAX(dp[i-2][0], dp[i-2][1]) + a[i];
```



반면, (1)의 경우 이미 현재 계단에 1칸을 올라왔기 때문에 3연속 올라가는 경우를 방지해주어야한다. dp[i]의 최대값을 비교하는 과정에서 애초에 dp[i-1] 번째 칸(현재 계단 -1)에 두 칸을 올라온 경우만 고려해주면 된다. 따라서 (1)의 점화식은 아래와 같다.

```
// i번째 계단을 1칸 전에 올라와서 얻을 수 있는 최대 점수
dp[i][0] = dp[i-1][1] + a[i];
```

 

자 이제 생각해낸 점화식을 디버깅 해보자.

![](https://user-images.githubusercontent.com/20294786/43999698-be59c0b6-9e4c-11e8-8f02-63038ce9d99e.png)

C++ 구현 코드

```
#include <iostream>
#define MAX(a, b) a>b ? a : b
using namespace std;
 
int n, a[301], dp[301][2];

void in() {
    cin >> n;

    for(int i = 1; i <= n; i++) {
        cin >> a[i];
    }
}

void climbing() {
    dp[1][0] = dp[1][1] = a[1];

    for(int i = 2; i <= n; i++) {
        dp[i][0] = dp[i - 1][1] + a[i];

        int maxValue = MAX(dp[i - 2][0], dp[i - 2][1]);
        int stairValue = a[i];

        dp[i][1] = maxValue + stairValue;
    }
}

void out() {
    int result = MAX(dp[n][0], dp[n][1]);
    cout << result << endl;
}

int main(int argc, const char * argv[]) {
    in();
    climbing();
    out();
    return 0;
}
```

##### 참고 블로그 : https://m.blog.naver.com/PostView.nhn?blogId=occidere&logNo=220788947949&proxyReferer=https%3A%2F%2Fwww.google.com%2F