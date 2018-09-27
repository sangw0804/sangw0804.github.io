---
layout: post
title: 'Big-O 표기법과 시간 복잡도'
date: 2018-09-01 20:30:37 +0900
categories: jekyll update
---

## 1. 알고리즘 평가의 필요성

한 가지 문제를 푸는 방법은 여러가지가 있을 수 있다. 예를 들어, 1 부터 N 까지 수들의 합을 구하는 알고리즘을 작성한다고 하자. <br>

```javascript
const argo1 = num => {
  let sum = 0;
  for (let i = 0; i < num; i++) {
    sum += i;
  }
  return sum;
};

const argo2 = num => {
  return (n * (n + 1)) / 2;
};
```

위의 두 가지 알고리즘 중 어느 것이 더 나은 알고리즘일까?

이런 상황에서 더 나은 알고리즘을 결정해야 하는 필요성이 생겼고, 이에 따라 등장한것이 시간, 공간 복잡도와 Big-O 표기법이다.

##2. 시간, 공간 복잡도
