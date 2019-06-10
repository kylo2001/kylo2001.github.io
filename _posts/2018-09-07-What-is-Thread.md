---
layout: post
title:  " 쓰레드(Thread)란 무엇인가? "
categories: OS
tags: OS
author: goodGid
---
* content
{:toc}

## 쓰레드(Thread)란 무엇인가?

* 쓰레드란 프로그램(프로세스) 실행의 단위이며 <br> 하나의 프로세스는 **여러개의 쓰레드**로 구성이 가능하다.

* 하나의 프로세스를 구성하는 쓰레드들은 프로세스에 할당된 메모리, 자원 등을 공유한다.

* 프로세스와 같이 실행, 준비, 대기 등의 실행 상태를 가지며 <br> 실행 상태가 변할때마다 **쓰레드 문맥교환(context switching)**을 수행한다.

* 각 쓰레드별로 자신만의 **스택**과 **레지스터**를 가진다.

![](/assets/img/os/what_is_thread_1.png)

* 한순간에 하나의 쓰레드만이 실행 가능하다.











---

## 프로세스와 쓰레드의 차이

> 프로세스는 운영체제로부터 자원을 할당받는 작업의 단위이고 <br> 쓰레드는 프로세스가 할당받은 자원을 이용하는 실행의 단위이다.

* 프로세스는 **실행 중인 프로그램**으로 <br> 디스크로부터 메모리에 적재되어 CPU의 할당을 받을 수 있는 것을 말한다.

* 하지만 프로세스 생성은 많은 시간과 자원을 소비한다. 

<br>

* 쓰레드는 **프로세스**의 **실행 단위**라고 할 수 있다. 

* 한 프로세스 내에서 동작되는 여러 실행 흐름으로 <br> 프로세스 내의 주소 공간이나 자원을 공유할 수 있다. 

* 이 경우 각각의 쓰레드는 **독립적인 작업**을 수행해야 하기 때문에 각자의 **스택**과 **PC 레지스터 값**을 갖고 있다.


> 스택을 쓰레드마다 독립적으로 할당하는 이유

* 스택은 함수 호출 시 전달되는 인자, 되돌아갈 주소값 및 함수 내에서 선언하는 변수 등을 <br> 저장하기 위해 사용되는 메모리 공간이므로 <br> **스택 메모리 공간**이 **독립적**이라는 것은 <br> **독립적인 함수 호출**이 가능하다는 것이고 <br> 이는 **독립적인 실행 흐름**이 가능하게 한다. 

* 따라서 **독립적인 실행 흐름을 위한 최소 조건**으로 독립된 스택을 할당한다.

---

> PC Resister를 쓰레드마다 독립적으로 할당하는 이유

* PC 값은 쓰레드가 명령어의 어디까지 수행하였는지를 나타나게 된다. 

* 쓰레드는 CPU를 할당받았다가 **스케줄러**에 의해 다시 선점당한다. <br> 그렇기 때문에 명령어가 연속적으로 수행되지 못하고 <br> 어느 부분까지 수행했는지 기억할 필요가 있다. 

* 따라서 PC 레지스터를 독립적으로 할당한다.



---


## 쓰레드(Thread)의 장점

* 쓰레드는 프로세스보다 생성 및 종료시간, 쓰레드간 전환시간이 짧다.

* 쓰레드는 프로세스의 메모리, 자원등을 공유하므로 커널의 도움없이 **상호간에 통신**이 가능하다.


---

## 쓰레드 동기화 방법의 종류

* Mutex / Semaphore / Monitor <br> 공통점은 세가지 모두 운영체제의 동기화 기법이라는 것이다.

* 뮤텍스(Mutual Exclusion)
    - 쓰레드의 **동시 접근**을 허용하지 않는다는 의미. 
    - 뮤텍스의 쓰레드 동기화 방법은 **임계영역**에 들어가기 위해 이 **뮤텍스를 가지고 있어야** 들어갈 수 있다. <br> 예) 일종의 자물쇠와 같은 역할을 한다. <br> 임계영역에 들어간 쓰레드가 뮤텍스를 이용해 임계영역에서 본인이 나올때까지 다른 쓰레드가 못들어오게 내부에서 자물쇠로 잠근다.

* 세마포어(Semaphore)
    - 세마포어 역시 뮤텍스와 비슷한 역할을 하지만 <br> 세마포어는 동시 접근 동기화가 아닌 **접근 순서 동기화**에 더 관련있다.

* 모니터(Monitor) 
    - Mutex(Lock)와 Condition Variables(Queue라고도 함)을 가지고 있는 Synchronization 메카니즘이다. 

<br> 

* 우선 뮤텍스 / 모니터 / 세마포어는 개념적으로 차이가 있다.

