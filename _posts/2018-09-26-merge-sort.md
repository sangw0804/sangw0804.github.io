---
layout: post
title: 'Merge Sort'
date: 2018-09-26 18:30:37 +0900
categories: programming algorithm
---

Merge Sort(합병 정렬)는 O(n \* log n)의 시간복잡도를 갖는 정렬 알고리즘으로, <br>

1. 정렬된 두 개의 배열을 합치면서 정렬하는 것은 O(n+m)의 시간 복잡도를 지닌다. <br>
2. 요소가 1 개인 배열은 정렬된 배열이다. <br>

위의 두 가지를 이용해 구현한다.
우선 주어진 배열을 길이가 1 이 될 때까지 나눈 뒤, 나누어진 배열들을 합치면서 정렬하는 방법이다.

```javascript
// 두개의 정렬된 배열을 합치면서 정렬하는 함수
const merge = (arr1, arr2) => {
  let merged = [];
  let i, j;
  for (i = 0, j = 0; i < arr1.length && j < arr2.length; ) {
    if (arr1[i] < arr2[j]) {
      merged.push(arr1[i]);
      i++;
    } else {
      merged.push(arr2[j]);
      j++;
    }
  }
  for (; i < arr1.length; i++) {
    merged.push(arr1[i]);
  }
  for (; j < arr2.length; j++) {
    merged.push(arr2[j]);
  }
  return merged;
};

// 실제 mergeSort 함수
const mergeSort = arr => {
  if (arr.length <= 1) {
    //만약 배열의 길이가 1 이하이면 이미 정렬된 배열이므로 그대로 리턴한다.
    return arr;
  }
  // 1보다 큰 배열은 반으로 나누어서 다시 mergeSort 호출
  const left = mergeSort(arr.slice(0, Math.floor(arr.length / 2))); // 최종적으로 여기서 리턴되는 배열은 이미 정렬된 배열이다.
  const right = mergeSort(arr.slice(Math.floor(arr.length / 2))); // 최종적으로 여기서 리턴되는 배열은 이미 정렬된 배열이다.

  return merge(left, right); // 정렬된 두개의 배열을 합치면서 정렬해서 리턴한다!
};
```

인수로 [3, -1, 4] 가 들어가면 어떻게 되는지 차근차근 살펴보자. <br>

1. 우선 배열의 길이가 1 이 아니므로 [3]과 [-1, 4]로 나누어져서 다시 mergeSort() 에 호출이 된다.

2. 첫번째 mergeSort([3]) 은 배열의 길이가 1 이므로 [3]을 리턴한다.
3. 두번째 mergeSort([-1, 4]) 는 배열의 길이가 1 이 아니므로 [-1] 과 [4] 로 나누어져서 다시 mergeSort() 에 호출이 된다.

- 1. 첫번째 mergeSort([-1]) 은 배열의 길이가 1 이므로 [-1]을 리턴한다.
- 2. 두번째 mergeSort([4])는 배열의 길이가 1 이므로 [4]를 리턴한다.
- 3. merge([-1], [4])의 결과인 [-1, 4]를 리턴한다.

4. merge([3], [-1, 4])의 결과인 [-1, 3, 4]를 리턴한다.

> Merge Sort 의 시간 복잡도는 시간복잡도가 O(n)인 merge 함수를 전체 배열을 반으로 나눠가면서( O(log n) ) 호출하므로 O(n \* log n)이다.
