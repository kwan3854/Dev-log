# 오버로드

함수의 이름은 똑같은데 입력과 출력을 다르게(여러가지로) 할 수 있음

## 코드

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Calc : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        Debug.Log(Sum(1,1));
        Debug.Log(Sum(1,1,1));
        Debug.Log(Sum(1.3f,1.4f));
        Debug.Log(Sum(1.3f,1.4f,1.5f));
    }

    public int Sum(int a, int b)
    {
        return a + b;
    }

    public int Sum(int a, int b, int c)
    {
        return a + b + c;
    }

    public float Sum(float a, float b)
    {
        return a + b;
    }

    public float Sum(float a, float b, float c)
    {
        return a + b + c;
    }
}

```

