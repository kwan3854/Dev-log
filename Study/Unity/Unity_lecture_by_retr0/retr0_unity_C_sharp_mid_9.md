# 코루틴

## 코루틴이란?

- 유니티가 지원하는 기능
- 처리와 처리 사이에 대기 시간을 삽입할 수 있다.
- 여러 처리를 동시에  병렬로 할 수 있게 해 준다. (비동기)

## 코루틴을 사용하는 대표적인 예

### 화면의 페이드인 페이드아웃 (코루틴의 대기시간 기능)

1. Hierarchy 창에서 `우클릭` -> `UI` -> `Image`

2. Image 의 Anchor preset 을 눌러 `Option` 또는 `Alt` 키를 누른 채로 Stretch 옵션을 선택한다.

3. 게임 화면이 해당 Image 로 가득 차게 된다.

4. 페이드인, 아웃은 해당 Image 의 Color 에서 알파값을 조절해서 구현하게 된다.

5. 아까 생성한 Imgage 오브젝트의 이름을 Fade 로 바꿔준다.

6. Fade 오브젝트의 컬러를 가져오고, 컬러의 알파값을 조절하는 방식으로 페이드 인 아웃을 구현하자.

7. Fade 오브젝트의 Inspector 창에서 `Add Component` -> `New Script` -> 새로운 스크립트의 이름을 `Fade` 로 함

8. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   using UnityEngine.UI;
   
   public class Fade : MonoBehaviour
   {
       public Image fadeImage;
       // Start is called before the first frame update
       void Start()
       {
           FadeIn();
       }
   
       void FadeIn()
       {
           Color startColor = fadeImage.color;
           
           for (int i = 0; i < 100; i++)
           {
               startColor.a = i / 100f;
               fadeImage.color = startColor;
           }
       }
   }
   
   ```

9. 위 코드와 같이 했더니 바로 알파값이 255 가 되어버렸다. 

   - 중간에 쉬지 않고 바로 마지막 라인까지 실행되기 때문.

   - 조금씩 값을 빼서 대기 시간을 줘야 한다.

10. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using UnityEngine.UI;
    
    public class Fade : MonoBehaviour
    {
        public Image fadeImage;
        // Start is called before the first frame update
        void Start()
        {
            StartCoroutine(FadeIn());
        }
    
        IEnumerator FadeIn()
        {
            Color startColor = fadeImage.color;
            
            for (int i = 0; i < 100; i++)
            {
                startColor.a = i / 100f;
                fadeImage.color = startColor;
                yield return new WaitForSeconds(0.01f);
            }
        }
    }
    
    ```

### Place Holder

1. 빈 게임 오브젝트를 만든다.

2. 빈 게임 오브젝트에 `Add Component ` -> `새로운 스크립트 생성` -> 이름을 HelloCoroutine 으로 한다.

3. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class HelloCoroutine : MonoBehaviour
   {
       // Start is called before the first frame update
       void Start()
       {
           StartCoroutine("HelloUnity"); // string 을 인자로 주면 중간에 수동으로 코루틴을 종료할 수 있다.
           // 만약 코루틴을 종료하고 싶다면 StopCoroutine("HelloUnity") 를 사용한다.
           // string 이 아닌 함수를 인자로 주면 코루틴을 종료할 수 없다.
       }
   
       IEnumerator HelloUnity()
       {
           Debug.Log("Hello");
   
           yield return new WaitForSeconds(3f);
           Debug.Log("WUnity");
       }
   }
   
   ```

4. 위 코드는 의도한대로 정상 작동한다.

5. 만약 두개의 함수를 동시에 작동시키면 어떻게 될까?

6. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class HelloCoroutine : MonoBehaviour
   {
       // Start is called before the first frame update
       void Start()
       {
           StartCoroutine("HelloUnity"); // 3초 걸린다.
           StartCoroutine("HelloCSharp"); // 5초 걸린다
           Debug.Log("End"); // 총 8초후에 End? 틀렸다. 코루틴이 끝나기 전에 실행된다
       }
   
       IEnumerator HelloUnity()
       {
           Debug.Log("Hello");
   
           yield return new WaitForSeconds(3f);
           Debug.Log("WUnity");
       }
   
       IEnumerator HelloCSharp()
       {
           Debug.Log("Hi");
   
           yield return new WaitForSeconds(5f);
           Debug.Log("CSharp");
       }
   }
   
   ```

7. Hello, Hi, End 가 먼저 찍히고, 그 다음 Unity, CSharp 이 찍힌다.

8. 즉, 코루틴은 비동기 방식을 구현할 수 있다는 뜻이다. [동기방식과 비동기방식에 대한 설명](https://koras02.tistory.com/87)