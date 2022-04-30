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

## 메서드의 람다 표현

람다 표현을 이용하면 다음처럼 코드를 간단히 할 수 있다.

```c#
    private int m_health = 100;

    public void RestoreHealth(int amount)
    {
        m_health += amount;
    }

    public bool isDead()
    {
        return m_health <= 0;
    }
```

```c#
    private int m_health = 100;
    public void RestoreHealth(int amount) => m_health += amount;
    public bool isDead() => m_health <= 0;
```

## 프로퍼티의 람다식 표현

```c#
    private int m_health = 100;

    public int Health
    {
        get { return m_health; }
        set { m_health = value; }
    }

    public bool IsDead
    {
        get { return m_health <= 0; }
    }
```

```c#
    private int m_health = 100;

    public int Health
    {
        get => m_health;
        set => m_health = value;
    }

    public bool IsDead => m_health <= 0;
```

주목할점은 `IsDead` 처럼 `get` 만 존재하는 프로퍼티의 경우 매우 짧은 코드로 표현이 가능하다는 점이다.

메서드나 프로퍼티를 람다 식으로 표현하는 것은 개인의 선택이다.

단, 다음 상황에서는 클린 코드를 구현하ㅣ 위해 람다 식을 적극 활용하는 것을 추천한다.

- 한두줄로 이루어진 메서드 또는 프로퍼티
  - 특히 한줄로 이루어진 `get` 프로퍼티가 존재하는 경우