* 전자(뮤텍스,모니터)는 **상호 배제**를 함으로써 임계구역에 **하나의 쓰레드만** 들어갈 수 있다.

* 후자(세마포어)는 **하나의 쓰레드(binary semaphore)**만 들어가거나 <br> 혹은 **여러 개의 쓰레드(counting semaphore)**가 들어가게 할 수도 있다.

<br>

> Q. 뮤텍스와 모니터의 차이는?

* 가장 큰 차이는 **뮤텍스**는 **다른 프로세스(애플리케이션)간**에 동기화를 위해 사용한다.

* 반면 **모니터**는 하나의 프로세스(애플리케이션)내에 **다른 쓰레드 간**에 동기화할 때 사용한다.

* 또한, **뮤텍스**는 보통 **운영체제 커널** 의해서 제공되는 반면에 <br> 모니터는 **프레임워크나 라이브러리** 그 자체에서 제공된다. 

* 따라서 뮤텍스는 **무겁고(heavy-weight) 느리며(slower)** <br> 모니터는 **가볍고(light-weight) 빠르다(faster)**.

> Q. 세마포어와 모니터의 차이는?

* Java에서는 모니터를 모든 객체에게 기본적으로 제공하고 있는 반면 C에서는 모니터를 사용할 수 없다.

* 세마포어는 **카운터라는 변수값**으로 프로그래머가 **상호 배제**나 **정렬**의 목적으로 사용 시 <br> 매번 값을 따로 지정해줘야하는 등 조금 번거롭다. 

* 반면, **모니터**는 이러한 일들이 **캡슐화** 되어 있어서(encapsulation) <br> 개발자는 카운터값을 1 또는 0으로 주어야 하는 고민을 할 필요 없이 <br> synchronized, wait(), notify() 등의 **키워드**를 이용해 좀 더 편하게 동기화할 수 있다.

> Q. 뮤텍스와 세마포의 차이는?

* 세마포어는 뮤텍스가 될 수 있지만 <br> 뮤텍스는 세마포어가 될 수 없다.

* 예를 들어 Casting을 한다고 보면 <br> (뮤텍스)세마포어 --> 가능 <br> (세마포어)뮤텍스 --> 불가능 

* 세마포어는 **소유할 수 없**는 반면 <br> 뮤텍스는 **소유할 수** 있고 소유자가 이에 책임을 진다.

* 뮤텍스는 **1개만** 동기화가 되지만 <br> 세마포어는 **하나 이상**을 동기화 할 수 있다.

```
변기가 하나뿐인 화장실에서는 
앞의 사람이 볼일을 마치고 나가야 다음 사람이 들어갈 수 있다. 
이렇게 한번에 오직 하나만 처리할 수 있는 대상에 사용하는 것이 뮤텍스이다. 

변기가 세개인 화장실에서는 동시에 세 사람이 볼일을 볼 수 있고 
이 세 사람 중 아무나 한명이 나오면 다음 사람이 볼일을 볼 수 있다. 
이렇게 동시에 제한된 수의 여러 처리가 가능하면 세마포어이다. 

만약 변기 세개짜리 화장실의 각 변기에 대해 뮤텍스를 사용한다면 
대기중인 사람은 각 변기 앞에 줄을 서는 것이고 
이렇게 되면 옆 칸이 비어도 들어가지 못하게 된다. 

만약 변기 세개를 묶어서 뮤텍스를 사용한다면 
변기 수에 관계없이 무조건 한명만 사용할 수 있게 된다. 

이 예에서 변기는 동기화 대상, 사람은 그 동기화 대상에 접근하는 쓰레드를 나타낸다. 

뮤텍스와 세마포어의 목적은 특정 동기화 대상이 이미 특정 쓰레드에 의해 사용중일 경우 
다른 쓰레드가 해당 동기화 대상에 접근하는 것을 제한하는 것으로 동일하지만 
관리하는 동기화 대상이 몇개 인가에 따라 차이가 생기게 되는 것이다.
```





---

## 참고

* [[운영체제 이론] 쓰레드(Thread)](http://arer.tistory.com/80)

* [멀티쓰레드란?](https://m.blog.naver.com/PostView.nhn?blogId=rja1104&logNo=220551216367&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)

* [[운영체제] 뮤텍스(Mutex) 세마포어(Semaphore) 모니터(Monitor)](http://about-myeong.tistory.com/34)

* [스핀 락(Spin lock), 크리티컬 섹션(Critical section), 세마포어(Semaphore), 뮤텍스(Mutex)](http://brownbears.tistory.com/45)

* [[OS]메모리 관점에서 본 쓰레드(thread)](https://mooneegee.blogspot.com/2015/01/os-thread.html)
