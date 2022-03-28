# Arrays Basics Practice

```c++
#include <iostream>

using namespace std;

int main()
{
  int A[5];
  A[0] = 12;
  A[1] = 15;
  A[2] = 25;
  
  cout << sizeof(A) << endl;
  cout << A[1] << endl;
  
  return 0;
}
```

```shell
20
15
```

---

```c++
#include <iostream>

using namespace std;

int main()
{
  int A[] = {2, 4, 6, 8, 10}; // you can skip writing size
  
  cout << sizeof(A) << endl;
  
  return 0;
}
```

```shell
20
```

---

```c++
#include <iostream>

using namespace std;

int main()
{
  int A[10] = {2, 4, 6, 8, 10, 12, 14}; // size 10, but only 7 allocated
  
  cout << sizeof(A) << endl;
  cout << A[8] << endl; // auto zero filled
  cout << A[9] << endl; // auto zero filled
  
  return 0;
}
```

```shell
40
0
0
```

---

```c++
#include <iostream>

using namespace std;

int main()
{
  int A[10] = {2, 4, 6, 8, 10, 12};
  
  for (int i = 0; i < 10; i++)
  {
    cout << A[i] << endl;
  }
  
  return 0;
}
```

```shell
2
4
6
8
10
12
0
0
0
0
```

---

```c++
#include <iostream>

using namespace std;

int main()
{
  int A[10] = {2, 4, 6, 8, 10, 12};
  
  for (int x:A)
  {
    cout << x << endl;
  }
  
  return 0;
}
```

```shell
2
4
6
8
10
12
0
0
0
0
```

---

```c++
#include <iostream>

using namespace std;

int main()
{
  int n;
  cout << "Enter Size";
  cin >> n;
  int A[n] = {2, 4, 6, 8, 10, 12}; // error occur
  
  for (int x:A)
  {
    cout << x << endl;
  }
  
  return 0;
}
```

```c++
#include <iostream>

using namespace std;

int main()
{
  int n;
  cout << "Enter Size";
  cin >> n;
  int A[n]; // error not occur, filled with garbage values
  
  for (int x:A)
  {
    cout << x << endl;
  }
  
  return 0;
}
```

```c++
#include <iostream>

using namespace std;

int main()
{
  int n;
  cout << "Enter Size";
  cin >> n;
  int A[n]; // error occur
  
  for (int i = 0; i < n; i++)
  {
      A[i] = i;
  }
  for (int x:A)
  {
    cout << x << endl;
  }
  
  return 0;
}
```

```shell
Enter Size3
0
1
2
```

