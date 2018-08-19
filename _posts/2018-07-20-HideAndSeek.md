---
layout: post
title:  "1697. 숨바꼭질"
date:   2018-07-20 18:07:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## 1697. 숨바꼭질



```
#include<cstdio>
#include <queue>
#include <iostream>
using namespace std;

int n, k;          // 수빈이, 동생의 위치를 입력받기 위한 변수
int map[100001];   // 방문한 위치에서의 흐른 시간을 알 수 있다.
int check[100001]; // 방문확인을 위한 배열

// 입력받은 위치를 가지고 현재 위치에 방문 표시를 하고 시간을 0으로 세팅한다.
void in() {
    scanf("%d %d", &n, &k);
    map[n] = 0;
    check[n] = 1;
}

// 매개변수 time 변수에 전달받은 값을 출력한다.
void out(int time) {
    if (time == -1) {
        cout << "동생을 찾지 못했습니다.." << endl;
    } else {
        cout << time << endl;
    }
}

// 문제에서 주어진 범위내에 위치하고 있는지 확인한다.
bool isInside(int a) {
    return ((a >= 0) && (a <= 100000));
}

// BFS 알고리즘을 활용하여 동생의 위치를 찾아낸다.
int seek() {
    queue<int> q;
    int current; // 현재 수빈이의 위치
    int next[3]; // 큐에 저장할 다음 위치들을 배열의 형태로 저장한다.
    
    q.push(n);
    
    while (!q.empty()) {
        current = q.front(); // 큐의 제일 앞에 있는 데이터를 얻어온다.
        q.pop();
        
        // 현재위치가 동생의 위치라면 동생을 찾아다고 간주하고 함수를 종료한다.
        if (current == k) {
            return map[current];
        }
        
        next[0] = current - 1;
        next[1] = current + 1;
        next[2] = current * 2;
        
        for(int i = 0; i < 3; i++) {
            // 범위에 속하는고, 방문하지 않은 점인지 검사
            if(isInside(next[i]) && check[next[i]] == 0) {
                q.push(next[i]);
                check[next[i]] = 1;
                map[next[i]] = map[current] + 1;
            }
        }
    }
    return -1;
}

int main(int argc, const char * argv[]) {
    in();
    out(seek());
    return 0;
}
```