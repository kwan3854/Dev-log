# 오브젝트 회전 + 시간 간격

## 오브젝트 회전

`EndPoint` 를 빙글빙글 회전 시켜서 눈에 띄게 하고 싶다.

1. 새로운 C# 스크립트를 생성하고 이름을 Rotator 로 한다.

2. 오브젝트의 Transform 은 물체의 위치, 회전, 크기를 관장한다. 따라서 이 Transform 을 가져다가 조작하면 EndPoint 를 회전시킬 수 있을 것이다.

3. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class Rotator : MonoBehaviour
   {
       // Update is called once per frame
       void Update()
       {
           // 소문자 transform 은 나 자신의 transform 을 가져다 쓸 수 있다.
           // (transform 은 모든 오브젝트가 가지고 있기 때문, 예외적인 경우의 숏컷기능임) 
           transform.Rotate(60, 60, 60);
       }
   }
   
   ```

4. 이 스크립트를 모든 EndPoint 에 적용하기 위해 EndPoint Prefab 에 드래그 앤 드롭해서 적용한다.

5. 플레이 해 보면, 미친듯이 빠르게 도는 것을 볼 수 있다.

   - Update 는 매 프레임마다 작동한다
     - 초당 60프레임이라 가정했을때 초당 60도 회전이 아닌, 초당 60*60 = 3600도 회전한다.

### 프레임 단위로 작동시켰을 때의 문제

과거 니드 포 스피드 게임의 xbox/PC 판이 30프레임 락이 걸려있었다.

이용자들은 이 프레임 락을 해제하는 프로그램을 개발하여 적용하였다.

모든 차들이 2배이상 빨라져버리는 문제가 발생했다.

프레임 단위로 차량의 이동 거리를 설정하여 차량의 이동속도를 적용했었는데 그 프레임 락을 해제해버려서 생긴 문제였다.

이럴 때 필요한 개념이 **시간 간격** 이라는 개념이다

## 시간 간격

- Update 함수는 화면이 한번 깜빡일때 마다 실행됨
- Update 함수에 { 한번에 1m 이동 } 이라는 코드가 있다고 해 보자.
  - 구린 컴퓨터 : 1초에 30번 => 1초에 30m 이동
  - 고사양 컴퓨터: 1초에 60번 => 1초에 60m 이동
  - 컴퓨터의 사양에 따라 게임 내용이 달라져버리는 문제가 발생했다.
- 해결법: 깜빡이는 횟수만큼 쪼개서 적용한다.
  - 즉, 한번 깜빡이고 그 다음 깜빡거릴때 까지의 시간 간격을 알아야 한다.
  - 이를 알려주는 것이 Unity 에서 지원하는 함수인 `Time.deltaTime` 이다.
- `Time.deltaTime`
  - 초당 60프레임의 경우
    - `Time.deltaTime` == 1/60
  - 초당 200프레임의 경우
    - `Time.deltaTime` == 1/200

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Rotator : MonoBehaviour
{
    // Update is called once per frame
    void Update()
    {
        transform.Rotate(60 * Time.deltaTime, 60 * Time.deltaTime, 60 * Time.deltaTime);
    }
}

```

