# 배열

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class HelloArray : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        // 여러개의 값을 하나의 변수로 다루게 해준다.
        int[] scores = new int[10];

        scores[0] = 90;
        scores[1] = 45;
        scores[2] = 60;

        for (int i = 0; i < 10; i++)
        {
            Debug.Log("학생" + i + "점수: " + scores[i]);
        }
    }
}
```

