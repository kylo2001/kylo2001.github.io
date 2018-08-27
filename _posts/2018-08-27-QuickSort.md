---
layout: post
title:  "퀵 정렬"
date:   2018-08-27 4:05:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true


---



## 퀵 정렬(Quick sort)

퀵 정렬 또한 분할 정복 알고리즘 기술의 좋은 예입니다. 퀵 정렬은 분할 교환 정렬(partition exchange sort)이라고도 하며 요소들을 정렬하기 위해 재귀 호출을 사용합니다. 비교 기반 정렬 알고리즘 가운데 가장 유명한 알고리즘 중 하나입니다.

* 분할 : 정렬하고자 하는 배열 A[low ~ high]을 A[low ~ q]의 각 요소가 A[q+1 ~  high]의 각 요소보다 작고나 같도록 두 개의 부분 배열 A[low ~ q], A[q+1 ~  high]로 나눕니다. 분할 기준인 인덱스 q(피봇)는 분할 과정(paritioning procedure)에서 정의됩니다. 
* 정복 : 두 부분 배열 A[low ~ q]와 A[q+1 ~  high]은 정렬을 위해 다시 퀵 정렬을 재귀 호출합니다.



##### 알고리즘

재귀 호출 알고리즘은 4단계의 절차로 구성됩니다.

1. 배열에 정렬된 요소가 하나 이하일 때 return 합니다.
2. 피봇(pivot) 지점 역할을 하는 요소를 배열에서 선택합니다(일반적으로 배열에서 가장 안쪽에 있는 요소가 사용됩니다).
3. 배열을 두 개의 부분으로 분할합니다. 하나는 피봇 값보다 큰 요소들이고 다른 하나는 피봇 값보다 작은 요소들이 됩니다.
4. 원래 배열을 양분한 두 배열에 대해 재귀적으로 알고리즘을 반복합니다.



![](https://user-images.githubusercontent.com/20294786/44643559-e5b4e580-aa0b-11e8-8285-f41ffec785a0.jpeg)



##### 분석

퀵 정렬의 복잡도를 T(n), 정렬할 배열 내 모든 요소들이 서로 다르다고 가정해봅시다. T(n)에서의 반복은 둘로 나뉘어진 부분 배열의 크기에 달려있습니다. 피봇이 i번째 작은 요소일 때 i 번째 요소를 기준으로 (i-1)번째 요소는 외쪽 부분 집합에 속하고 (n+i)번째 요소는 오른쪽 부분 집합에 속하게 될 것입니다. 이것을 i-분할(i-split)이라고 합시다. 배열의 각 요소들은 비폿 값으로 선택될 가능성이 동일하므로 i번째 요소가 선택될 사능성은 1/n 입니다.



##### 성능

- 최악의 경우 복잡도 : O(n^2)
- 최선의 경우 복잡도 : O(nlogn)
- 평균 복잡도 : O(nlogn)
- 최악의 경우  공간 복잡도 : 보조 공간 O(1)



##### 순서도

![](https://user-images.githubusercontent.com/20294786/44640058-53f0ac80-a9fa-11e8-8518-8005c932d0d2.jpeg)



##### C++ 구현 코드

```
#include <iostream>
using namespace std;

void out(int *array, int n) {
    for (int i = 0; i < n; ++i) cout << array[i] << " ";
    cout << endl;
}

void quickSort(int *array, int left, int right) {
    int i = left;
    int j = right;
    int pivot = array[(i + j) / 2];
    int temp;
    
    while (i <= j) {
        while (array[i] < pivot) i++;
        while (array[j] > pivot) j--;
        if (i <= j) {
            temp = array[i];
            array[i] = array[j];
            array[j] = temp;
            i++;
            j--;
        }
    }
    
    if (j > left) quickSort(array, left, j);
    if (i < right) quickSort(array, i, right);
}

int main(int argc, const char * argv[]) {
    int array[10] = {30, 20, 10, 50, 40, 60, 100, 80, 90, 70};
    int n = sizeof(array)/sizeof(int);
    
    quickSort(array, 0, n);
    out(array, n);
    return 0;
}
```