---
layout: post
title: 'NPM - 노드 패키지 매니저'
date: 2018-08-29 17:30:37 +0900
categories: jekyll update
---

## 1. npm(node package manager)

---

https://docs.npmjs.com/getting-started/what-is-npm, 현재는 백엔드 패키지 뿐 아니라 프론트 엔드, 커맨드라인 툴 등 자바스크립트 패키지 전체를 다룸.
<br>
<br>

## 0. package vs module

---

> module : require() 로 import 할 수 있는 모든 자바스크립트 파일. 즉, 어딘가에서 export 가 명시되어 있어야 한다.<br> > <br>
> package : 자신을 설명하는 package.json 파일이 존재하는 자바스크립트 파일 또는 디렉토리

<br>
<br>

### 2. O P Q M

---

- Optimal : 종합점수
- Popularity : 얼마나 많이 다운로드 되었는가
- Quality : ReadMe.md 파일이 있는지, 안정성, 테스트 여부, 최신 모듈에 dependency 하고 있는지, 웹 사이트가 있는지, 코드 복잡도 등
- Maintainance : 얼마나 잘 유지되고 있는지, 업데이트 지속성
  <br>
  <br>

### 3. Command Line NPM #1

---

```java
npm init // package.json 파일 생성
npm install // 현재 package.json 파일을 확인해 dependencies들을 다운로드
npm install <패키지 이름> [--save] // 특정 패키지를 npm서버에서 다운로드, save 플래그를 적으면 package.json 에 dependencies 속성으로 추가
```

<br>
<br>

## 4. package.json

---

```javascript
{
  "name": "npmpractice", //영소문자, -_만 허용.
  "version": "1.0.0",
  "description": "hi", //npm 서버에서 다른 사용자들이 해당 패키지를 찾을때 보여주는 내용
  "main": "app.js", // 이 패키지를 export 하는 곳.
  "scripts": { // 이 안에 있는 명령어들을 커맨드라인에서 사용 가능
    "test": "echo \"Error: no test specified\" && exit 1" // npm run test
  },
  "author": "osw",
  "license": "ISC",
  "dependencies": {
    "cat-me": "^1.0.3" // 버젼관리
  }
}
```

만약 현재 버젼이 1.0.4 라면

- Patch Release accept : 1.0 or 1.0.x or ~1.0.4
- Minor Release accept : 1 or 1.x or ^1.0.4
- Major Release accept : \* or x

<br>
<br>

### 5. Command Line NPM #2

---

```javascript
npm ls // 현재 설치된 모듈들을 보여줌
npm outdated // 현재 설치된 모듈들의 업데이트 현황 확인
npm update // 업데이트!

npm uninstall <패키지 이름> [--save] // 특정 패키지 삭제, save 플래그 설정 시 package.json 의 dependencies 에서도 삭제!
npm prune //extraneous 패키지를 모두 삭제
```

<br>
<br>

## 6. package_lock.json?

---

> 현재 실제로 설치되어 있는 구체적 패키지들과 모듈들의 정확한 버젼정보가 있는 파일. compatibility 이슈 때문에 npm 5. 버전에 등장. 현재는 package.json 파일이 우선하기 때문에 크게 신경 쓸 필요가 없다.
