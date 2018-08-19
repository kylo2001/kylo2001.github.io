---
layout: post
title:  "2178. 미로찾기"
date:   2018-07-20 18:07:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## 2178. 미로찾기



```
#include<cstdio>
#include<iostream>
#include<queue>
using namespace std;

int n;  //row
int m;  //column

bool maze[100][100];  // true: 갈 수 있는 길, false: 막힌 길 , boolean 값 자체가 true면 1, false면 0의 값을 갖는다.
int check[100][100]; // 지나온길 기록
int direction[4][2] = { {1,0}, {-1,0}, {0,1}, {0,-1} }; // down, up, right, left check.

//input
void In() {
    cin >> n >> m;
    
    // 입력되는 데이터로 2차원 행렬 만든다.
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < m ;j++) {
            int b;
            
//            1234125
//            1260729
//            1346868
//
//            이런식으로 정수가 띄어쓰기 없이 입력받을때 cin으로 입력이 어렵다.
//
//            이때 scanf(“%1d”,&num)처럼 %1d를 사용하면 붙어있어도 한번에 한개씩 입력 받을 수 있다.
//
//            귀찮게 char배열로 입력받아서 변환 시켜줄 필요가 없어서 좋다.
            
            scanf("%1d", &b);
            if(b == 1){
                maze[i][j] = true;
            }
        }
    }
}

//output
void Out(int num) {
    cout << num << endl;
}

// 입력된 n * m을 벋어나는지 확인한다.
bool isInside(int a, int b) {
    return (a >= 0 && a < n) && (b >= 0 && b < m);
}


int bfs() {
    int current_y = 0, current_x = 0;   //current y, x
    
    queue<pair<int, int>> q;
    q.push(pair<int, int>(current_x, current_y));
    check[current_x][current_y] = 1;    //bfs가 지나가면서 몇번째만에 그 점에 접근했는지
    
    // 큐가 비었다는 것은 출구에 도착했거나 길이 없다라는 것이다.
    while(!q.empty()){
        
        // 큐에 먼저 들어온 정점을 pop한다.
        current_x = q.front().first;
        current_y = q.front().second;
        q.pop();
        
        if((current_x == n) && (current_y == m)) break; //도착지점
        
        // 현재 위치에서 상하좌우 위치에 있는 정점에 대해서 이동가능한지 검사하여 큐에 추가한다.
        for(int i=0; i<4; i++){
            //up, down, left, right !!
            int next_x = current_x + direction[i][0];
            int next_y = current_y + direction[i][1];
            
            //next y, x가 배열 내부에 있고, 방문한 적이 없고, map에 true로 되어있으면
            if(isInside(next_x, next_y) && check[next_x][next_y] == 0 && maze[next_x][next_y]){
                check[next_x][next_y] = check[current_x][current_y] + 1; // 이전 방문값 + 1 = 다음 방문
                q.push(pair<int, int>(next_x, next_y)); //방문한 곳 push
            }
        }
    }
    
    return check[n-1][m-1];
}

int main(void){
    In();
    Out(bfs());
    return 0;
}
```