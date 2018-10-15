---
layout: post
title: 'Hash Table'
date: 2018-10-15 23:10:37 +0900
categories: programming data-structure
---

Hash Table 은 key : value 쌍으로 이루어진 자료구조다. array 처럼 대부분의 프로그래밍 언어에 내장되어있으며(자바스크립트의 object, 파이썬의 dictionary 등), 그 편리함과 속도 때문에 매우 빈번하게 사용되는 자료구조이기도 하다.<br>
hash Table 은 크게 key 를 value 의 인덱스로 hashing 하는 hash function 과 실제 값들이 저장되어 있는 bucket 으로 이루어져 있다. 우선 hash function 부터 구현해 보자.

```javascript
// key는 문자열로 제한한다.
const hashing = (key, size = 11) => {
  // size는 실제 값이 저장될 bucket 크기를 말한다. bucket이 클수록 많은 정보를 충돌없이 저장할 수 있지만 실제 정보량에 비해 너무 큰 bucket은 낭비이니 잘 조절하도록 하자.
  let index = 0;
  for (const a of key) {
    index = (index + a.charCodeAt()) % size; // index값이 size이상이 되지 않게 한다.
  }

  return index;
};
```

hash function 은 책 한권이 나올만큼 깊은 주제이지만, 여기서는 그냥 key 문자열의 아스키코드값을 더하는 간단한 hash function 으로 구현하였다.<br>

> hash 함수의 기본 조건
>
> 1. 시간복잡도가 낮아야 한다.
> 2. 같은 key 값을 입력받으면 항상 같은 index 를 출력해야 한다.
> 3. 최대한 index 값을 고르게 분포시켜야 한다.

사실 위의 hash function 은 key 의 크기만큼 반복하는 O(n) 알고리즘이라 1 번 조건부터 탈락하지만, 여기서는 key 문자열을 그렇게 길게 만들 일은 없으므로 넘어가자.(일반적으로 내장 hashTable 을 쓰지않고 직접 구현하는 경우는 거의 없다)<br>
bucket 도 마저 구현해 보자.

```javascript
class HashTable {
  constructor(size = 11) {
    this.buckets = new Array(size);
  }

  _hashing(key) {
    let index = 0;
    for (const a of key) {
      index = (index + a.charCodeAt()) % this.buckets.length;
    }
    return index;
  }
}
```

이제 우리의 Hash Table 자료구조가 생겼다. 그렇다면 삽입과 접근은 어떻게 할까?<br>
삽입을 구현하기 전 알아야 할 게 한가지 있다. 바로 collision 에 관한 것이다.<br>
한글로는 '충돌'인 이 용어는, hash 함수가 서로다른 key 값을 같은 index 로 hashing 해서 두 value 가 같은 bucket 에 저장되는 상황을 나타낸다. collision 에 대한 대처법으로 대표적인 2 가지가 있다.

1. Chaining : 해당 bucket 에 연결리스트/배열 추가로 다른 메모리를 연결해, 계속 정보를 저장하는 방식으로 주소값이 바뀌지 않는다.(close addressing)
2. Open addressing : 다른 버킷에 정보를 저장하는 방식으로 주소값이 달라진다. 다른 빈 주소를 찾는 방식도 선형/제곱 탐색, 이중 해시 등 여러가지가 있다.

먼저 체이닝 방식으로 구현해 보자.

```javascript
class HashTable {
  constructor(size = 11) {
    this.buckets = new Array(size);
  }

  _hashing(key) {
    let index = 0;
    for (const a of key) {
      index = (index + a.charCodeAt()) % this.buckets.length;
    }
    return index;
  }

  set(key, value) {
    const index = this._hashing(key); // key를 해쉬함수에 넣는다.
    if (!this.buckets[index]) this.buckets[index] = []; // 만약 해당 bucket에 아무것도 없다면 빈 배열을 할당한다.
    this.buckets[index].push([key, value]);
  }

  get(key) {
    const index = this._hashing(key);
    for (const slot of this.buckets[index]) {
      // 해당 버켓의 slot들을 차례로 확인한다.
      if (slot[0] === key) return slot[1]; // 해당 key가 있으면 value를 리턴한다.
    }
    return undefined; // key가 없으면 undefined를 리턴한다.
  }
}
```

이번에는 오픈 어드레싱 방식으로 구현해 보자.

```javascript
class HashTable {
  constructor(size = 11) {
    this.buckets = new Array(size);
  }

  _hashing(key) {
    let index = 0;
    for (const a of key) {
      index = (index + a.charCodeAt()) % this.buckets.length;
    }
    return index;
  }

  _anotherHashing(key) {
    // 이중 해싱에 사용할 또다른 해시 함수
    //...
  }

  set(key, value) {
    const index = this._hashing(key); // key를 해쉬함수에 넣는다.
    while (this.buckets[index]) {
      // 아래 방법들 중 하나를 사용하면 된다.
      index = (index + 1) % this.buckets.length; // 선형 탐색
      index = (index * index) % this.buckets.length; // 제곱 탐색
      index = this._anotherHashing(index) % this.buckets.length; // 이중 해싱
    }
    this.buckets[index] = [key, value];
  }

  get(key) {
    const index = this._hashing(key);
    let slot = this.buckets[index];
    while (slot) {
      if (slot[0] === key) return slot[1]; // key가 맞으면 value를 리턴한다.
      // set 메소드에서 사용했던 탐색 방법을 그대로 사용한다.
      index = (index + 1) % this.buckets.length; // 선형 탐색
      index = (index * index) % this.buckets.length; // 제곱 탐색
      index = this._anotherHashing(index) % this.buckets.length; // 이중 해싱
    }
    return undefined; // key가 없으면 undefined를 리턴한다.
  }
}
```

오픈 어드레싱 방식은 체이닝에 비해 구현이 쉽고 메모리를 적게 사용한다는 장점이 있으나, 값들이 한곳에 집중되는 클러스터링이 쉽게 발생한다는 단점이 있다.<br>
해시 테이블의 삽입/삭제/검색(key 값으로) 의 시간 복잡도는 해시 함수와 테이블 구조에 따라 다르겠지만 보통 O(1)로, 매우 빠른 시간 복잡도를 자랑한다.<br>
collision 을 줄이는 것이 성능에 중요한 요소인데, bucket 의 수를 소수로 정하는 것만으로 collision 이 상당히 감소하게 되니 bucket 의 수는 무조건 소수로 하자.
