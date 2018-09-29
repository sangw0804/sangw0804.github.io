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

퀵소트의 시간 복잡도는 O(n \* log n)이다(반으로 나누어 가며 시간복잡도 O(n)인 getPivot 함수를 호출하므로). 다만 pivot 값에 따라 성능이 달라질 수 있는데, 최악의 경우 매번 가장 큰 or 가장 작은 값을 선택하게 되면 O(n ^ 2)까지 시간 복잡도가 커질수도 있다. 따라서 pivot 값을 잘 정하는 것이 퀵소트의 성능에 중요한 요소인데, 위의 알고리즘에서는 그냥 배열의 첫 번째 요소값을 사용했지만 난수를 사용하거나 세 값(첫번째, 중앙값, 마지막)의 중위법을 이용해 분할하기도 한다.<br><br>

퀵 정렬은 설계 구조상 컴퓨터 아키텍처에 최적화되어 있기에 다른 O(n \* log n) 정렬들보다 빠르게 작동한다. 그래서 대부분의 라이브러리는 퀵 정렬을 사용한다.
