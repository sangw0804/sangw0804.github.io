---
layout: post
title: 'Async & Await'
date: 2018-09-30 13:30:37 +0900
categories: javascript ES7
---

## ES8 , async / await

콜백 함수가 ES6 에서 프로미스로 바뀌고 2 년이 지났다. 그리고 ES8 에서 새로운 async / await 가 나오면서 자바스크립트의 비동기 처리 방법이 다시한번 큰 변화를 맞이했다. <br>
async / await 에 대해 몇가지 짚고 넘어갈 것들이 있다.

- async / await 는 프로미스와 다른 개념이 아닌, 프로미스를 더욱 업그레이드한 방식이다.
- await 는 async 함수 안에서만 사용할 수 있다.
- async / await 는 비동기 로직을 마지 동기처럼 만들어준다.

코드를 살펴보자.

```javascript
const url = 'url';
fetchData(url)
  .then(response => {
    return anotherRequest(response);
  })
  .then(data => {
    console.log(data, 'success!');
  })
  .catch(e => {
    console.log(e, 'error!');
  });
```

기존의 프로미스 처리 방식은 위와 같았다. 콜백 지옥과 비교하면 나은 방법이었지만, 여전히 then 을 여러번 걸어야 한다는 점에서 동기 코드에 비해 가독성이 떨어진다.<br>
그렇다면 async / await 를 사용하면 위의 코드가 어떻게 바뀔까?

```javascript
const url = 'url';
const asyncFunc = async url => {
  try {
    const response = await fetchData(url);
    const data = await anotherRequest(response);
    console.log(data, 'success!');
  } catch (e) {
    console.log(e, 'error!');
  }
};

asyncFunc(url);
```

우선 간단히 async / await 의 문법에 대해 설명하면, 모든 함수는 정의할 때 앞에 async 를 붙여 async 함수로 만들 수 있다. async 함수는 무조건 async 함수가 리턴하는 값을 resolve 하는 Promise 를 리턴하게 된다. 위의 asyncFunc 는 아무것도 리턴하지 않으므로 undefined 를 resolve 하는 Promise 를 리턴한다.(만약 async 함수 안에서 에러가 발생할 경우 해당 에러를 reject 하는 Promise 를 리턴한다. )<br>
await 는 async 함수 안에서만 사용할 수 있는데, 프로미스 앞에 사용하면 해당 프로미스가 fulfill 될 때까지 기다렸다가 fulfill 된 값을 리턴한다. 즉 위에서 fetchDate()가 프로미스를 리턴하므로 해당 프로미스가 fulfill 될때까지 기다려서 값만을 리턴해 response 라는 변수에 할당하는 것이다.(만약 await 한 Promise 가 reject 될 경우 에러를 던진다.)<br><br>

그렇다면 async / await 에서 에러는 어떻게 처리할까? 위의 코드에도 나와 있듯, 일반적 동기 코드처럼 try catch 문을 사용하면 된다!!<br>
그렇게 하므로써 해결 할 수 있는 문제가 또 있다.

```javascript
// ES6
try {
  fetchData(url).then(data => {
    throwErrorFunc(data); // then 블록 안에서 발생하는 에러는 바깥쪽 try문이 핸들링하지 못한다!!!
    console.log(data);
  }); // 아래쪽 catch함수를 체이닝해서 핸들링해야 한다.
  // .catch(e => {
  //   console.log(e);
  // });
} catch (e) {
  console.log('I cannot catch the Error!!');
}

// ES8
const awesome = async () => {
  try {
    const data = await fetchData(url);
    throwErrorFunc(data); // 여기서 발생한 애러는 try문이 핸들링할 수 있다.
    console.log(data);
  } catch (e) {
    console.log('now I can catch the Error!!');
  }
};
```

위의 ES6 부분과 같이 에러 처리를 중복해서 해줘야하는 문제가 있었는데, 이 역시 try catch 로 해결할 수 있게 되었다.<br><br>

여러분이 promise 를 사용해서 개발하다 보면 다음과 같은 문제를 자주 마주했을 것이다.

```javascript
authenticateUser(id, password)
  .then(userId => {
    return getPosts(userId); // userId는 이 then블록 안에서만 접근할 수 있네...
  })
  .then(posts => {
    return createPost(posts[0].article, userId); // 앗, 두번째 인자로 userId가 필요한데...
  })
  .then(res => {
    console.log(res);
  })
  .catch(e => {
    console.log(e);
  });
```

그래서 아래와 같이 전역변수를 사용한, 작동은 되지만 보기 좋지 않은 코드를 써야 했다.

```javascript
const userId; // 전역변수 선언

authenticateUser(id, password)
  .then(tempUserId => {
    userId = tempUserId; //전역변수에 저장해둔다.
    return getPosts(tempUserId);
  })
  .then(posts => {
    return createPost(posts[0].article, userId); // 일단 이렇게 쓰자...
  })
  .then(res => {
    console.log(res);
  })
  .catch(e => {
    console.log(e);
  });
```

하지만 ES8 부터는 아래와 같이 깔끔하게 해결된다!

```javascript
const newVersion = async () => {
  try {
    const userId = await authenticateUser(id, password);
    const posts = await getPosts(userId);
    const res = await createPost(posts[0].article, userId);
    console.log(res);
  } catch (e) {
    console.log(e);
  }
};
```

## 결론

node.js 는 8 버젼부터 async / await 를 완벽하게 지원한다. 앞으로는 최대한 async / await 를 사용하도록 하자!
