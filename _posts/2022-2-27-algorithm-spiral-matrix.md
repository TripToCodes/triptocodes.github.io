---
title: "알고리즘 정복 - Spiral Matrix in JS"
layout: single
---

### 문제
2차원 M x N 배열을 나선형(spiral)으로 순회해야 합니다.

### 입력
인자 1 : matrix
세로 길이(matrix.length)가 M, 가로 길이(matrix[i].length)가 N인 2차원 배열
matrix[i]는 string 타입을 요소로 갖는 배열
matrix[i][j].length는 1

### 출력
string 타입을 리턴해야 합니다.

### 주의사항
순회는 좌측 상단 (0,0)에서 시작합니다.
배열의 모든 요소를 순서대로 이어붙인 문자열을 리턴해야 합니다.

### 입출력 예시

```js
matrix = [
  ['T', 'y', 'r', 'i'],
  ['i', 's', 't', 'o'],
  ['n', 'r', 'e', 'n'],
  ['n', 'a', 'L', ' '],
];
output = spiralTraversal(matrix);
console.log(output); // --> 'Tyrion Lannister'

```

### 나의 풀이

```js
const spiralTraversal = function (matrix) {
  // 시작점은 (top, left) = (0, 0) 반대쪽 끝은 (botom, right) = (arr[0].length -1, arr.length -1) = (3, 3) 을 정한다
  // direction(right, down, left, up)을 정한다
  // matrix[top][left] 에선 to right
  // matrix[top][right] 에선 to down
  // matrix[bottom][right]에선 to left, 
  // matrix[bottom][left]에선 to up

  let result = []
  let top = 0
  let left = 0
  let right = matrix[0].length - 1
  let bottom = matrix.length -1
  let direction = 'right'
  while(left<=right && top<=bottom) {
    if(direction === 'right'){
      for(let i = left; i<=right; i++) {
        result.push(matrix[top][i])
      }
      top += 1 // 기준 top점이 내려가려면 반대로 인덱스 번호를 1 더한다.
      direction = 'down'
    }
    else if(direction === 'down'){
      for(let i = top; i<=bottom; i++) {
        result.push(matrix[i][right])
      }
      right -= 1
      direction = 'left'
    }
    else if(direction === 'left'){
      for(let i = right; i>=left; i--) {
        result.push(matrix[bottom][i])
      }
      bottom -= 1 // 기준 bottom이 올라가려면 인덱스 번호를 1 내린다.
      direction = 'up'
    }
    else if(direction === 'up'){
      for(let i = bottom; i>=top; i--) {
        result.push(matrix[i][left]) 
      }
      left += 1
      direction = 'right'
    }
  }

  return result.join('');
};
```


### 레퍼런스

```js
const spiralTraversal = function (matrix) {
  // 각 방향마다 [row, column]의 변화를 저장
  const RIGHT = [0, 1];
  const DOWN = [1, 0];
  const LEFT = [0, -1];
  const UP = [-1, 0];
  // 각 방향을 'MOVES'라는 배열에 집어넣고 
  // MOVES[index]에서 index를 direction으로 칭한다. 
  // 반복문에서 direction의 값이 증가하면 
  // 차례로 RIGHT>DOWN>LEFT>UP의 요소에 접근한다.
  const MOVES = [RIGHT, DOWN, LEFT, UP]; //2차 행렬
  const M = matrix.length; // 행
  const N = matrix[0].length; // 열
  // 행렬의 범위를 벗어나지 않는 조건
  const isValid = (row, col) => row >= 0 && row < M && col >= 0 && col < N;

  let count = 0;
  let row = 0,
    col = -1; // col은 왜 범위 밖에서 시작하나 싶었는데 뒤에 cd(1)가 더해져서 0이 됨
  let direction = 0;
  let result = '';
  while (count < M * N) { // M*N은 엘리먼트의 총 개수가 되고 나중에 count와 비교하여 함수를 종료할 수 있음
    const move = MOVES[direction]; // MOVES[0] = RIGHT = [0, 1]
    const [rd, cd] = move; // 구조분해할당 rd(row direction) =0, cd(col direction)=1

    row = row + rd; // 0
    col = col + cd; // 0
    while (isValid(row, col) && matrix[row][col] !== false) {
      result = result + matrix[row][col]; // result = "T"
      // 한 요소에 두 번 접근하지 않게 하기 위해서, 접근된 요소를 false로 변경한다.
      matrix[row][col] = false;
      row = row + rd; // 0+0 행은 그대로
      col = col + cd; // 0+1 열은 1칸 이동
      count++;
    }
    // row, col 이 행렬의 범위를 벗어났기 때문에,
    // 진행된 방향의 반대로 한 칸 이동한다.
    row = row - rd;
    col = col - cd;

    // 각 방향이 순환되기 때문에 방향 개수를 나머지로 하는 모듈러 연산을 사용한다.
    direction = (direction + 1) % 4;
  }
  return result;
};
```
  

>### 소감
레퍼런스는 항상 놀랍다. [row, col] 배열에 0, 1, -1을 넣어 방향을 직관적으로 표현한 것이 좋았다. 한 요소에 두번 접근하지 않기 위해 접근된 요소를 false로 바꿔놓는 것도 DFS에서 visited 에 [false, ..., false]를 할당하고 접근했을 때마다 true로 바꿔놓았던 방법과 일맥상통하는 것 같다. 방향 개수만큼 나머지 연산을 사용하여 방향이 모든 방향으로 다 돌았을 때 다시 0으로 초기화 하는 것도 기억했다가 꼭 다른데서 써야겠다.
  
