---
layout: post
title: '삽질일기 - ES6 함수형 반복문과 async await'
date: 2018-11-09 19:30:37 +0900
categories: es6 javascript async
---

ESlint는 모든 for 반복문을 foreach 계열의 함수형 반복문으로 고치라고 요구한다. 일반적인 상황에서는 문제가 없으나, 비동기 프로그래밍 상황에서는 매번 콜백 함수가 달리는 함수형 반복문이 예상과 다르게 작동할 수 있다. 드리머리 서버 코드를 리팩토링하던 중 그런 문제가 발생했다.

```javascript
//

const fileUpload = async files => {
  try {
    for (let fileType in files) {
      const urlList = await getListFromServer();
      const data = await s3Upload(files[fileType]);
      urlList.push(data.Link);
      await updateListToServer(urlList);
    }

    done();
  } catch (e) {
    errorHandling(e);
  }
};
```

물론 실제 코드는 위 보다 복잡하지만, 간단히 표현하면 저렇게 여러개의 파일을 받아 s3저장소에 파일을 저장하고 생성된 url을 받아서 데이타베이스의 url리스트에 추가하는 로직이었다. ESlint가 for문을 사용한 것에 불평을 하기에 그냥 foreach로 바꾸면 해결될 줄 알았다.

```javascript
const fileUpload = async files => {
  try {
    Object.keys(files).forEach(async fileType => {
      const urlList = await getListFromServer();
      const data = await s3Upload(files[fileType]);
      urlList.push(data.Link);
      await updateListToServer(urlList);
    });

    done();
  } catch (e) {
    errorHandling(e);
  }
};
```

허나 위의 코드는 매 await 동작이 foreach에 들어간 콜백 함수 내에서만 코드를 멈추게 된다. 따라서 1번파일 저장 -> 1번파일 url 디비에 업데이트 -> 2번파일 저장... 이런 순서가 아니라 1번파일 저장 -> 2번파일 저장 -> 3번파일 저장 -> 1번파일 url 디비에 업데이트 ... 이런 순서로 실행이 된다. 서로 비동기이기에 만약 2번파일을 저장하는 데 3번파일보다 오랜 시간이 걸리면 순서가 예상할 수 없게 되어버리는 것이었다.<br><br>

해결책은 Promise.all이었다.

```javascript
const fileUpload = async files => {
  try {
    const urlList = await getListFromServer();
    const promises = Object.keys(files).map(async fileType => await s3Upload(files[fileType]));

    const Links = await Promise.all(promises);
    await updateListToServer(urlList.concat(Links));

    done();
  } catch (e) {
    errorHandling(e);
  }
};
```

async 함수가 암시적으로 promise를 리턴한다는 것을 활용하면 코드를 저렇게나 간결하게 짤 수 있다.<br>
거기다 위 코드는 s3Upload를 병렬적으로 처리하기 때문에 시간도 횔씬 짧아진다. <br><br>

이것으로 1시간정도의 삽질을 마무리하며 느낀 것은, async await는 물론 편리하지만 promise, 그리고 비동기에 대한 이해가 높으면 더욱 잘 사용할 수 있다는 것이다. 매번 코드를 await 시키는 것만이 능사는 아니다. 오히려 비동기의 장점을 살리려면 병렬 처리가 필요한 부분이 많다. <br>
