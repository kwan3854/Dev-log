# 파워 슬라이더

1. 새로운 Material 을 만들어서 Small Prop 에게 색을 적용한다.
   - Material 이름을 SmallProp
   - 노란색
2. Small Prop 을 이용해 Big Prop 을 만든다
   - Scale 을 (2, 2, 2) 로 만듦
   - 새로운 Material 적용해서 색 바꿈
   - HP를 50 으로 수정
   - Score를 10 으로 수정
   - 폭발효과를 BigExplosion 으로 변경
     - 폭발효과를 Hierarchy 로 드래그 앤 드롭하고
     - 자식 오브젝트까지 모두 선택한 상태로
     - Looping 옵션 체크 해제
     - Play On Awake 체크 해제
     - Audio Source 추가 (Add Component)
     - Audio 의 Play On Awake 체크 해제
     - 폭발 효과를 프리펩으로 만듦
     - 만든 프리펩 효과를 Big Prop 에 적용
3. Big Prop 을 프리펩으로 만듦
4. Ball, Big Prop, Small Prop 의 변경사항을 프리펩에 Apply 한다
5. Ball, Big Prop, Small Prop 오브젝트들을 모두 삭제한다.
6. Shooter Pivot 에 새로운 스크립트를 만들어서 붙여준다.
   - BallShooter 스크립트를 만듦
7. Hierarchy 에서 Create -> UI -> Slider (차징되는 정도를 나타낼 UI)
8. 우리는 UI 로 띄우고 싶은게 아니라, 슬라이더를 총신에 직접 붙여서 표현하고 싶다.
   - Canvas 의 Render Mode 를 World Space 로 변경
   - Canvas Scaler -> Reference Pixels Per Unit 을 1로 변경
   - Rect Transform ->
     - Width: 3.5
     - Height 3.5
     - Position: (0, 0, 0)
9. Canvas 를 Shooter Pivot 의 자식으로 넣음
10. Event System 은 지움 (유저 입력을 받게 해 주는 것인데 필요 없으니)
11. Canvas 의 Rotation 을 (90, 0, 0), PosY를 0.2 정도 올려줌
12. 우리는 슬라이더의 손잡이가 필요없음
    - Background, Handle Slide Area 를 삭제
13. Slider 오브젝트의
    - Interactalbe 체크 해제 (유저가 직접 조정 불가능하게)
    - Transition 을 None 으로
    - Min Value 15
    - Max Value 30
14. Slider 와 그 하위 오브젝트를 모두 선택한 상태에서
    - Rect Transform 의 Anchor Point 를 클릭
    - `Option` 또는 `Alt` 버튼을 누른 채로 맨 오른쪽 아래의 Strecth 옵션 선택
15. Slider 오브젝트의 Direction 을 Bottom to Top 으로 변경
16. Rect 툴을 사용해 Slider 의 모양을 길쭉하게 변경
17. Fill 의 색깔을 변경한다.
    - 빨간색, 투명도 조절해서 약간 투명하게
18. Slider 의 이름을 Power Slider 로 변경