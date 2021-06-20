#  Type Size in Memory #1

```c++
int main()
{
  int a = 0;
  std::cout << sizeof(int) << std::endl;
  std::cout << sizeof(a) << std::endl;
  
  return 0;
}
```

```shell
prompt: clang++ stack.cpp
prompt: ./a.out
4
4
```

이 결과에서 4는 4byte 를 의미한다.

int, long 등의 size 는 환경에 따라 다를 수 있다. (반드시 4byte 인 것은 아니다)

다음과 같은 방식으로 assert 를 이용해 사이즈를 확인할 수 있다.

```c++
static_assert(sizeof(int)==4, "int is 4bytes");
```

Fixed width integer types (C++ 11 부터 사용가능) 를 이용할 수도 있다.

```c++
#include <cstdint>
#include <iostream>

int main()
{
  std::cout << sizeof(int8_t) << std::endl; // 8bit
  std::cout << sizeof(int64_t) << std::endl; // 64bit
  
  return 0;
}
```

```shell
prompt: g++ stack.cpp -std=c++11
prompt: g++ ./a.out
1
8
```

- 아무 생각 없이 int 가 필요할 때는, 그냥 int 사용
- 정확한 메모리 레이아웃을 정해서 사용할 때는, fixed width integer types 를 사용.

이를 확장해, array 에도 사용가능하다.

```c++
#include <array>
#include <cstdint>
#include <iostream>

int main()
{
  // array
  // int a[10];
  // std::cout << sizeof(a) << std::endl; // 40
  std::array<int,5> ia;
  std::cout << sizeof(ia) << std::endl; // 20
  
  return 0;
}
```

```shell
prompt: g++ stack.cpp
prompt: ./a.out
20
```

