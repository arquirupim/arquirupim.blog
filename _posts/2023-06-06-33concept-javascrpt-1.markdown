---
layout: post
title: "33 Concept Javascript(1)"
date: 2023-06-06 18:00:00 +0900
tags: Spring
description: 자바스크립트 33컨셉 -1 Call stack
---

# Javascript

Javascript는 싱글 쓰레드의 언어이며 이는 순간의 태스크나 코드조각에서 실행되는 것을 의미한다.

아래사진은 Javascript의 동시성 모델이다(V8기준)

![img]({{ '/assets/images/Visual Representation of JS Model.webp' | relative_url }}){: .center-image }_출처: [medium-
Gaurav Pandvia, <Understanding Javascript Function Executions — Call Stack, Event Loop , Tasks & more>, 2017-01]_

# Call stack

Javascript의 `call stack`은 웹 브라우저와 같은 인터프리터의 메카니즘이다.
call stack은 여러 함수의 stack으로 이해하면 좋다.

stack에 함수를 차례로 넣어두는 개념인데, 아래 예시로 보자.

{% highlight Javascript %}
function greeting() {
// [1] Some code here
sayHi();
// [2] Some code here
}
function sayHi() {
return "Hi!";
}

// Invoke the `greeting` function
greeting();

{% endhighlight %}

함수의 실행 순서는 greeting을 호출-> sayHi를 호출하는 식이다.
call stack에는 맨 처음 호출된 greeting이 들어가게 되고, 새로운 함수(여기서는 sayHi)를 호출하면 greeting, sayHi순으로 stack에 들어간다.

이때 stack이라고 말한것과 같이 FILO식으로 greeting을 실행하다가 sayHi함수를 실행할때 call stack에 sayHi를 담고 `실행이 끝나면 greeting의 해당부분으로 돌아온다.`

이로, 에러스택이 길게 나왔단것 또한 call stack을 순서대로 보여주기 때문으로 알 수 있다.

call stack의 크기는 최대 16,000 프레임이며 이를 초과할 수 없기에, loop문 등으로 넘게 되면 에러를 발생한다.(stack 크기를 초과했으므로 stack over flow)

# Heap

Javascript에서는 Object, Array의 실제 데이터가 이곳에 위치하는데 이는 다른 게시물에서 서술.

# Queue

JavaScript의 메세지 큐는 처리된 메시지와 콜백 함수와 관련된 실행을위한 리스트이다.
stack의 용량이 충분할때 연관된 함수와 메시지를 큐에서 꺼내 실행된다.(개별의 초기 스택을 만들어준다)
stack이 비게되면 메시지큐 또한 끝난다.

통상 메시지는 요청에의한 외부 응답(async, xhr)을 뜻한다.

# Event Loop

앞서 설명한 외부로의 요청에 대한 응답(AJAX 등)은 callback함수를 `나중에 실행`($.get, setTimeout, setInterval, etc)해야하는데 이러한 함수들의 실행은 기존 함수들의 실행과 달리 call stack에 즉시 넣을 수 없다.
이러한 비동기함수는 브라우저의 비동기 이벤트 처리를위해 만든 쓰레드에 보관된다.(이때 보통 C++로 구현되서 웹 개발자는 C++을 공부하라는 말이 있지 않나 싶다)
WebAPI는 call stack이 비워진후 콜백의 실행을 위해 큐에 집어넣는데 이를 `이벤트 루프`라고 한다.
이벤트 루프는 call stack과 task queue를 감시해 stack이 비었을때 queue의 내용물을 집어넣는 역할을한다.

![img]({{ '/assets/images/Javascript Event Loop Visual Representation.webp' | relative_url }}){: .center-image }_출처: [medium-
Gaurav Pandvia, <Understanding Javascript Function Executions — Call Stack, Event Loop , Tasks & more>, 2017-01]_

참고 자료.

[leonardomso, <33-js-concepts>],
[MDN, Call stack],
[Gaurav Pandvia, <Understanding Javascript Function Executions — Call Stack, Event Loop , Tasks & more>, 2017-01]

[leonardomso, <33-js-concepts>]: https://github.com/leonardomso/33-js-concepts
[MDN, Call stack]: https://developer.mozilla.org/en-US/docs/Glossary/Call_stack
[Gaurav Pandvia, <Understanding Javascript Function Executions — Call Stack, Event Loop , Tasks & more>, 2017-01]: https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec
