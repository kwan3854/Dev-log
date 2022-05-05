# 프로젝트 구성 + 패키지 매니저

## 주의

### 다운로드 부분

https://github.com/IJEMIN/Unity-TPS-Sample

- git lfs 를 사용하여 업로드 되어 있음
- 따라서  `Clone or Download -> Download ZIP` 이 방식으로는 다운로드 하면 안됨
- release 탭의 압축파일로 다운로드 받아야 함

### 버전

- 유니티 2019.1.7 버전을 사용해야 함

## 구성

### Assets

- ProBuilder Data: 프로빌더라는 유용한 서드파티 패키지가 있는데 이것을 이용해 맵을 구성해 놓은 데이터 파일
- Sprites: 2D 텍스처 파일
- Textures: 3D 모델을 위한 텍스처 파일

### Packages

- Cinemachine: 스마트 추적 카메라, 복잡한 카메라 연출을 손쉽게 구현
- Post Processing: 영상미을 위해 사용
- ProBuilder: 레벨 디자인, 프로토타이핑 도구
- Unity Timeline: 영상편집 도구와 비슷한 UI로 시네마틱 컷신을 구현할 수 있음

## 패키지 매니저

- 애셋 스토어를 거치지 않고 패키지를 관리할 수 있음
- 패키지 매니저를 통해 다 받은 패키지는 글로벌 캐시로 관리됨
- Packages 폴더 내의 패키지들은 사실 글로벌 캐시로 관리되는 패키지들을 참조하거나 복제해 놓은 것임
- 패키지 매니저를 통해 추가된 패키지들은
  - 프로젝트폴더/Packages/manifest.json 파일에 명단으로서 관리됨
  - 따라서 실제 패키지 그 자체를 전달할 필요없이 이 명단만 있으면 전달 가능
  - 프로젝트 파일의 크기를 줄일 수 있음