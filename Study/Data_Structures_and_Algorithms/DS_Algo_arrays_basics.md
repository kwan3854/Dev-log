# Arrays Basics

> "Arrays are defined as a collection of similar data elements."

```c++
int A[5];

A[5] = 27;
A[1] = 10;
```

<img src="Images/Arrays_Basics/array_mem.png" alt="array_mem" style="zoom:50%;" />

```c
int main()
{
  int A[5];
  int B[5] = {2, 4, 6, 8, 10};
  
  int i;
  for (i = 0; i < 5; i++)
  {
    printf("%d", B[i]);
  }
}
```

```shell
246810
```

<img src="Images/Arrays_Basics/array_mem_2.png" alt="array_mem_2" style="zoom:50%;" />