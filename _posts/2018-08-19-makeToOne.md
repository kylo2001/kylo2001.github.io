---
ㅋayout: post
title:  "1463. 1로 만들기"
date:   2018-08-19 23:25:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## 1463. 1로 만들기



```
#include <iostream>
using namespace std;

// < 가능한 연산 >
// 1. 3으로 나누기
// 2. 2로 나누기
// 3. 1 빼기

// dp[n] : n을 1로 만들기 위해 필요한 최소 연산 횟수
int dp[1000001];
int n;

void in() {
    cin >> n;
    dp[1] = 0;
}

int min(int a, int b, int c) {
    
    int min = 0;
    
    if (a < b) {
        min = a;
    } else {
        min = b;
    }
    
    if (min < c) {
        return min;
    } else {
        min = c;
    }
    return min;
}

void makeToOne() {
    for (int i = 2; i <= n; i++) {
        int a = n, b = n, c;
        
        if (i % 3 == 0) { a = dp[i/3] + 1; }
        if (i % 2 == 0) { b = dp[i/2] + 1; }
        c = dp[i-1] + 1;
        
        dp[i] = min(a, b, c);
    }
}

void out() {
    cout << dp[n] << endl;
}

int main(int argc, const char * argv[]) {
    in();
    makeToOne();
    out();
    return 0;
}

```

