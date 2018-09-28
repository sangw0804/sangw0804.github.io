---
layout: post
title: '탐색 알고리즘'
date: 2018-09-28 18:30:37 +0900
categories: programming algorithm
---

탐색 알고리즘이란, 주어진 자료들에서 원하는 자료가 있는지 여부를 검색하는데 사용하는 알고리즘이다. 실제 개발에서도 검색은 무척 많이 쓰이는 만큼 잘 공부해 두는것이 좋다.<br>

## 1. Linear Search

선형 탐색은 가장 기본적인 탐색 알고리즘이다. 쉽게 말해 데이터들을 하나씩 확인하는 방식이다. 시간복잡도는 O(n)이다.

```javascript
const linearSearch = (arr, target) => {
  for (let i = 0; i < arr.length; i++) {
    if (argu === target) {
      return i;
    }
  }
  return -1;
};
```

## 2. Binary Search

이진 탐색은 데이터들이 정렬되어 있을 때 사용 가능한 탐색 알고리즘이다. 마치 사전에서 단어를 찾을때와 비슷하다. "속독" 이라는 단어를 사전에서 찾는다면, 먼저 사전의 중간을 펼친다. 펼쳐서 나온 단어가 "완성" 이라고 하면, 'ㅅ'은 'ㅇ'보다 앞쪽에 있으므로 펼친 쪽의 앞쪽 부분의 절반을 다시 펼친다.... 이런식으로 "속독"이 나올때까지 찾는다. <br>
시간 복잡도는 O(log n)이다(계속 절반씩 나누어 가면서 찾으므로 32 -> 16 -> 8 -> 4 -> 2 -> 1 최대 log 32 = 5 번만 반복하면 된다!).

```javascript
const binarySearch = (sortedArr, target) => {
  let start = 0,
    end = sortedArr.length - 1,
    middle = Math.floor((start + end) / 2); // 처음과 끝의 중간!
  for (start <= end) {
    if (sortedArr[middle] === target) { // 발견한 경우
      return middle;
    } else if (sortedArr[middle] < target) { // 찾아야하는 값보다 작은 경우
      start = middle + 1;
    } else { // 찾아야하는 값보다 큰 경우
      end = middle - 1;
    }
  }
  return -1; // 존재하지 않는다.
};
```

## 3. String search

전체 문자열(길이 n)에서 특정 문자열(길이 m)을 탐색하는 알고리즘. 한글자 한글짜 맞춰가는 알고리즘으로 중간에 틀리게 되면 시작 문자열로 돌아가서 다시 탐색한다. 시간 복잡도는 O(n\*m)이다.

```javascript
const searchString = (str, subStr) => {
  outter: for (let i = 0; i < str.length; i++) {
    for (let j = 0; j < subStr.length; j++) {
      if (str[i + j] !== subStr[j]) {
        continue outter;
      }
    }
    return true;
  }
  return false;
};
```

## 4. KMP 알고리즘

KMP 알고리즘은 보다 향상된 문자열 검색 알고리즘으로, 만든 사람들의 이름(Knuth, Morris, Prett)을 따서 이름 붙여졌다.
