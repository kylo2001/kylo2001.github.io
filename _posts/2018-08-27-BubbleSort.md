---
layout: post
title:  "버블 정렬"
date:   2018-08-27 1:20:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---



## 버블 정렬(Bubble sort)

버블 정렬은 간단한 정렬 알고리즘입니다. 버블 정렬은 입력 배열의 i번째 요소와 i+1 번째 요소를 비교하여 i번째 요소가 i+1 번째 요소보다 클 경우 두 요소의 위치를 바꾸는 작업을 입력 배열의 첫 번째부터 마지막까지 반복합니다. 더 이상 교체가 필요없을 때까지 이 과정을 계속 반복합니다. 매 반복 때마다 최소 값이 목록의 앞으로 이동하고 최대값이 배열의 마지막에 위치하게 되는 방식 때문에 버블(Bubble)이라는 이름을 가졌습니다. 일반적으로 버블 정렬보다 삽입 정렬의 복잡도가 더 좋습니다.



몇몇 사람들은 단순함과 복잡도 때문에 버블 정렬을 무시해야 한다고 합니다.

다른 방법보다 버블 정렬이 가지는 유일한 장점은 입력 목록이 이미 정렬되어 있는지를 탐지할 수 있다는 것 뿐입니다.



##### 순서도

![](https://user-images.githubusercontent.com/20294786/44631089-6a6b1980-a9a2-11e8-9452-11161f9fe718.jpeg)



##### 성능

- 최악의 경우 복잡도 : O(n^2)
- 최선의 경우 복잡도 : O(n)
- 평균 복잡도 : O(n^2)
- 최악의 경우  공간 복잡도 : O(1) 보조 공간



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

void bubbleSort() {
    for (int k = n - 1 ; k > 0; k--) {
        for (int i = 0; i < k; i++) {
            int j = i + 1;
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
    bubbleSort();
    out();
    return 0;
}
```



가장 좋은 경우 조차도 복잡도는 O(n^2)입니다. 더 이상의 치환이 필요하지 않을 때 정렬이 완료되었음을 가리키는 확장 플래그를 사용하게 되면 복잡도를 개선할 수 있습니다. 만약, 입력되는 배열이 정렬되어 있다면 시간 복잡도를 O(n)으로 줄일 수 있습니다.



```
#include <iostream>
using namespace std;

int sortedArr[10] = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100};
int n = sizeof(sortedArr) / sizeof(int);
bool pass = true;

void swap(int *x, int *y) {
    int temp;
    temp = *x;
    *x = *y;
    *y = temp;
}

void bubbleSort() {
    for (int k = n - 1 ; k > 0 && pass == true; k--) {
        pass = false;
        for (int i = 0; i < k; i++) {
            int j = i + 1;
            if (sortedArr[i] > sortedArr[j]) {
                swap(&sortedArr[i], &sortedArr[j]);
                pass = true;
            }
        }
    }
}

void out() {
    for (int i = 0; i < n; i++) {
        cout << sortedArr[i] << " ";
    }
    cout << endl;
}

int main(int argc, const char * argv[]) {
    bubbleSort();
    out();
    return 0;
}
```