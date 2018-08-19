---
layout: post
title:  "11052. 붕어빵 판매하기"
date:   2018-08-13 11:57:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## 11052. 붕어빵 판매하기



이번 문제를 해결하기 위해서 그림을 그려보았다.

![](https://user-images.githubusercontent.com/20294786/43999683-6963e866-9e4c-11e8-9316-3d881ebcaf02.png)

문제는 이러하다.

도날드라는 붕어빵 장사꾼이 있다. n개의 붕어빵을 만들었고 한번에 i개를 팔았을 때 받을 수 있는 돈은 p[i]원이다. 문제에서 요구하는 답은 N개의 붕어빵을 팔았을 때 최대로 벌 수 있는 수익은 얼마인가?? 이다. 점화식을 위해 dp[n]을 이렇게 정의하겠다. 

```
p[i] 는 i 개의 붕어빵을 팔았을 때 받을 수 있는 이익
dp[n] = 붕어빵 n개를 팔았을 때 얻을 수 있는 최대 수익
```

dp배열은 Memoization을 위해 사용된다.

이 문제를 풀기 위해 생각해낸 로직은 아래와 같다.

만약 어떤 손님에게 i개의 붕어빵을 팔았다면 나머지 손님에게는 n - i개의 붕어빵을 팔아야 한다.

따라서 이 문제에 점화식을 도출해낸다면 아래와 같을 것이다.

```
dp[n] = p[i] + dp[n-i]
```

 

도출해낸 점화식을 가지고 디버깅을 해보았다.

![1](https://user-images.githubusercontent.com/20294786/43999682-6941da64-9e4c-11e8-9313-d313f19655fe.png)

C++ 구현 코드

```
#include <iostream>
#define MAX(a, b) a>b ? a : b
using namespace std;

int n, dp[1001], a[1001];

void in() {
    cin >> n;
    
    for(int i = 1; i <= n; i++) {
        cin >> a[i];
    }
}

void selling() {
    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= i; j++) {
            dp[i] = MAX(dp[i], a[j] + dp[i-j]);
        }
    }
}

void out() {
    cout << dp[n] << endl;
}

int main(int argc, const char * argv[]) {
    in();
    selling();
    out();
    return 0;
}
```