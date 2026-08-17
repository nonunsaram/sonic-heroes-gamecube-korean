# Sonic Heroes GC Korean Restoration v1.0

비공식 팬 복원 프로젝트입니다. 이 릴리스는 기존 한국 정발판 ISO를 수정한 것이 아니라, 일본판 *Sonic Heroes* GameCube 디스크와 그 안에 남아 있던 한국어 리소스를 바탕으로 한국어 플레이 경험을 복원한 xdelta 패치입니다.

목표는 2004년 한국어판의 분위기와 리소스를 최대한 보존하면서, 확인된 번역 오류·깨진 글리프·누락된 대사·그래픽 문제를 필요한 범위에서 보정하는 것입니다.

## 주요 특징

- 메뉴, 시스템, 게임플레이, 힌트, 인게임 컷신의 한국어 텍스트 및 자막 복원
- 기존 한국어 리소스와 2004년 스타일을 우선 보존한 폰트 복원
- 미사용·누락·깨진 글리프 보정
- Stage Intro / Mission Goal 화면의 한국어 미션 목표 그래픽 보정
- 스테이지·보스 이벤트와 컷신의 한국어 대사 경로 보완
- `Dr. 에그맨` 표기와 마침표 글리프 보정
- `event0402`의 잘못된 합성 글리프 제거 및 역사적 한국어 문구 복원
- Korean 모드 CG Theater 오른쪽 미리보기 패널의 공식 영문 `SONIC HEROES` 로고 적용

## 제공 패치

두 패치는 서로 독립적입니다. 반드시 원본 G9SJ8P ISO에 하나만 적용해 주세요.

### A. Corrected FullGame FINAL

파일:

```text
Sonic_Heroes_GC_JP_Korean_Correction_v1.0.xdelta
```

전체 한국어 복원에 확인된 오탈자, 폰트·글리프 보정, 미션 목표 그래픽 수정, 컷신·보스 이벤트 수정, CG Theater 로고 수정을 포함한 최종 수정판입니다.

### B. Korean Restoration Legacy v1.0

파일:

```text
Sonic_Heroes_GC_JP_Korean_Restoration_v1.0.xdelta
```

일본판 GameCube 디스크에 남아 있던 한국어 복원 데이터를 중심으로, 원래 한국어판의 표현과 리소스를 최대한 유지하는 보수적 복원판입니다.

## 설치 방법

### 필요한 파일

1. 사용자가 직접 준비한 Sonic Heroes GameCube 원본 ISO
2. xdelta3를 지원하는 패처

사용할 원본은 일본판 멀티랭귀지 디스크인 G9SJ8P여야 합니다. 이미 패치된 ISO, 다른 지역판 ISO, 한국 PS2 ISO를 원본으로 사용하지 마세요.

원본 확인값:

```text
File size: 1,459,978,240 bytes
SHA-256:   46585AF9446CF101021C7AC0BCCD1E7B9A7CF518D86904B1459E198DEDC93A3F
MD5:       F52DE0B95C82DE9D1974CEDD1F185488
```

### 데스크톱 패처

1. xdelta 패처를 실행합니다.
2. `Source / Original file`에 원본 G9SJ8P ISO를 선택합니다.
3. `Patch`에 원하는 xdelta 파일 하나를 선택합니다.
4. `Output`에 새 ISO 파일명을 지정합니다.
5. `Apply` 또는 `Patch`를 눌러 완료될 때까지 기다립니다.
6. 생성된 ISO를 Dolphin 또는 보유 중인 GameCube 환경에서 실행합니다.

두 xdelta를 차례로 적용하는 것이 아닙니다. 원하는 버전에 해당하는 패치 하나만 적용하세요.

### 브라우저 기반 Web Patcher

브라우저 기반 xdelta3 패처를 사용할 수도 있습니다.

1. xdelta3와 대용량 파일을 지원하는 신뢰할 수 있는 Web Patcher를 엽니다.
2. 원본 G9SJ8P ISO를 업로드합니다.
3. 두 패치 중 하나를 선택해 업로드합니다.
4. `Apply`를 실행합니다.
5. 생성된 ISO를 다운로드합니다.
6. 다운로드한 파일의 크기와 SHA-256을 `CHECKSUMS.txt`와 대조합니다.

원본 ISO는 약 1.46GB이므로 브라우저 패처의 파일 크기·메모리 제한에 걸릴 수 있습니다. 또한 신뢰하지 않는 웹사이트에 원본 ISO를 업로드하지 마세요. 문제가 있으면 데스크톱 xdelta3 패처를 사용하세요.

## 변경 사항

### 복원

- 기존 GameCube 디스크의 한국어 메뉴·시스템·게임플레이·힌트·자막 리소스 복원
- 한국어 폰트와 자막 렌더링 경로 복원
- 기존 한국어 리소스의 표현과 구조 보존

### 오류 수정

확인된 예시는 다음과 같습니다.

- `쉐도우` → `섀도우`
- `테일스` → `테일즈`
- `타겟` → `타깃`
- `개굴이` → `개구리 군`
- `Dr. 에그맨`의 공백·마침표 표시 보정
- 컷신·스테이지·보스 이벤트에서 누락되거나 잘못 연결된 한국어 대사 보정
- `event0402`: `메탈 소닉！ 결국 완성한 건가？！` 복원

### 그래픽 수정

- Stage Intro / Mission Goal 화면의 한국어 미션 목표 그래픽 수정
- Stage 13 Chaotix 미션의 `들키기` → `들키지` 보정
- Korean CG Theater 오른쪽 미리보기 패널의 일본어 로고를 공식 영문 `SONIC HEROES` 로고로 교체

## 개발 과정

- 원본 GameCube 디스크와 한국어 리소스 분석
- 폰트 시트, 글리프 메트릭, 자막·REL 구조 분석
- GameCube용 텍스처·아카이브·자막 리소스 패킹
- 에뮬레이터 런타임 확인, 정적 바이너리 검증, 주요 화면 플레이테스트

## 체크섬

패치 파일의 SHA-256, MD5, 크기와 패치 적용 결과의 기준값은 [`CHECKSUMS.txt`](CHECKSUMS.txt)에 정리되어 있습니다.

출력 ISO는 다음 크기여야 합니다.

```text
1,459,978,240 bytes
```

## 크레딧

- Sonic Heroes GC Korean Restoration Project contributors
- 한국어 리소스 검토 및 플레이테스트 참여자
- 원본 GameCube 리소스 및 한국어 폰트 연구에 기여한 자료 제공자
- xdelta3 프로젝트
- Dolphin Emulator 프로젝트 및 관련 디버깅 도구

이 프로젝트는 Sega의 공식 제품이나 공식 번역 패치가 아닙니다. 저작권이 있는 게임 ISO는 배포하지 않으며, 사용자는 합법적으로 취득한 원본 디스크에서 직접 ISO를 만들어 사용해야 합니다. 배포 대상은 이 프로젝트의 패치와 문서뿐입니다.

