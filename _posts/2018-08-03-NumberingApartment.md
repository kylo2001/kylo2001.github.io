---
layout: post
title:  "2667. 단지번호 붙이기"
date:   2018-08-03 18:07:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## 2667. 단지번호 붙이기

```
#include <cstring>
#include <iostream>
#include <queue>
using namespace std;

int n; // 정점 (1 ≤ N ≤ 1,000)
int m; // 간선 (1 ≤ M ≤ 10,000)
int startVertex; // 출발 정점 start vertex

int matrix[1001][1001]; // 인접 행렬
bool check[1001];       // 정점 방문 확인 배열

void in() {
    scanf("%d %d %d", &n, &m, &startVertex);
    int vertex1, vertex2;

    for(int i = 0; i < m; i++) {
        cin >> vertex1 >> vertex2;
        matrix[vertex1][vertex2] = 1;
        matrix[vertex2][vertex1] = 1;
    }
}

void travelDFS(int vertex) {
    cout << vertex;
    check[vertex] = true;
    for(int i = 1; i <= n; i++) {
        // 0 ~ n+1를 순차적으로 확인하여 간선이 존재하고, 방문할 정점이 방문된적 없다면 해당 정점을 방문한다.
        if(matrix[vertex][i] == 1 && check[i] == false) {
            cout << " ";
            travelDFS(i);
        }
    }
}

void travelBFS(int vertex) {
    int currentVertex;
    queue<int> q;
    q.push(vertex);
    check[vertex] = true;
    
    while(!q.empty()) {
        currentVertex = q.front();
        cout << currentVertex;
        q.pop();
        cout << " ";
        
        for(int i = 1; i <= n; i++) {
            if(matrix[currentVertex][i] == 1 && check[i] == false) {
                q.push(i);
                check[i] = true;
            }
        }
    }
}
void init() {
    for(int i = 1; i <= n; i++) {
        check[i] = false;
    }
    cout << endl;
}

int main(int argc, const char * argv[]) {
    in();
    travelDFS(startVertex);
    init();
    travelBFS(startVertex);
    return 0;
}
```



