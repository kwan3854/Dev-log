# Type Size in Memory #2

- long/int/float 등은 4bytes

- ptr 는 64bit 환경이 경우, type 에 상관없이 8bytes

  왜 pointer 는 항상 8byte?

  64bit 환경에서의 메모리 주소 길이는 항상 일정하므로 (포인터는 메모리 주소를 나타내는 것이니까)

크기를 가진다.

---

### Struct

```c++
#include <iostream>

struct ST1
{
  int a; // 4bytes
  int b; // 4bytes
}; // 4+4=8 bytes

struct ST2
{
  long a; // 8bytes
  int b; // 4bytes
  short c; // 2bytes
}; // 8+4+2=14 bytes? (X)

int main()
{
  std::cout << sizeof(ST1) << std::endl; // 8
  std::cout << sizeof(ST2) << std::endl; // 16
  return 0;
}
```

struct 내부에서 메모리 access 방식 때문에 padding 을 사용한다.

따라서 단순히 내부의 변수들의 크기를 더하는 것 만으로 전체 struct 크기를 추측해서는 안된다.

### Class

```c++
#include <iostream>

class Cat
{
public:
  void printCat(); // ????
private:
  int age;      // 4bytes
  float weight; // 4bytes
};

int main()
{
  std::cout << sizeof(Cat) << std::endl; // 8
  
  return 0;
}
```

멤버함수를 가리키는 함수의 포인터까지 8+4+4 가 되지 않을까 싶지만, 멤버 함수는 포함되지 않는다.

 

