# 회전과 쿼터니언

유니티 상에서 Inspector 창의 Transform 의 Rotation 값을 조절하면, vector3를 이용해 회전을 조절하는 것 같이 보인다.

그러나, 사실 회전은 쿼터니언을 통해 조정된다.

쿼터니언이 너무 복잡하기 때문에 vector3의 형태로 회전을 조작하게 만든 것.

### 잘못된 코드

따라서 다음과 같은 코드는 작동하지 않는다

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        transform.rotation = new Vector3(30, 30, 30); // 에러발생
    }
}

```

### 수정

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        transform.rotation = Quaternion.Euler(new Vector3(30, 30, 30));
    }
}

```

## 오일러각과 짐벌락 문제

[참고 영상](https://youtu.be/zc8b2Jo7mno)

1. x축, y축, z축 각각의 회전행렬을 만든다.

   yaw(y축 회전각) pitch(x축), roll(z축)

2. 회전 행렬을 객체의 변환행렬과 각각 순서대(x, y, z)로 곱한다.(오일러각)

3. x축을 90도 틀고난 후 y축으로 90도 튼것과 y축을 90도 틀고난 후 z축으로 90도 튼것과의 회전 값이 같다.

4. 세 개의 축 각을 이용하여 항상 순서대로 회전을 하면 위와같이 한 개의 축이 쓸모 없게 된다. 이 현상이 짐벌락이다.

5. 이 문제는 계산을 한 번에 하지 않아서(세 번으로 나누어서)이다. 쿼터니온을 쓰면 한 번에 계산되어 문제가 해결된다.

## 쿼터니언

x, y, z, w 라는 4개의 차원을 사용

> '쿼터니언은 복잡한 수를 기반으로 하고, 직관적으로 이해하기 쉽지 않습니다.'
>
> -유니티 공식 문서 인용-

쿼터니언의 구조를 정확하게 모두 이해할 필요는 없다.

실제 개발중에 우리가 알아야 할 것은 우리가 **vector3를 통해서 쿼터니언을 생성** 한다는 것이다. 쿼터니언 내부를 직접 조작할 일은 없다.

실제로 유니티상에서는 직접 쿼터니언을 조작하는것은 막아놓았다.

## 코드 예시

### vector3 을 이용한 쿼터니언 생성

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        Quaternion newRotation = Quaternion.Euler(new Vector3(45, 60, 90));
        transform.rotation = newRotation;
    }
}
```
### LookRotation 을 이용해 특정 좌표를 향해 보기

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        // 지정한 좌표를 향해 보게 만듦, 좌표는 상대 좌표임
        Quaternion targetRot = Quaternion.LookRotation(new Vector3(0, 1, 0)); 
        transform.rotation = targetRotation;
        // 만약 큐브가 원점에 있었다면, 월드 좌표 0, 1, 0 쪽을 바라보게 됨
        // 만약 큐브가 1, 1, 1 에 있었다면, 월드 좌표 1, 2, 1 쪽을 바라보게 됨

        // LookRotation() 의 2번째 인자는 머리가 향하는 방향을 지정할 수 있음
        // 2번째 인자를 생략하면 Vector3.up 으로 자동 적용 됨 (보통 머리가 위에 있으니 생략해도 무방)
    }
}

```
### LookRotation 을 활용한 특정 오브젝트 바라보기

```c#
// target 오브젝트를 바라보기

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    public Transform targetTransform;
    // Start is called before the first frame update
    void Start()
    {
        Vector3 direction = targetTransform.position - transform.position;
        Quaternion targetRotation = Quaternion.LookRotation(direction);
        transform.rotation = targetRotation;
    }
}
```
### Lerp 를 이용한 중간각도 찾기

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    public Transform targetTransform;
    // Start is called before the first frame update
    void Start()
    {
        Quaternion aRotation = Quaternion.Euler(new Vector3(30, 0, 0));
        Quaternion bRotation = Quaternion.Euler(new Vector3(60, 0, 0));
        // 퍼센테이지를 명시하면 그 중간값을 찾음
        Quaternion targetRotation = Quaternion.Lerp(aRotation, bRotation, 0.5f);
        // 0.5는 중간이므로 45가 됨
      
        // 0 이면 완전히 aRotation 과 동일
        // 1 이면 완전히 bRotation 과 동일
        transform.rotation = targetRotation;
    }
}

```
### Rotate 함수를 이용한 현재 각도에서 추가적인 회전

```c#
// Rotate 함수는 현재 회전상태에서 추가로 해당 수치만큼 더 회전시킴

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    public Transform targetTransform;
    // Start is called before the first frame update
    void Start()
    {
        Quaternion targetRotation = Quaternion.Euler(new Vector3(45, 0, 0));
        transform.rotation = targetRotation;
        transform.Rotate(new Vector3(30, 0, 0));
        // 75, 0, 0 이 될 것이다.
    }
}

```

### 쿼터니언을 vector3로 변환

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
       Quaternion originalRotation = transform.rotation;

       Vector3 originalRotationInVector3 = originalRotation.eulerAngles;
       Vector3 targetRotationVec = originalRotationInVector3 + new Vector3(30, 0, 0);

       Quaternion targetRotation = Quaternion.Euler(targetRotationVec);
       
       transform.rotation = targetRotation;
    }
}

```

### 쿼터니언 상태에서의 연산

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SetRotation : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
       Quaternion originalRotation = Quaternion.Euler(new Vector3(45, 0, 0));
       
       Quaternion plusRotation = Quaternion.Euler(new Vector3(30, 0, 0));

       Quaternion targetRotation = originalRotation * plusRotation;
       
       transform.rotation = targetRotation; // 75, 0, 0 이 됨
    }
}

```

## 주의사항

### 회전을 나눠서 할 경우

- (30, 45, 60) 상태에서 Rotate(30, 0, 0) 을 하면 (60, 45, 60) 이 될까?
  - 전혀 아니다
  - 만약 30도의 추가 회전이 global space 를 기준으로 이루어졌다면 (60, 45, 60)이 되는 것이 맞다.
  - 그러나, Rotate 를 이용한 추가 회전은 local space 를 기준으로 이루어진다.
