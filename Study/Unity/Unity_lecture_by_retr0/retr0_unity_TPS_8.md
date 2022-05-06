# PlayerInput

1. Player Character 오브젝트에 Add Component -> Character Controller
   - Character Controller: Rigid Body 의 물리시스템을 필요로 하지 않는 캐릭터에게 움직이는 기능을 추가할 때 사용
     - Capsule Collider 를 상속받아서 만들어 짐
   - Rigid Body 의 물리시스템을 수정하지 않고 사용하면 의도와는 다르게 사실적인 물리가 적용되어 움직임이 자연스럽지 않는 경우가 있다. (특히 FPS, TPS 장르 등에서)
2. Character Controller 의 수치를 조정한다.
   - Center (0, 0.9, 0)
   - Radius: 0.3
   - Height: 1.8
3. 캐릭터가 피해를 입었을 때 소리를 내도록 Audio Source 컴포넌트를 추가
   - Play On Awake 체크 해제
4. Player Character 에 다음 스크립트들을 추가한다.
   - Assets/Scripts 하단의
     - Player Input: 플레이어의 인풋을 감지하고 다른 컴포넌트들에게 알림
     - Player Movement: 플레이어 입력에 따라 캐릭터를 실제로 움직임

