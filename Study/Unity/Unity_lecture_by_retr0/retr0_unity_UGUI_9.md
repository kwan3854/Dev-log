# 인터렉션 컴포넌트 + Selectable

- 마우스, 터치, 키보드, 마우스 입력을 감지해서 거기에 맞는 대응을 할 수 있음
- 대표적으로 버튼이 있음
- 모든 UI 인터렉션 컴포넌트는 Selectable 클래스를 상속해서 구현되어 있음

---

- 여러개의 버튼, 슬라이더 등을 만들고
- Visualize 옵션을 키면 화살표가 나오는 것을 확인할 수 있음
- EventSystem 에서 First Selected 에 버튼이나 슬라이더 등의 오브젝트를 넣는다. (Selectable 을 상속한 것들이 들어감)
  - 게임 실행 후, 화살표키로 버튼들을 왔다갔다 할 수 있음
  - 게임 메인 메뉴창에서 메뉴들을 왔다갔다 하는 것을 생각해면 됨