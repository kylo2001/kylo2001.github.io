---
layout: post
title:  "선택 정렬"
date:   2018-08-27 12:31:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## 선택 정렬(Selection sort)

선택 정렬은 제자리 정렬 알고리즘입니다. 선택 정렬은 작은 양의 데이터에 유용합니다. 이 알고리즘은 키는 작고 값은 큰 데이터에 사용됩니다. 키를 기반으로 선택이 이루어지고 치환이 필요하다고 판단되면 치환이 이루어지기 때문입니다. 



##### 장점

* 구현이 쉽습니다.
* 제자리 정렬(추가 저장 공간이 필요없음)알고리즘입니다.



##### 단점

* 데이터의 양에 유연하지 않습니다.



##### 알고리즘

1. 목록에서 최소값을 찾습니다.
2. 찾은 최솟값을 맨 앞의 값(혹은 현재 위치의 값)과 교체합니다.
3. 배열의 전체 요소가 정렬될 때까지 이 과정을 반복합니다.



##### 순서도

![](https://user-images.githubusercontent.com/20294786/44630015-6df6a480-a992-11e8-8e97-85e765d4485c.jpeg)



##### 성능

* 최악의 경우 복잡도 : O(n^2)
* 최선의 경우 복잡도 : O(n)
* 평균 복잡도 : O(n^2)
* 최악의 경우  공간 복잡도 : O(1) 보조 공간



##### C++ 구현 코드

```
#include <iostream>
using namespace std;

int arr[10] = {30, 20, 10, 50, 40, 60, 100, 80, 90, 70};
int n = sizeof(arr) / sizeof(int);

void swap(int *x, int *y) {
    int temp;
    temp = *x;
    *x = *y;
    *y = temp;
}

void selectionSort() {
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (arr[i] > arr[j]) {
                swap(&arr[i], &arr[j]);
            }
        }
    }
}

void out() {
    for (int i = 0; i < n; i++) {
        cout << arr[i] << " ";
    }
    cout << endl;
}

int main(int argc, const char * argv[]) {
    selectionSort();
    out();
    return 0;
}
```