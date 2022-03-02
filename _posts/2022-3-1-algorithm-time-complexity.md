---
title: "알고리즘 정복 - Big-O in JS"
layout: single
---

>시간 복잡도란? 
특정한 크기의 입력 n에 대하여 알고리즘을 위해 필요한 연산의 횟수를 의미하며 빅오(Big-O) 표기법을 사용해 나타냄


표에서 아래로 내려갈수록 시간 복잡도가 높다.

|빅오 표기법	|명칭|	설명|
|-|-|-|
|O(1)|	상수 시간(Constant time)	|문제를 해결하는데 오직 한 단계 처리|
|O(logN)	|로그 시간(Logarithmic time)|	문제를 해결하는데 필요한 단계들이 연산마다 특정 요인에 의해 줄어듬|
|O(N)	|선형 시간(Linear time)	|문제를 해결하기 위한 입력 N 만큼 단계가 필요|
|O(NlogN)	|로그 선형 시간(Linearithmic time)|	문제를 해결하기 위한 단계의 수가 N번에 그 하나의 N번당 단계들이 연산마다 특정 요인에 의해 줄어듬|
|O(N^2)	|이차 시간(Quadric time)	|문제를 해결하기 위한 단계의 수는 입력값 N의 제곱|
|O(2^N)|	지수 시간|	문제를 해결하기 위한 단계의 수는 주어진 상수값 2의 N제곱|



### 예시
1. O(1)
```js
// 예1
// n의 숫자가 아무리 커져도 한 번만 대입해 계산하면 끝 
// O(10), O(100) 이런 거 없다. O(1*10), O(1*100) 이고 상수 10과 100은 제거
function sumUp(n) {
  return (n / 2) * (n + 1); 
}
```
```js
// 예2
// i<n이 아니고 i<5라서 n이 영향을 미치지 않음
let i = 0;
do {
  i = i + 1;
} while (i < 5);
```
2. O(logN): Binary Search
```js
const binarySearch = function (arr, target) {
  let left = 0,
    right = arr.length - 1;
  while (left <= right) {
    let middle = parseInt((right + left) / 2);
    if (arr[middle] === target) {
      return middle;
    }
    if (target < arr[middle]) {
      right = middle - 1;
    } else {
      left = middle + 1;
    }
  }
  return -1;
};
```
3. O(N) 
```js
// 예1
// i<n인 반복문
let i = 0;
do {
  i = i + 1; // f(n) = n, O(f(n)) = O(n)
} while (i < n);
```
```js
// 예2
let i = 0;
do {
  i = i + 3; // f(n) = 3n, i에 3씩 더하니까 위의 알고리즘보다 3배 일찍 끝나지만 n 앞의 상수는 별 의미 없으므로 똑같이 O(f(n)) = O(n)
} while (i < n);
```
4. O(NlogN): Merge Sort
![Merge Sort](https://miro.medium.com/max/1400/1*61Mf0zjVfd1s3_SzUNGxPA.png)
5. O(N^2): loop in loop
```js
// 예1
for(let i = 0; i < n; i++)
	for(let j = 0; j < n; j++)
// f(n) = n*n, O(f(n)) = O(n^2) 
```
```js
// 예2
for(let i = 0; i < n; i++)
	for(let j = i; j < n; j++) // 0이 i로 바뀜
      // i=0이면 n work, i=1이면 n-1 work
      // (n) + (n-1) + n(n-2) _ ... + 2 + 1 = n(n+1)/2 = 1/2(n^2+n)
      // O(1/2(n^2+n)) 상수 1/2 날리고 별 의미없는 +n 날리면 O(n^2)
```
6. O(2^N): Recursive Fibonacci
```js
function fibonacci(num) {
    if(num < 2) {
        return num;
    }
    else {
        return fibonacci(num-1) + fibonacci(num - 2);
    }
}
```

