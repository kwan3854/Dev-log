# 액션 + 람다 함수

- 액션: C# 에서 제공하는 미리 만들어진 델리게이트
- 람다 함수: 이름이 없는 함수를 만들고 변수에 값을 넣듯이 여기저기 줄 수 있다.

## 액션

리턴값이 없고(void) 아무 입력을 받지 않는 형태의 delegate 가 너무 흔하기 때문에 C# 내부에서 미리 만들어 둔 형태가 바로 액션임.

### 예시: 액션을 사용하지 않고 일반 delegate 를 사용

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Worker : MonoBehaviour
{
    delegate void Work();

    Work work;

    void MoveBricks()
    {
        Debug.Log("벽돌을 이동시킵니다.");
    }

    void DigIn()
    {
        Debug.Log("땅을 팝니다.");
    }

    void Start()
    {
        work += MoveBricks;
        work += DigIn;
    }
    
    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            work();
        }
    }
}

```

### 예시: 액션을 사용

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System;

public class Worker : MonoBehaviour
{
    // delegate void Action(); // 과 동일
    Action work;

    void MoveBricks()
    {
        Debug.Log("벽돌을 이동시킵니다.");
    }

    void DigIn()
    {
        Debug.Log("땅을 팝니다.");
    }

    void Start()
    {
        work += MoveBricks;
        work += DigIn;
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            work();
        }
    }
}

```

## 람다 함수

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Messenger : MonoBehaviour
{
    public delegate void Send(string reciever);

    Send onSend;

    void Start()
    {
        onSend += SendMail;
        onSend += SendMoney;

        // 람다 함수를 만들어 이름이 없는 함수를 즉석해서 만들 수 있다.
        // 생략된 형태의 람다 함수 문법표현
        onSend += man => Debug.Log("Assasinate " + man);

        // 완전한 형태의 람다 함수 문법 표현
        onSend += (string man) =>
        {
            Debug.Log("Assasinate " + man);
            Debug.Log("Hide Body");
        };
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            onSend("KwanJoong");
        }
    }

    void SendMail(string reciever)
    {
        Debug.Log("Send Mail to " + reciever);
    }

    void SendMoney(string reciever)
    {
        Debug.Log("Send Money to " + reciever);
    }
}

```

