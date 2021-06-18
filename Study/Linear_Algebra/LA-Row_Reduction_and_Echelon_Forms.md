# Row Reduction and Echelon Forms

## 1. 용어 정리

- A nonzero row or column

  Matrix 에서 특정 row 나 column 을 볼 때 최소한 하나라도 0이 아니면 해당.

- A leading entry of row

  특정 row 에서 가장 왼쪽에 있는 nonzero entry.

- Echelon form

  1. 모든 nonzero row 들이 0보다 위에 있어야 함.
  2. row의 각각의 leading entry 는 자신 보다 위의 row의 leading entry 보다 오른쪽에 있어야 한다.

- Reduced echelon form

  3. 각각의 nonzero row 의 leading entry 는 1.
  4. 각각의 leading 1 은, 해당 column 에서 유일한 nonzero entry.

- Pivot position

  Reduced echelon form 에서 leading entry 의 위치.

> **Theorem 1. Uniqueness of the Reduced Echelon Form**
>
> 각각의 matrix 는, 유일하게 존재하는 reduced echelon matrix 와 row equivalent 하다.
>
> (각각의 matrix 에서 reduced echelon matrix 는 하나만 존재한다.)

## 2. Row reduction algorithm

1. 가장 왼쪽의 nonzero column 에서 시작.

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%200%20%26%203%20%26%20-6%20%26%206%20%26%204%20%26%20-5%5C%5C%203%20%26%20-7%20%26%208%20%26%20-5%20%26%208%20%26%209%5C%5C%203%20%26%20-9%20%26%2012%20%26%20-9%20%26%206%20%26%2015%20%5Cend%7Bbmatrix%7D">

2. Pivot column 에서 pivot 으로 nonzero entry 를 고른다. 필요하다면 interchange 를 한다.

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%200%20%26%203%20%26%20-6%20%26%206%20%26%204%20%26%20-5%5C%5C%203%20%26%20-7%20%26%208%20%26%20-5%20%26%208%20%26%209%5C%5C%203%20%26%20-9%20%26%2012%20%26%20-9%20%26%206%20%26%2015%20%5Cend%7Bbmatrix%7D">

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%203%20%26%20-9%20%26%2012%20%26%20-9%20%26%206%20%26%2015%5C%5C%203%20%26%20-7%20%26%208%20%26%20-5%20%26%208%20%26%209%5C%5C%200%20%26%203%20%26%20-6%20%26%206%20%26%204%20%26%20-5%20%5Cend%7Bbmatrix%7D">

3. Pivot 아래의 모든 position 을 0 으로 만들기 위해, row replacement 를 한다.

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%203%20%26%20-9%20%26%2012%20%26%20-9%20%26%206%20%26%2015%5C%5C%200%20%26%202%20%26%20-4%20%26%204%20%26%202%20%26%20-6%5C%5C%200%20%26%203%20%26%20-6%20%26%206%20%26%204%20%26%20-5%20%5Cend%7Bbmatrix%7D">

4. Step 1-3 을 남은 submatrix 에 대해 수행한다.

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%203%20%26%20-9%20%26%2012%20%26%20-9%20%26%206%20%26%2015%5C%5C%200%20%26%202%20%26%20-4%20%26%204%20%26%202%20%26%20-6%5C%5C%200%20%26%200%20%26%200%20%26%200%20%26%201%20%26%204%20%5Cend%7Bbmatrix%7D">

   위의 과정을 **foward phase** 라고 하고,

   이 과정을 통해 나온 결과물을 **echelon form** 이라고 한다.

5. 가장 오른쪽 pivot 부터,  해당 pivot 위의 숫자들을 0으로 만든다. 만약 pivot 이 0이 아니라면, scaling 도 한다.

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%203%20%26%20-9%20%26%2012%20%26%20-9%20%26%206%20%26%2015%5C%5C%200%20%26%202%20%26%20-4%20%26%204%20%26%202%20%26%20-6%5C%5C%200%20%26%200%20%26%200%20%26%200%20%26%201%20%26%204%20%5Cend%7Bbmatrix%7D">

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%203%20%26%20-9%20%26%2012%20%26%20-9%20%26%200%20%26%20-9%5C%5C%200%20%26%202%20%26%20-4%20%26%204%20%26%200%20%26%20-14%5C%5C%200%20%26%200%20%26%200%20%26%200%20%26%201%20%26%204%20%5Cend%7Bbmatrix%7D">

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%203%20%26%20-9%20%26%2012%20%26%20-9%20%26%200%20%26%20-9%5C%5C%200%20%26%201%20%26%20-2%20%26%202%20%26%200%20%26%20-7%5C%5C%200%20%26%200%20%26%200%20%26%200%20%26%201%20%26%204%20%5Cend%7Bbmatrix%7D">

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%203%20%26%200%20%26%20-6%20%26%209%20%26%200%20%26%20-72%5C%5C%200%20%26%201%20%26%20-2%20%26%202%20%26%200%20%26%20-7%5C%5C%200%20%26%200%20%26%200%20%26%200%20%26%201%20%26%204%20%5Cend%7Bbmatrix%7D">

   <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%201%20%26%200%20%26%20-2%20%26%203%20%26%200%20%26%20-24%5C%5C%200%20%26%201%20%26%20-2%20%26%202%20%26%200%20%26%20-7%5C%5C%200%20%26%200%20%26%200%20%26%200%20%26%201%20%26%204%20%5Cend%7Bbmatrix%7D">

   Step 5 를 **backward phase** 라고 한다.

## 3. Solution of Linear Systems

<img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%201%20%26%200%20%26%20-5%20%26%201%5C%5C%200%20%26%201%20%26%201%20%26%204%5C%5C%200%20%26%200%20%26%200%20%26%200%20%5Cend%7Bbmatrix%7D">

위와 같은 경우,

<img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge x_1=1%2B5x_3"> 

<img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge x_2=4-x_3">

<img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge x_3"> is free

Leading position 에 해당하는 값을 basic variable 로 잡는다.

따라서,x1, x2 는 basic variables(leading variables) 이다.

나머지 x3 가 free 로 잡는다.

### 이런 경우라면?

<img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%201%20%26%206%20%26%202%20%26%20-5%20%26%20-2%20%26%20-4%5C%5C%200%20%26%200%20%26%202%20%26%20-8%20%26%20-1%20%26%203%5C%5C%200%20%26%200%20%26%200%20%26%200%20%26%200%20%26%207%20%5Cend%7Bbmatrix%7D">

<img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge 0%20%3D%207%3F">

> **Theorem 2. Existence and Uniqueness Theorem**
>
> 만약, linear system 에서 augmented matrix의 가장 오른쪽 column 이 pivot column 이 아니라면, consistent 하다(해가 있다).
>
> 쉽게 말해, augmented matrix의 echelon form 이 아래 형태의 row 가 없다면 해가 있다는 뜻.
>
> <img style="float: left;" src="https://render.githubusercontent.com/render/math?math=\huge %5Cbegin%7Bbmatrix%7D%200%20%26%20...%20%26%200%20%26%20b%20%5Cend%7Bbmatrix%7D">
>
> (b 는 0 이 아니다)
>
> 만약, linear system 이 consistent 하다면, solution set 은,
>
> 1. unique solution (free varaible 이 없다면)
> 2. infinitely many solutions (최소 하나의 free variables 가 있다면)

