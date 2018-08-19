---
layout: post
title:  "1012. 유기농 배추"
date:   2018-08-03 18:07:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## 1012. 유기농 배추

```
#include <iostream>
using namespace std;

int testCaseNumber;
int cabbageNumber;
int earthwormNumber = 0;
int n, m; // (n, m)    1<= n, m <= 50

int cabbageField[50][50];
bool check[50][50];

int direction[4][2] = { {1, 0}, {-1, 0}, {0, 1}, {0, -1} };

void in() {
    cin >> m >> n >> cabbageNumber;
    int x, y; // 임시로 행렬을 만들기 위한 변수
    
    for(int i = 0; i < cabbageNumber; i++) {
        cin >> y >> x;
        cabbageField[x][y] = 1;
    }
}

bool isInside(int x, int y) {
    return (x >= 0 && x < n) && (y >= 0 && y < m);
}

void search(int x, int y) {
    int currentX = x, currentY = y;
    
    check[currentX][currentY] = true;
    for(int i = 0; i < 4; i++) {
        int nextX = currentX + direction[i][0];
        int nextY = currentY + direction[i][1];
        
        if(isInside(nextX, nextY) && check[nextX][nextY] == false && cabbageField[nextX][nextY] == 1) {
            search(nextX, nextY);
        }
    }
}

void numbering() {
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < m; j++) {
            if(cabbageField[i][j] == 1 && check[i][j] == false) {
                earthwormNumber++;
                search(i, j);
            }
        }
    }
}
void out() {
    cout << earthwormNumber << endl;
}

void init() {
    earthwormNumber = 0;
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < m; j++) {
            cabbageField[i][j] = 0;
            check[i][j] = false;
        }
    }
}

int main(int argc, const char * argv[]) {
    cin >> testCaseNumber;
    for(int i = 0; i < testCaseNumber; i++) {
        in();
        numbering();
        out();
        init();
    }
    return 0;
}
```



