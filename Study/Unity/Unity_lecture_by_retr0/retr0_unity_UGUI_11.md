# 토글

- 껏다 켰다 하는 체크 박스

- bool 입력을 받는 함수를 만들어 제어 가능

- ```c#
      public void ChangeColor(bool isOn)
      {
          if (isOn)
          {
              GetComponent<Renderer>().material.color = Color.red;
          }
          else
          {
              GetComponent<Renderer>().material.color = Color.white;
          }
      }
  ```

- 해당 함수를 Toggle 의 On Value Chnaged 에 연결하면 됨

  - Dynamic bool: 유니티 이벤트가 입력값을 전달 할 수 있음
  - Static Parameter: 유니티 이벤트가 입력값을 전달하지 않고 개발자가 직접 전달할 수 있음

[보조자료 링크](https://ansohxxn.github.io/unity%20lesson%201/chapter10-3/)