# The Matrix Equations <img src="https://render.githubusercontent.com/render/math?math=\Huge Ax=b">

![product_of_A_and_X](Images/LA4/product_of_A_and_X.png)

## 1. Example 1. Matrix equation

<img src="https://latex.codecogs.com/gif.latex?\dpi{200}&space;\begin{bmatrix}&space;1&space;&&space;2&space;&&space;-1\\&space;0&space;&&space;-5&space;&&space;3&space;\end{bmatrix}\begin{bmatrix}&space;4\\&space;3\\&space;7&space;\end{bmatrix}&space;=&space;4\begin{bmatrix}&space;1\\&space;0&space;\end{bmatrix}&plus;3\begin{bmatrix}&space;2\\&space;-5&space;\end{bmatrix}&plus;7\begin{bmatrix}&space;-1\\&space;3&space;\end{bmatrix}=\begin{bmatrix}&space;3\\&space;6&space;\end{bmatrix}" title="\begin{bmatrix} 1 & 2 & -1\\ 0 & -5 & 3 \end{bmatrix}\begin{bmatrix} 4\\ 3\\ 7 \end{bmatrix} = 4\begin{bmatrix} 1\\ 0 \end{bmatrix}+3\begin{bmatrix} 2\\ -5 \end{bmatrix}+7\begin{bmatrix} -1\\ 3 \end{bmatrix}=\begin{bmatrix} 3\\ 6 \end{bmatrix}" />

<img src="https://latex.codecogs.com/gif.latex?\dpi{200}&space;\begin{bmatrix}&space;2&space;&&space;-3\\&space;8&space;&&space;0\\&space;-5&space;&&space;2&space;\end{bmatrix}\begin{bmatrix}&space;4\\&space;7&space;\end{bmatrix}=\begin{bmatrix}&space;-13\\&space;32\\&space;-6&space;\end{bmatrix}" title="\begin{bmatrix} 2 & -3\\ 8 & 0\\ -5 & 2 \end{bmatrix}\begin{bmatrix} 4\\ 7 \end{bmatrix}=\begin{bmatrix} -13\\ 32\\ -6 \end{bmatrix}" />

## 2. Example 2. Vector equation to matrix equation

<img src="https://latex.codecogs.com/gif.latex?\dpi{200}&space;x_1\mathbf{v_1}&plus;x_2\mathbf{v_2}&plus;x_3\mathbf{v_3}=\begin{bmatrix}&space;\mathbf{v_1}&space;&&space;\mathbf{v_2}&space;&&space;\mathbf{v_3}&space;\end{bmatrix}\begin{bmatrix}&space;x_1\\&space;x_2\\&space;x_3&space;\end{bmatrix}" title="x_1\mathbf{v_1}+x_2\mathbf{v_2}+x_3\mathbf{v_3}=\begin{bmatrix} \mathbf{v_1} & \mathbf{v_2} & \mathbf{v_3} \end{bmatrix}\begin{bmatrix} x_1\\ x_2\\ x_3 \end{bmatrix}" />

## 3. Exapmle 3. System of linear equations to matrix equation

![exapmle3](Images/LA4/example3.png)

## 4. Example 4. More efficient way to compute matrix equation

![example4_1](Images/LA4/example4_1.png)

<img src="https://latex.codecogs.com/gif.latex?\dpi{200}&space;\begin{bmatrix}&space;1&space;&&space;2&space;&&space;-1\\&space;0&space;&&space;-5&space;&&space;3&space;\end{bmatrix}\begin{bmatrix}&space;4\\&space;3\\&space;7&space;\end{bmatrix}=\begin{bmatrix}&space;3\\&space;6&space;\end{bmatrix}" title="\begin{bmatrix} 1 & 2 & -1\\ 0 & -5 & 3 \end{bmatrix}\begin{bmatrix} 4\\ 3\\ 7 \end{bmatrix}=\begin{bmatrix} 3\\ 6 \end{bmatrix}" />

<img src="https://latex.codecogs.com/gif.latex?\dpi{200}&space;\begin{bmatrix}&space;2&space;&&space;-3\\&space;8&space;&&space;0\\&space;-5&space;&&space;2&space;\end{bmatrix}\begin{bmatrix}&space;4\\&space;7&space;\end{bmatrix}=\begin{bmatrix}&space;-13\\&space;32\\&space;-6&space;\end{bmatrix}" title="\begin{bmatrix} 2 & -3\\ 8 & 0\\ -5 & 2 \end{bmatrix}\begin{bmatrix} 4\\ 7 \end{bmatrix}=\begin{bmatrix} -13\\ 32\\ -6 \end{bmatrix}" />

![example4_2](Images/LA4/example4_2.png)

## 5. Theorem 3.

![theorem3](Images/LA4/theorem3.png)

위의 세 가지 식은 모두 같은 내용을 다르게 표현한 것 뿐이다.

하나의 linear system 을 세 가지 관점으로 볼 수 있다는 것이다.

## 6. Theorem 4.

![theorem4](Images/LA4/theorem4.png)

- a, b, c, d 가 모두 참, 거짓을 함께한다.
- 넷 중 하나가 참이면, 모두 참이라는 뜻이고, 넷 중 하나가 거짓이면, 모두 거짓이라는 뜻이다.
- a, b, c 는 사실상 같은 이야기를 하는 것이다.

## 7. Theorem 5.

![theorem5](Images/LA4/theorem5.png)

