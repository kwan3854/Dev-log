# 슬라이더

- Slider
  - Fill Area: 채워지는 공간
    - Fill: Fill Area 의 일부분을 채움 (Slider 의 Value 를 기반으로)

---

- Whole Numbers: Value 가 정수 단위로 조정되게 함

- Handle Slider Area: 유저가 조정하는 핸들부분을 표시

  - Slider 의 Interactable 만 켜져있다면 없어도 조정가능. (단순히 화면 표시용도임)

- Background: 마찬가지로 단순히 시각적 효과일 뿐임

- On Value Changed

  - value 값에 따라 뭔가 작동하게 할 수 있음

  - ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    
    public class ColorChange : MonoBehaviour
    {
        public void ChangeColor(float percentage)
        {
            Color color = new Color(percentage, 0, 0);
            GetComponent<Renderer>().material.color = color;
        }
    }
    
    ```

    