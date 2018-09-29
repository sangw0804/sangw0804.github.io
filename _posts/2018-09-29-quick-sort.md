---
layout: post
title: 'Quick Sort'
date: 2018-09-29 10:30:37 +0900
categories: programming algorithm
---

퀵소트는 머지소트와 같은 O(n \* log n)의 시간복잡도를 지닌 정렬 알고리즘이다.

1. 배열에서 랜덤하게 하나의 pivot 을 정한 뒤, 그 pivot 보다 작은 값은 왼쪽으로, pivot 보다 큰 값은 오른쪽으로 이동시킨다.
2. 그 다음 pivot 의 왼쪽과 오른쪽에서 다시 pivot 을 정해 위의 과정을 반복한다.
3. 배열의 크기가 1 이 될 때까지 반복한다.

```javascript
// 배열과 범위를 입력받아 범위 안쪽의 배열을 pivot기준으로 정렬하고 pivot의 위치를 리턴한다.
const getPivot = (arr, start, end) => {
  let pivotIndex = start,
    pivot = arr[start]; // pivot은 배열의 첫번째 값으로 한다.
  for (let i = start + 1; i <= end; i++) {
    // 배열의 두번째 값부터 마지막 값까지
    if (arr[i] < pivot) {
      // 만약 pivot보다 작다면
      arr[pivotIndex] = arr[i]; // pivot 최종위치의 왼쪽으로 복사
      pivotIndex++; // pivot의 최종 위치를 1 올린다.
      arr[i] = arr[pivotIndex]; // pivot 현재위치의 오른쪽에 있던 (pivot보다 큰) 값을 옮겨진 값
    }
  }
  arr[pivotIndex] = pivot;
  return pivotIndex;
};

const quickSort = (arr, start = 0, end = arr.length - 1) => {
  if (start >= end) {
    return;
  }
  const pivot = getPivot(arr, start, end);
  quickSort(arr, start, pivot - 1);
  quickSort(arr, pivot + 1, end);

  return arr;
};
```
