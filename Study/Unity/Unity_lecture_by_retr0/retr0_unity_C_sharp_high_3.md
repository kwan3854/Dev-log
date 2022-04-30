# 델리게이트

델리게이트는 함수에 대한 참조이다.

하나의 델리게이트로 여러 함수들에 접근해 실행할 수 있다.

델리게이트를 이용해 함수를 파라미터로 전달할 수 있고, 여러 함수를 한꺼번에 실행하는 체인 기능, 어떤 상황에 도달했을 때, 이벤트를 발생시키는 델리게이트 이벤트도 제공된다.

---

유언 대리인이 있다고 생각해 보자.

어떤 사람이 유언 리스트를 남겨놓고 죽으면 유언 대리인은 그 리스트에 적힌 내용이 어떻든간에 신경쓰지 않고 리스트에 적힌 내용들을 죽은 사람을 대신해 수행 해 준다.

델리게이트가 이와 비슷하게 작동한다.

## 예시

1. 새로운 C# 스크립트 `Calculator` 를 만든다.

2. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class Calculator : MonoBehaviour
   {
       delegate float Calculate(float a, float b);
   
       Calculate onCalculate;
   
       void Start()
       {
           onCalculate = Sum; // Sum() 가 아닌 Sum 이다. Sum 함수를 명부에 등록한다는 의미.
           
           // 추가 등록
           onCalculate += Multiply;
           onCalculate += Subtract;
   
           // 등록에서 삭제
           onCalculate -= Multiply;
       }
       public float Sum(float a, float b)
       {
           Debug.Log("Summing " + a + " and " + b + " equals " + (a + b));
           return a + b;
       }
   
       public float Subtract(float a, float b)
       {
           Debug.Log("Subtracting " + a + " and " + b + " equals " + (a - b));
           return a - b;
       }
   
       public float Multiply(float a, float b)
       {
           Debug.Log("Multiplying " + a + " and " + b + " equals " + (a * b));
           return a * b;
       }
   
   
       void Update()
       {
           if (Input.GetKeyDown(KeyCode.Space))
           {
               // 마지막으로 실행된 값만 리턴값으로 받는다.
               Debug.Log("마지막 결과값: " + onCalculate(1, 10));
           }
       }
   }
   
   ```

3. Hierarchy 에서 빈 오브젝트를 추가하고 거기에 Calculator 를 붙여 실험해 테스트 해 본다.

   - 스페이스바를 눌러 확인

