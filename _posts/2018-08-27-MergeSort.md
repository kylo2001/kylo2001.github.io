---
layout: post
title:  "병합 정렬"
date:   2018-08-27 4:05:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true


---



## 병합 정렬(Merge sort)

병합 정렬은 분할 정복의 좋은 예입니다.



##### 특징

* 병합(merging)은 두 개의 정렬된 파일을 하나의 정렬된 파일로 조합하는 과정입니다.
* 선택(selection)은 파일을 k개의 작은 요소를 가지는 부분과 n-k개의 많은 요소를 가지는 두 개의 부분 파일로 나누는 과정입니다.
* 선택과 병합은 서로 반대 과정입니다.
* 선택은 목록을 두 개의 목록으로 나눕니다.
* 병합은 두 개의 파일을 합쳐서 하나의 파일로 만듭니다.
* 병합 정렬은 퀵 정렬의 보완입니다.
* 병합 정렬은 순차적인 방식으로 데이터에 접근합니다.
* 이 알고리즘은 정렬에 연결 리스트를 사용합니다.
* 병합 정렬은 입력 목록의 사전 정렬에 영향을 받지 않습니다.
* 퀵 정렬에서 대부분의 작업은 재귀 호출이 일어나기 전에 처리됩니다. 퀵 정렬은 큰 부파일에서 시작해서 작은 부파일로 끝납니다. 따라서 스택이 필요하고 안정성을 가지지 않는 알고리즘입니다. 반면 병합 정렬은 정렬할 목록을 두 개의 부분으로 나누고 구 부분을 따로 따로 정렬합니다. 병합 정렬은 작은 부파일엣 시작하여 큰 부파일에서 끝납니다. 따라서 스택이 필요없으며 안정성을 가집니다. 



##### 알고리즘

1. 리스트의 길이가 1이 될때까지 반으로 잘게 **나눈다.-> 분할(Divide)**

2. 다 나누어 졌다면, 데이터를 합치는데(Merge), **정렬하면서** 합친다. 

   (작은 숫자를 앞에놓고 큰 숫자를 뒤에 놓으면 되겠죠?)



##### 분석

병합 정렬은 정렬할 목록을 두 부준으로 나누고 재귀적으로 처리합니다. 각 부분의 처리가 끝난 후 두 부분을 병합합니다. n개의 요소를 병합 정렬하는 복잡도를 T(n)이라 하면 병합 정렬을 위한 반복은 다음과 같이 정의됩니다.

```
병합 정렬을 위한 반복 T(n) = 2T(n/2) + Θ(n)
마스터 정리에 의해 T(n) = Θ(nlogn)을 얻습니다.
```



##### 성능

- 최악의 경우 복잡도 : O(nlogn)
- 최선의 경우 복잡도 : O(nlogn)
- 평균 복잡도 : O(nlogn)
- 최악의 경우  공간 복잡도 : 보조 공간 O(n)



##### 순서도

![](https://user-images.githubusercontent.com/20294786/44639947-ab424d00-a9f9-11e8-9669-c8fc13d3bf11.jpeg)



##### C++ 구현 코드

```
#include <iostream>
using namespace std;

void out(int *array, int n) {
    for (int i = 0; i < n; ++i) cout << array[i] << " ";
    cout << endl;
}

void merge(int *array, int left, int mid, int right) {
    int temp[right + 1];
    int i = left;
    int j = mid + 1;
    int k = 0;
    
    while (i <= mid && j <= right) {
        if (array[i] <= array[j]) temp[k++] = array[i++];
        else temp[k++] = array[j++];
    }
    while (i <= mid) temp[k++] = array[i++];
    while (j <= right)temp[k++] = array[j++];
    k--;
    while (k >= 0) {
        array[k + left] = temp[k];
        k--;
    }
}

void mergeSort(int *array, int left, int right) {
    int mid;
    
    if (left < right) {
        mid = (left + right) / 2;
        mergeSort(array, left, mid);
        mergeSort(array, mid + 1, right);
        merge(array, left, mid, right);
    }
}

int main(int argc, const char * argv[]) {
    int array[10] = {30, 20, 10, 50, 40, 60, 100, 80, 90, 70};
    int n = sizeof(array)/sizeof(int);
    
    mergeSort(array, 0, n - 1);
    out(array, n);
    return 0;
}
```

