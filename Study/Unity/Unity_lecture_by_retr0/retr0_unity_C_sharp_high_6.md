# 제네릭

> 제네릭은 우리가 가져다 쓰는 일은 잦아도, 직접 구현하는 일은 드물다.

타입을 특정하지 않고 구현할 수 있게 해 줌

### 예시1

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Util : MonoBehaviour
{
    void Start()
    {
        Print<int>(30);
        Print<string>("Hello");
        Print<double>(3.14);

        // 우리가 많이 보던 제네릭
        // GetComponent<Rigidbody>().AddForce(Vector3.up * 10);
    }

    public void Print<T>(T inputMessage)
    {
        Debug.Log(inputMessage);
    }
}
```

### 예시2

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Util : MonoBehaviour
{
    void Start()
    {
        // string 타입
        Container<string> container = new Container<string>();
        container.messages = new string[3];

        container.messages[0] = "Hello";
        container.messages[1] = "World";
        container.messages[2] = "Generic";

        for (int i = 0; i < container.messages.Length; i++)
        {
            Debug.Log(container.messages[i]);
        }


        // int 타입
        Container<int> container2 = new Container<int>();
        container2.messages = new int[3];

        container2.messages[0] = 1;
        container2.messages[1] = 2;
        container2.messages[2] = 3;

        for (int i = 0; i < container2.messages.Length; i++)
        {
            Debug.Log(container2.messages[i]);
        }
    }
}

public class Container<T>
{
    public T[] messages;
}

```