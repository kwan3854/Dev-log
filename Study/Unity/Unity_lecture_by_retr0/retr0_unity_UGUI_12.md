# 토글 그룹

- 라디오 버튼을 생각하면 된다.
  - 옛날 라디오의 버튼은 동시에 여러개가 눌리지 않고 한번에 하나만 클릭될 수 있다.
- UI -> Panel 을 추가한다.
- Panel 하위에 여러개의 Toggle 을 넣는다.
- Panel 에 Add Component -> Toggle Group
- 각 토글들의 컴포넌트를 보면 Group 을 지정할 수 있다.
  - 각 토글들을 Panel 그룹에 추가
- 하나의 그룹에 속해있는 토글 버튼들은 한번에 하나씩만 선택 가능하다.

- 예외적으로 모든 토글버튼들의 Is On 이 처음부터 켜져있었다면
  - 시작할때는 모든 토글 버튼들이 켜져 있게된다.
  - 그러나 체크표시를 건드리는 순간 다시 하나씩만 켜지게 된다.
- Allow Swicth Off
  - 해당 옵션을 끄면 이미 켰던것을 끄는 것이 가능하다.