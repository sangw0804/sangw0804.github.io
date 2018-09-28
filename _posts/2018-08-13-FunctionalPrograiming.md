---
layout: post
title: '함수형 프로그래밍'
date: 2018-08-13 17:30:37 +0900
categories: programming
---

# 함수형 프로그래밍

> 함수형 프로그래밍은 순수한 함수를 작성하고, 공유된 상태와 변경 가능한 데이터 및 부작용을 피하여 소프트웨어를 작성하는 프로세스이다.
> 함수형 프로그래밍은 명령형이 선언형이며, 애플리케이션의 상태는 순수한 함수를 통해 전달된다.
> 애플리케이션의 상태가 공유되고, 객체의 메소드와 사용되는 객체 지향 프로그래밍과는 대조된다.

```javascript
// 객체지향 프로그래밍
class MyObj {
    constructor(family) {
        this.family = family;
    }

    function newKid(newChild) {
        return this.family.child = newChild;
    }
}

//함수형 프로그래밍
const family = {father:"James",mother:"Lara"}

const newKid = (family, newChild) => {
    return {...family, child: newChild};
};
```

## 1. Pure Function

> 부작용이 없고, 같은 입력을 받으면 같은 출력을 계속해서 리턴한다. 즉 외부에 의존성이 없다.

```javascript
/* pure function */
const double = num => num * 2;
/* impure function */
const operand = 2;
const multiple = num => num * operand;
```

## 2. 불변성

> 한번 메모리에 값을 할당하면 그 값을 참조해 변경하지 않는다. (_자바스크립트의 map, from, concat 등의 함수가 불변성의 예이다_)

```javascript
const obj = { x: 1, y: 2 };
const newObj = { ...obj, z: 3 };
console.log(obj); // { x: 1, y: 2 }
console.log(newObj); // { x: 1, y: 2, z: 3 }
```

# 3. 명령형 vs 선언형

> 함수형 프로그래밍은 선언형 프로그래밍의 한 분야이다. 선언형 프로그래밍은 필요한 것을 달성하기 위한 구체적인 단계를 하나하나 기술해 나가는 것 보다 필요한 것이 어떤 것인지, 데이터의 변화에 집중하는 스타일을 말한다.

```javascript
let originalNums = [1, 2, 3, 4, 5];

// 선언형 프로그래밍
let smallNums = original.map(num => num * 2);

// 명령형 프로그래밍
let smallNums = [];
for (let i = 0; i < originalNums.length; i++) {
  smallNums.push(originalNums[i] * 2);
}
```

## - 결론

- 공유 상태(Shared state)와 부작용(Side effects) 대신 순수 함수(Pure function) 사용하라
- 변경 가능한 데이터보다는 불변성(Immutability)을 따르라
- 명령형(Imperative) 흐름 제어보다는 합성 함수(Function composition) 사용하라
- 같은 곳에 있는 데이터에서만 작동하는 메서드 대신에 많은 데이터 유형에 대해 작업할 수 있도록 고차 함수(Higher order functions)를 사용하여 일반적이고 재사용 가능한 도구를 만들어라
