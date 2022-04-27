# 오버라이드

## Base Rotator

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class BaseRotator : MonoBehaviour
{
    public float speed = 60f;

    void Update()
    {
        Rotate();
    }

    protected virtual void Rotate()
    {
        // transform.Rotate(speed * Time.deltaTime, 0f, 0f);
    }
}

```

## XRotator

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class XRotator : BaseRotator
{
    protected override void Rotate()
    {
        transform.Rotate(speed * Time.deltaTime, 0f, 0f);
    }
}

```

## YRotator

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class YRotator : BaseRotator
{
    protected override void Rotate()
    {
        transform.Rotate(0f, speed * Time.deltaTime, 0f);
    }
}

```

## ZRotator

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ZRotator : BaseRotator
{
    protected override void Rotate()
    {
        // 만약 base 클래스의 Rotate() 함수를 사용하고 싶다면 아래와 같이 사용한다.
        // base.Rotate(); 

        transform.Rotate(0f, 0f, speed * Time.deltaTime);
    }
}

```

