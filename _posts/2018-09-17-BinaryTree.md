---
layout: post
title:  "이진 트리(Binary Tree)"
date:   2018-09-17 16:04:00
categories: algorithm
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
comments: true
---

## 이진 트리(Binary Tree)

##### 이진 트리의 특징

- 이진 트리에서 각 노드는 최대 2개의 자식을 가진다.

- 각각의 자식 노드는 자신이 부모의 왼쪽 자식인지, 오른쪽 자식인지가 지정된다.(자식이 한명인 경우에도)


##### 이진 트리의 종류

- Full Binary Tree
- Complete Binary Tree

![](https://user-images.githubusercontent.com/20294786/45607576-563da800-ba88-11e8-8fc1-cf6f56d5e813.png)

높이가 h인 full binary tree는 2^h - 1 개의 노드를 가진다.

노드가 n개인 Full 혹은 Complete 이진 트리의 높이는 O(logN)이다.(노드가 N개인 이진트리의 높이는 최악의 경우 N이 될 수도 있다.)



##### 이진 트리의 표현

- 연결구조(linked structure) 표현

  ![](https://user-images.githubusercontent.com/20294786/45607705-33f85a00-ba89-11e8-8fe2-0ca491482897.png)

  각 노드에 하나의 데이터 필드와 왼쪽자식(left), 오른쪽 자식(right), 그리고 부모노드(p)의 주소를 저장(부모노드의 주소는 반드시 필요한 경우가 아니면 보통 생략 함)


##### 이진 트리 예시

![](https://user-images.githubusercontent.com/20294786/45607804-bf71eb00-ba89-11e8-9894-786bf2b91c86.png)



##### 이진 트리의 순회(Traversal)

순회 : 이진 트리의 모든 노드를 방문하는 일

* 중순위(inorder) 순회
* 선순위(preorder) 순회
* 후순위(postorder) 순회
* 레벨오더(level - order) 순회



##### 중순위 순회(Inorder Traversal)

![](https://user-images.githubusercontent.com/20294786/45608492-2d201600-ba8e-11e8-8b93-dcbd20460558.png)



그림과 같이 부모 노드를 기준으로 계속해서 왼쪽 자식과 오른쪽 자식을 inorder로 순회하는것 이 재귀적으로 진행이 됩니다.



inorder 순회가 진행되는 과정은 다음과 같습니다.

![](https://user-images.githubusercontent.com/20294786/45608589-bb949780-ba8e-11e8-83a9-6c1d545a67d2.png)



indrder 방식의 순회를 수도 코드로 나타내면 다음과 같습니다.

![](https://user-images.githubusercontent.com/20294786/45608671-25ad3c80-ba8f-11e8-892b-26535ebe18bb.png)



선순위와 후순위 순회는 이 중순위 순회와 아주 유사합니다. 다른 점이라고는 단지 출력해주는 순서가 다르다는 것 뿐입니다.



아래에서 선순위 순회와 후순위 순회의 수도 코드를 살펴본다면 이해할 수 있을 것 입니다.



##### 선순위 순회(Preorder Traversal)

![](https://user-images.githubusercontent.com/20294786/45608917-63f72b80-ba90-11e8-958e-5899e42988c9.png)



##### 후순위 순회(Postorder Traversal)

![](https://user-images.githubusercontent.com/20294786/45608918-648fc200-ba90-11e8-8ac7-ee295de01447.png)



##### 레벨 순위 순회(Level - order Traversal)

레벨 순으로 방문, 동일 레벨에서는 왼쪽에서 오른쪽 순서입니다. 

큐(queue)를 이용하여 구현합니다.



![](https://user-images.githubusercontent.com/20294786/45609049-2b0b8680-ba91-11e8-8036-eab627d4a7e1.png)



레벨 순위 순회의 수도 코드는 아래와 같습니다.

![](https://user-images.githubusercontent.com/20294786/45609203-d3214f80-ba91-11e8-87ed-48af4c41b6dc.png)
