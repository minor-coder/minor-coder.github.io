---
title: "node.js 개념"
excerpt_separator: "<!--more-->"
categories:
  - node.js
tags:
  - node.js
  - study
header:
  overlay_image: https://images.unsplash.com/photo-1641736494173-e7d5775121b2?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8
  overlay_filter: 0.3
  show_overlay_excerpt: false
toc: true
toc_sticky: true
---

![node.js logo](https://blog.kakaocdn.net/dn/y1eTS/btqJn1bFA22/Svn0GlZPcnkzFFKFCOhvn0/img.png){: width="400px"}{: .align-center}

# node.js란?
> Chrome V8 Javascript 엔진으로 빌드된 Javascript 런타임.


## 노드의 내부 구조
~~~
┌──────────────────────┐
| Node.js Core Library |
├──────────────────────┤
|   Node.js Bindings   |
├──────────────────────┤
|    V8    |   libuv   |
└──────────────────────┘
~~~

노드는 `V8 Javascript` 엔진과 함께 `libuv` 라이브러리를 사용한다.
`libuv`라이브러리는 이벤트기반, 논 블록킹 I/O 모델을 구현한다.

---

## 이벤트 기반
> 이벤트 기반이란 이벤트가 발생할 때 미리 지정해둔 작업을 수행하는 방식.
>
> ex) 마우스 클릭, 네트워크 요청

이벤트 기반 시스템은 `이벤트 리스너`, `콜백함수`를 등록해 특정 이벤트가 발생할 때 실행 시킬 수 있다.

### 이벤트 루프
> 이벤트 기반 모델에서 여러 이벤트가 동시에 발생했을 때 어떤 순서로 `콜백함수`를 호출 할 지 판단함.
* Background : `setTimeout`같은 타이머나 이벤트 리스너들이 대기하는 곳 (여러 작업 동시 실행 가능)
* Tesk Queue : 이벤트 발생 후, Backgound에서 Tesk Queue로 `콜백함수`를 보냄 (일반적으로 완료된 순서대로 실행되지만, 순서가 바뀌는 경우도 있음) 

### 논 블로킹 I/O
Javascript의 코드는 동시에 실행될 수 없지만, Javascript에서 돌아가는 것이 아닌 I/O작업과 같은 것들은 동시에 처리가 가능하다.

`논 블로킹`이란 이전 작업이 끝나기 전까지 대기하지 않고 다음 작업을 수행하지만, 작업 순서에 따라 성능이 크기 달라진다.

또한, I/O작업이라라도 `논 블로킹` 방식으로 구현하지 않으면 의미가 없다. (setTimeout, setImmediate 함수)

~~~js
function longRunningTask(){
  console.log('작업 끝');
}

console.log('시작');
setTimeout(longRunningTask, 0); // longRunningTask()
console.log('다음 작업');
~~~

~~~
result

시작
다음작업
작업 끝
~~~

### 싱글 스레드
> `스레드`는 `프로세스` 내에서 실행되는 **흐름의 단위**이다. 
> `프로세스`는 `스레드`를 여러 개 생성해 작업을 동시에 처리할 수 있고, `스레드`는 `부모 프로세스`의 자원을 공유한다. (같은 주소 메모리 접근, 데이터 공유 가능)

사실 **node.js**는 `싱글 스레드`가 아니지만, 사용자가 일반적으로 직접 제어할 수 있는 `스레드`는 하나 뿐이라 **node.js는 싱글 스레드**라고 여겨진다.
<br>(다만, `스레드풀`, `워커 스레드`로 `멀티 스레드`를 사용 할 수 있다.)

| 멀티 스레딩 | 멀티 프로세싱 |
|:--------|:-------:|
| 한 개의 프로세스, 여러 개의 스레드 | 여러 개의 프로세스 |
| CPU 작업이 많을 때 사용   | I/O요청이 많을 때 사용 |
| 프로그래밍이 어려움 | 프로그래밍이 비교적 쉬움|

---

## 서버로서의 노드
