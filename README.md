# Sonic Heroes GC Korean Restoration v1.0

비공식 팬 복원 프로젝트입니다. 이 릴리스는 일본판 *Sonic Heroes* GameCube 디스크와 그 안에 남아 있던 한국어 리소스를 바탕으로 한국어 플레이 경험을 복원한 xdelta 패치입니다.

목표는 2004년 한국어판의 분위기와 리소스를 최대한 보존하면서, 확인된 번역 오류·깨진 글리프·누락된 대사·그래픽 문제를 필요한 범위에서 보정하는 것입니다.

## 주요 특징

- 소닉 히어로즈 게임큐브판에 들어있던 미사용 한국어 텍스트 및 UI 복원 (메뉴, 시스템, 게임플레이, 힌트, 인게임 컷신)
- 복원판의 경우 YBM 시사영어의 번역을 그대로 사용. Playstation 2 버전의 한국어 번역과 동일
- 수정판의 경우 2004년 번역을 기반으로 2026년 세가코리아 공식 명칭으로 수정 및 심각한 번역 오류 수정

## 복원 기록 요약

이 프로젝트는 일본판 GameCube 디스크에 남아 있던 숨겨진 한국어 언어 경로와 자산을 조사해, 실제 게임의 언어 선택·저장·불러오기 경로에서 한국어가 동작하도록 복원한 작업입니다. Options의 7번째 언어 슬롯 `6 = 한국어`를 활성화하고, Save/System용 다국어 폰트와 Omochao / Help의 한국어 문자 경로를 보완했습니다.

복원판을 기반으로 한 수정판에서는 2004년 한국어판의 문체와 자산을 우선 보존하면서, 확인된 고유명사·맞춤법·띄어쓰기·명백한 오역을 교정했습니다.

자세한 개발 기록은 다음 문서에서 확인할 수 있습니다.

- [전체 복원·교정 기록 및 변경 사항](SONIC_HEROES_GC_KOREAN_RESTORATION_FULL_CHANGELOG.md)
- [Sonic Heroes GameCube 한국어 복원 기록](SONIC_HEROES_GC_KOREAN_RESTORATION_HISTORY.md)

## 제공 패치

두 패치는 서로 독립적입니다. 반드시 원본 G9SJ8P ISO에 하나만 적용해 주세요.

### A. 수정판 (Correction)

파일:

```text
Sonic_Heroes_GC_JP_Korean_Correction_v1.0.xdelta
```

전체 한국어 복원에 확인된 오탈자, 폰트·글리프 보정, 미션 목표 그래픽 수정, 컷신·보스 이벤트 수정, CG Theater 로고 수정을 포함한 최종 수정판입니다.

### B. 복원판 (Restoration)

파일:

```text
Sonic_Heroes_GC_JP_Korean_Restoration_v1.0.xdelta
```

일본판 GameCube 디스크에 남아 있던 한국어 복원 데이터를 중심으로, 원래 한국어판의 표현과 리소스를 최대한 유지하는 보수적 복원판입니다.

## 추가 자료: 세이브 및 Gecko 코드

개발·검증 과정에서 제작한 G9SJ8P 일본판용 자료도 함께 제공합니다.

- [Sonic Heroes JP 100% Save](extras/Sonic_Heroes_JP_100Percent_Save.gci) — 스토리와 주요 콘텐츠가 해금된 100% 세이브
- [Sonic Heroes JP Gecko Codes G9SJ8P](extras/Sonic_Heroes_JP_Gecko_Codes_G9SJ8P.ini) — Dolphin용 Gecko 코드 설정 파일

Gecko 코드에는 다음 기능이 포함되어 있습니다.

- **Debug Level Select (All Modes)** — 가장 중요한 디버그 모드 셀렉트 기능
- Story Mode 전체 레벨 해금
- 무적 및 피격 반응 제거
- 항상 레벨 3 유지
- 팀 블래스트 게이지 항상 최대

Dolphin에서는 게임 속성의 Gecko Codes 탭에서 코드를 확인·활성화하거나, 설정 파일을 다음 위치에 복사할 수 있습니다.

```text
C:\Users\<사용자명>\AppData\Roaming\Dolphin Emulator\GameSettings\G9SJ8P.ini
```

사용 중인 Dolphin의 사용자 폴더 위치가 다르면 Dolphin의 `Open User Folder`에서 `GameSettings` 폴더를 찾아 주세요. `.gci` 세이브는 Dolphin 메모리 카드 관리 기능으로 가져오면 됩니다.

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

### 수정판에서의 대표적인 변경 목록

- `쉐도우` → `섀도우`
- `테일스` → `테일즈`
- `챠미` → `차미`
- `닥터 에그맨` → `Dr. 에그맨` (닥터라고만 부르거나 에그맨이라고만 부르는 경우는 유지)
- `개굴이` → `개구리 군`
- `타겟` → `타깃`
- `팀웍` → `팀워크`
- 팀 카오틱스 Lost Jungle 엑스트라 미션의 목표 "챠오 10마리를 구출하자！" → "챠오 20마리를 구출하자！"
- 팀 카오틱스 Egg Fleet의 목표 "적에게 들키기 말고 전함에 잠입하자！" → "적에게 들키지 말고 전함에 잠입하자！"
- 팀 로즈 Casino Park의 목표 "커다란 핀볼을 사용해 보자！" → "커다란 핀볼에서 놀아보자！"
- 팀 다크 Rail Canyon 인트로에서 "이 앞은 에그맨의 지하요새다"를 "이 앞은 에그맨의 지상요새다"로 수정 (원문: 地上要塞)
- 기타 직역, 어색한 문구, 오류 등을 수정

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
