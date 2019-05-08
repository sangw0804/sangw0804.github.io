---
layout: post
title: 'JS 자료구조 라이브러리 & 시각화'
date: 2019-02-27 22:30:37 +0900
categories: JS datastructure
---

비록 수상은 하지 못했지만, 2달간 열심히 작업했던 내용을 정리해놓으려 한다.

<img src='/assets/images/campus-fest-main.png'>

## Data-Structure JS

[깃헙 링크](https://github.com/sangw0804/data_structure_JS)

1. 자바스크립트 자료구조 & 시각화 라이브러리

2. 자료구조 - 개발 목적

3. 시각화 - 교육 목적

<img src='/assets/images/campus-fest-second.png'>

### 자료구조 추가 : B - Tree

<img src='/assets/images/campus-fest-btree.png'>

### 애니메이션 구현 : JS 제너레이터

처음 만들었던 프로젝트는 매번 DOM 엘리먼트를 완전히 날리고 새로 구성하는 방식이라 애니메이션 구현이 불가능했다.

```javascript

// insert

const insertButton = document.getElementById('insert_button');
const insertInput = document.getElementById('insert_value');
insertButton.onclick = () => {
  try {
    if (!insertInput.value.length) throw new Error('채워지지 않은 필드가 있습니다.');
    bst.insert(+insertInput.value);
    main.innerHTML = null; // 기존 DOM을 완전히 날리고
    main.appendChild(createBinarySearchTreeElement(bst)); // 새 DOM울 삽입
  } catch (e) {
    alert(e);
  }
};

```

처음에는 snapshots 라는 배열을 활용해 진행 과정을 저장한 뒤, 하나씩 순차적으로 구현해보았다.
<br>
그러나 문제점이 발생하였다.

> 1. snapshot 객체들을 만들고 관리하는 비용
> 2. snapshot 관련 로직들이 기존 로직들과 섞이게 됨.

이를 해결하기 위해 자바스크립트 제너레이터를 사용하기로 했다.

```javascript

// 제너레이터 코드
*insertGen(value) {
  // value 가 number 타입이 아니면 에러 발생.
  if (typeof value !== 'number') throw new Error(INVALID_VALUE);

  const newNode = new BinaryTreeNode(value);

  if (!this.root) {
    // 빈 bst 인 경우
    this.root = newNode;
  } else {
    // node 가 존재하는 bst 인 경우
    let current = this.root;

    while (true) {
      current.colored = 'blue';
      yield this;
      delete current.colored;

      if (value < current.value) {
        // 현재 노드의 value > 주어진 value 인 경우
        if (current.leftChild) {
          current = current.leftChild;
        } else {
          current.leftChild = newNode;
          break;
        }
      } else if (value > current.value) {
        // 현재 노드의 value < 주어진 value 인 경우
        if (current.rightChild) {
          current = current.rightChild;
        } else {
          current.rightChild = newNode;
          break;
        }
      } else {
        // 현재 노드의 value === 주어진 value 인 경우
        throw new Error(EXIST_VALUE);
      }
    }
  }

  this.length += 1;
  return this;
}

// DOM 렌더링 작업
const insertButton = document.getElementById('insert_button');
const insertInput = document.getElementById('insert_value');
insertButton.onclick = buttonDisableHOC(async event => {
  try {
    if (!insertInput.value.length) throw new Error('채워지지 않은 필드가 있습니다.');

    const iter = bst.insertGen(+insertInput.value);

    for (let snapshot of iter) {
      await delayAndApply(main, createBinarySearchTreeElement(snapshot), delayTime);
      lines.forEach(l => l.remove());
      lines = drawLineBST(main.firstChild);
    }

    await delayAndApply(main, createBinarySearchTreeElement(bst), delayTime);
    lines.forEach(l => l.remove());
    lines = drawLineBST(main.firstChild);
  } catch (e) {
    alert(e);
  }
}, 'INSERT');

```

이렇게 제너레이터를 사용하므로써 여러 문제들을 해결할 수 있었다. <br>
우선 snapshots 객체들을 따로 만들고 관리할 필요가 없어졌고, <br>
제너레이터 코드와 기존 자료구조 코드를 완전히 분리해서 작성하게 되었으며, <br>
모든 snapshots를 한번에 만들면서 발생했던 성능 이슈도 해결되었다.

### 문서화 : JS-DOC

js-doc을 사용하여 라이브러리를 문서화 하였다. 멘토님이 특히 강조하신 부분이었다.

[javascript-datasturcture 문서 페이지](https://sangw0804.github.io/data_structure_JS_doc/)

<img src='/assets/images/campus-fest-jsdoc.png'>

```javascript

/**
   * dequeue 메소드의 진행 상태를 generate 하는 제너레이터 함수.
   * @generator
   * @param {BTreeNode} parent
   * @param {number} index
   * @yields {BTree} 진행 상태가 시각적으로 표시된 자기 자신.
   * @returns {boolean} 빌려 오는 것이 가능한지 여부를 리턴한다.
   */
  *_borrowKeyGen(parent, index) {
    let fromIndex = index + 1;
    if (index === parent.size()) fromIndex = index - 1;

    const from = parent.leftChildOf(fromIndex);
    const to = parent.leftChildOf(index);

    if (from.size() <= Math.floor(this.limit / 2)) {
      return false;
    }
    yield true;
  
  ...


```

위와 같이 주석을 달아주면 js-doc이 자동으로 문서를 만들어 주며, 주석 자체도 메소드를 잘 설명해준다.

## 결과

비록 결승전에서 수상은 하지 못했지만, 오픈 소스, 프로젝트 관리, 제너레이터와 문서화, 발표(?) 등 정말 많은 것을 네이버의 멘토님과, 같이 조언을 주고 받았던 참가자들에게 배울 수 있었다. <br>
다음에도 기회가 된다면 또 참여하고 싶을 정도로 좋은 경험이었다.

<img src='/assets/images/campus-fest-last.png'>
<img src='/assets/images/campus-fest-last2.jpg'>