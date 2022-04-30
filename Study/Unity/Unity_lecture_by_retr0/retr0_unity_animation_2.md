# 애니메이터 컨트롤러 + FSM

### 애니메이터 컨트롤러

1. Assets/unity-chan!/Unity-Chan! Model/Art/Model/unitychan 파일을 Hierarchy 로 드래그 앤 드롭
2. Assets/Animation 폴더를 새로 만든다.
3. 해당 폴더 내부에 우클릭->Create->Animator Controller
   - 파일이름을 Character Animator 로 한다. (이름은 무엇이되었든 상관없음)
4. Hierachy 창 -> unitychan -> Inspector 창 -> Animator -> Controller 부분에 만든 Character Animator 를 드래그 앤 드롭
   - Animator Controller: 애니메이션을 제어하는 지도가 그려져 있다. (에셋 파일임)
   - unitychan 내부의 Animator 컴포넌트: Animator Controller 를 가져와 이것을 바탕으로 애니메이션을 재생 (컴포넌트임)

### 애니메이터 컨트롤러와 FSM (Finate State Machine)

1. Window -> Animation -> Animator 를 눌러 새로운 Animator 창을 연다.
   - 유니티의 Animator 는 FSM(Finate State Machine) 이라는 모델을 바탕으로 만들었다.
2. 이 State Machine 을 이용해 애니메이션을 제어 해 보자.
3. Assets/unity-chan!/Unity-Chan! Model/Art/Animations/ 폴더에 들어간다.
   - unitychan_WAIT00/WAIT00 이라는 애니메이션 클립을 Animator 창으로 드래그 앤 드롭
     - WAIT00 이라는 State 가 추가되었는데 이 이름을 Idle 로 변경한다.
   - 이번에는 Animation 창의 빈공간에 우클릭 -> Create State -> Empty 로 새로운 State 를 생성한다.
     - 이름을 Jump 로 한다.
     - 위 Idle 과 같은 방식으로 Jump00 애니메이션을 Jump State에 할당한다.
4. 이제 게임이 시작되면 Entry State 를 통해 진입해 화살표(Transition)를 타고 Idle 로 이동한다.
5. 만약 Jump State 에 우클릭을 해서 Set as Layer Defualt State 를 하면 점프 동작이 기본 동작이 된다.
6. 다시 Idle 을 기본 State 로 해 주고 새로 걷는 State 를 추가한다.
   - WALK00_F 애니메이션을 드래그 앤 드롭
   - 상태 이름을 Walk Forward 로 바꿔줌

### FSM 의 이용

- 한번에 두 가지 상태일 수는 없다. 한번에 한 상태만 가능
- Transition 을 통해 상태에서 상태로 이동 가능
  - 우클릭 -> Make Transition 으로 다른 상태로 이어줄 수 있음.
  - Idle 에서 Jump 로 Transition 을 이어주고, 다시 Jump 에서 Idle 로 서로 이어주면 무한히 두 상태를 반복하는 것을 볼 수 있다.
  - 마찬가지로 Idle 과 Walk Forward 역시 서로 연결해 준다.
- 보통은 Transition 하는 조건을 주어서 사용한다.
- Animator 창에서 Parameters 창을 본다
  - Float 파라미터 추가 -> 이름을 Speed
  - Trigger 파라미터 추가 -> 이름을 Jump
    - Trigger 는 순간적으로 true 가 되었다가 다시 false 로 자동으로 변함
- Idle -> Walk Forward 의 Transition 을 눌러 Inspector 창 -> Conditions 에 Speed 조건을 추가한다.
  - Speed , Greater, 0.3 으로 설정
    - if speed is greater than 0.3 이라는 의미
- 같은 방법으로 Walk Forward -> Idle 의 상태도 조건을 설정 해 준다.
  - Speed, Less, 0.3
- Idle -> Jump
  - Jump
- Jump -> Idle 은 설정하지 않는다.
  - Has Exit Time 이 켜져 있다.
    - 현재 재생중인 애니메이션이 종료시간이 있고, 현재 애니메이션이 종료시간에 도달하면 지금 애니메이션을 종료하고 다음 애니메이션으로 자동으로 이동한다는 의미.