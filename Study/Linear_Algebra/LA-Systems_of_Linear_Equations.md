# Systems of Linear Equations

## 1. Linear Equations

### Example

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge a_1x_1%2Ba_2x_2%2B...%2Ba_nx_n = b">\

이와 같은 형태의 식이 linear equation 이다.

### Coefficients

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge a_1,a_2...a_n">\

위 식에서 coefficients 부분은 이 부분이다.

## 2. A system of linear equations

A collection of one or more linear equations.

식이 하나여도 상관없다.

## 3. Solution set

The set of all possible solutions of the linear system.

Two linear systems are called **equivalent** if they have the same solution set.

### A system of linear equations has either

1. **no solution,** or.                     -> **inconsistent**
2. **exactly one solution,** or.    -> **consistent**
3. **infinitely many solutions.** -> **consistent**

## 4. Matrix notation

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge x_1-2x_2%2Bx_3=0">\

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge 0%2B2x_2-8x_3=8">\

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge -4x_1%2B5x_2%2B9x_3=-9">\

### Coefficient Matrix (3 x 3)

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge%5C%20%5Cbegin%7Bbmatrix%7D%201%20%26%20-2%20%26%201%5C%5C%200%20%26%202%20%26%20-8%5C%5C%20-4%20%26%205%20%26%209%20%5Cend%7Bbmatrix%7D">\

### Augmented Matrix (3 x 4)

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge%5C%20%5Cbegin%7Bbmatrix%7D%201%20%26%20-2%20%26%201%20%26%200%5C%5C%200%20%26%202%20%26%20-8%20%26%208%5C%5C%20-4%20%26%205%20%26%209%20%26%20-9%20%5Cend%7Bbmatrix%7D">\

## 5. Solving Problem with row operations

### Row Operations

1. **replacement:** 특정 row 부분을 다른 row 의 값을 곱해서 더하는 방식으로 대체하는 것.
2. **scaling:** 특정 row 에 어떤 값을 곱하거나 나누는 것.
3. **interchange:** row의 순서를 바꿈.

위의 방식을 이용해서 augmented matrix 를 풀어보면,

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge%5C%20%5Cbegin%7Bbmatrix%7D%201%20%26%20-2%20%26%201%20%26%200%5C%5C%200%20%26%202%20%26%20-8%20%26%208%5C%5C%20-4%20%26%205%20%26%209%20%26%20-9%20%5Cend%7Bbmatrix%7D">\

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge%5C%20%5Cbegin%7Bbmatrix%7D%201%20%26%20-2%20%26%201%20%26%200%5C%5C%200%20%26%201%20%26%20-4%20%26%204%5C%5C%200%20%26%20-3%20%26%2013%20%26%20-9%20%5Cend%7Bbmatrix%7D">\

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge%5C%20%5Cbegin%7Bbmatrix%7D%201%20%26%20-2%20%26%201%20%26%200%5C%5C%200%20%26%201%20%26%20-4%20%26%204%5C%5C%200%20%26%200%20%26%201%20%26%203%20%5Cend%7Bbmatrix%7D">\

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge%5C%20%5Cbegin%7Bbmatrix%7D%201%20%26%20-2%20%26%200%20%26%20-3%5C%5C%200%20%26%201%20%26%200%20%26%2016%5C%5C%200%20%26%200%20%26%201%20%26%203%20%5Cend%7Bbmatrix%7D">\

<img align="left" src="https://render.githubusercontent.com/render/math?math=\huge%5C%20%5Cbegin%7Bbmatrix%7D%201%20%26%200%20%26%200%20%26%2029%5C%5C%200%20%26%201%20%26%200%20%26%2016%5C%5C%200%20%26%200%20%26%201%20%26%203%20%5Cend%7Bbmatrix%7D">\

### Row Equivalent

Row operation 으로 변환시켜 왔다갔다 할 수 있는 관계에 있는 row 는 서로 row equivalent 이다.

만약 두 개의 linear systems 의 augmented matrix 가 서로 row equivalent 하다면, 두 시스템은 서로 같은 solution set 을 가진다.

