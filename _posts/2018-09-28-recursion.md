---
layout: post
title: '재귀 함수'
date: 2018-09-28 11:30:37 +0900
categories: jekyll update
---

<br>
재귀 함수는 자기 자신을 다시 호출하는 함수를 말한다.

```javascript
const recursionFunc = () => {
  console.log('hello again!');
  return recursionFunc();
};

recursionFunc(); // hello again 이 끝없이 출력된다!
```

위의 함수가 재귀 함수이다. 다만 위의 함수는 재귀 종료 조건이 없어서 콜 스택이 가득 찰 때까지 자기자신을 호출하다가 에러를 발생시킨다.<br><br>

재귀 함수는 복잡한 알고리즘을 사람이 이해하기 쉽게 구현하도록 도와 준다. n 을 입력받아 n 번째 피보나치 수를 구하는 함수를 작성해보자.

```javascript
// 반복을 사용한 알고리즘
const fib = n => {
  let present = 0,
    before = 1;
  for (let i = 1; i <= n; i++) {
    [present, before] = [present + before, present];
  }
  return present;
};
```

```javascript
// 재귀를 사용한 알고리즘
const fib = n => {
  if (n <= 2) {
    return 1;
  }
  return fib(n - 1) + fib(n - 2);
};
```

재귀를 사용하면 한결 간결하고 직관적으로 함수를 작성할 수 있다. <br>
하지만 대부분의 경우 반복을 사용한 경우보다 비효율적이기 때문에 특수한 몇몇 경우를 제외하고는 반복을 사용하는것이 낫다. 위의 알고리즘도 사람이 보기에는 밑에 것이 낫지만, n 이 조금만 커져도 아래쪽 함수는 컴퓨터가 뻗어버린다(n=5 일 경우 위의 함수는 5 번의 연산을 하지만 밑의 함수는 1 + 1 + 2 + 3 + 5 = 12 번의 연산을 한다. n 이 더 커지면 연산숫자가 급격하게 증가한다. 거기다 똑같은 f(1)의 계산을 몇번이나 반복하는지 생각해보자!).<br>

정렬 알고리즘 중 퀵 소트나 머지 소트등에 사용되기에 알고리즘을 배울 때 초반부에 배워두어야 한다.
