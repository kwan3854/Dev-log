# 오디오 믹싱 + 최종 빌드

## 오디오 믹싱

1. `Assets` -> `Music` -> `Disco Century` 를 Hierarchy 로 드래그 앤 드롭
2. 이름을 BGM 으로 변경
3. Loop 체크
4. 효과음이 나올때는 배경음을 줄여주고 싶다
5. 상단의 `Window` -> `Audio` ->  `Audio Mixer`
6. `+` 버튼을 누르고 이름을 `MainMix` 로 한다.
7. `Groups` 의 `+` 버튼을 누르고 `Master` 하위에 BGM 과 SFX 를 추가한다.
8. BGM 오브젝트의 Audio Source -> Output 설정을 BGM 으로 변경한다.
9. Audio Mixer 창에서 BGM 의 출력을 -10 정도로 내려준다.
10. `Assets` -> `Prefabs` -> `Ball` 을 잠시 Hierarchy 창으로 드래그 앤 드롭한다.
11. Ball 하위에 PlasmaExplosionEffect 가 있다.
12. PlasmaExplosionEffect 의 Output 을 SFX 로 하고 Apply 한 뒤, 오브젝트를 다시 지워준다
13. 마찬가지로 BigExplosion, SmallExplosion 에도 적용해 준다.
14. Audio Mixer -> SFX -> Add Effect -> Send
15. BGM -> Add Effect -> Duck Volume (누군가가 치고 들어오면 소리를 줄여주는 역할)
16. SFX -> Send -> Recieve -> BGM\Duck Volume
    - Send level: 0 db
17. BGM -> Duck Volume
    - Attack Time: 0 (딜레이 없이 바로 발동 되게)
    - Threshold: -60 (발동되기 쉽게 발동 기준을 낮춤)
    - Ratio: 550 (음향이 꺾이는 정도를 높임)

## 버그 픽스

- Rig -> Main Camera -> Camera
  - Clear Flags: Solid Color
  - Background: 컬러픽커를 통해 바닥 색과 동일하게 맞춤

## 빌드

1. `cmd` + `s` 로 씬 저장
2. File -> Build Settings -> Add Open Scenes
3. Build And Run
4. 파일 경로가 꼬이지 않게 새로운 폴더, 경로를 만들어서 저장.