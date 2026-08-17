# Sonic Heroes GameCube 한국어 복원 기록

> **이 문서는 일본판 GameCube판 `Sonic Heroes`에 남아 있던 미발매/숨겨진 한국어 로컬라이즈를 복원한 과정을 정리한 개발 기록입니다.**  
> 이 프로젝트는 SEGA/Nintendo가 실제로 발매한 “공식 한국 정발판”이 아닙니다. 다만 원본 디스크와 실행 코드 안에 남아 있던 한국어 언어 슬롯, 한국어 자산, 번역 데이터를 가능한 한 원래 구조대로 되살리는 것을 목표로 했습니다.

---

## 1. 프로젝트의 출발점

GameCube판 `Sonic Heroes` 일본판을 조사하던 중, 디스크 안에 단순한 흔적 수준을 넘어 **상당한 양의 한국어 리소스**가 남아 있다는 것을 발견했습니다.

대표적으로 다음과 같은 파일/리소스가 존재했습니다.

- `font/event*_k.txt`
- `hint*k.txt`
- 한국어 이벤트/힌트용 BMP + MET
- `text/TextKorean.utx`
- `startbtn_ko_GC.anm/.txd`
- `hintmenuk.bin/.txt`
- `advertise/K/...`
- 한국어 Stage Mission BMP
- 한국어 Pause 관련 archive
- 그 외 다수의 `K` / `_k` 언어별 자산

처음에는 이것이 단순한 개발 중간 산출물인지, 일부 기능만 남아 있는 것인지 알 수 없었습니다.

그래서 목표를 다음과 같이 잡았습니다.

> **“게임 안에 실제로 한국어 언어 슬롯이 존재하는가?”**  
> **“존재한다면 어디까지 자연스럽게 동작하는가?”**

---

## 2. 숨겨진 7번째 언어 슬롯

조사 결과 `Sonic Heroes`의 언어 값은 사실상 다음과 같은 구조를 가지고 있었습니다.

| Index | Language |
|---:|---|
| 0 | 日本語 |
| 1 | English |
| 2 | Français |
| 3 | Deutsch |
| 4 | Español |
| 5 | Italiano |
| **6** | **한국어** |

한국어는 단순한 리소스 이름만 존재하는 것이 아니라, **실행 코드가 실제로 값 `6`을 처리할 수 있는 언어 슬롯**으로 남아 있었습니다.

초기 테스트에서는 여러 subsystem의 language selector를 강제로 `6`으로 바꾸어 실제 동작 여부를 확인했습니다.

이 단계에서 다음 항목들이 한국어로 실제 동작함을 확인했습니다.

- CG / movie subtitle
- gameplay dialogue
- hint / instruction
- in-engine cutscene
- Main Menu
- Options
- Pause
- Stage Mission
- Stage Loading
- Omochao / Help
- Save/System text source

이 테스트들은 최종 패치가 아니라, **“원래 한국어 경로가 실제로 존재하는지 확인하기 위한 진단용 force patch”**였습니다.

---

## 3. `TextKorean.utx`와 Save/System 흰 네모 문제

Save/System 화면에서 한국어 문자열 자체는 정상적으로 선택되었지만, 처음에는 한글 대신 흰 네모가 출력되었습니다.

예를 들어 실제 문자열은:

```text
게임 데이터를 로드했습니다.
A 버튼을 누르십시오.
```

였지만 화면에서는 한글이 전부 missing-glyph box로 나타났고, ASCII `A`만 정상 표시되었습니다.

이 현상을 추적하면서 Save/System renderer가 다음 구조를 사용한다는 것을 확인했습니다.

```text
TextKorean.utx
    ↓ UTF-16 문자열
global NECFONT
    ↓ glyph lookup
Save/System renderer
```

문제는 GameCube 일본판의:

```text
./advertise/sega.prs
```

에 한국어 glyph가 없다는 점이었습니다.

---

## 4. PS2 한국 정발판의 `SEGA_K.PRS`

PS2 한국 정발판의 `SEGA_K.PRS`를 조사한 결과, GameCube의 `sega.prs`와 동일 계열의 NECFONT 구조를 사용한다는 사실을 확인했습니다.

초기 복원에서는 PS2 한국판 `SEGA_K.PRS`를 GameCube 쪽 `sega.prs` 대신 넣어 한국어 Save/System 렌더링을 성공시켰습니다.

하지만 Korean-only donor 폰트로 전역 `sega.prs`를 교체하면 일본어 glyph가 사라지는 regression이 발생했습니다.

최종적으로는:

- 원본 GC 8,176 glyph
- PS2 Korean donor-only 245 glyph

를 합쳐 **8,421 glyph multilingual NECFONT**를 만들었습니다.

이로써 일본어/서양어/한국어 Save/System을 하나의 폰트로 모두 처리할 수 있게 되었습니다.

---

## 5. Omochao / Help의 문자 처리

Omochao/Help는 실제 language state에 따라 문자 parser가 달라졌습니다.

- Japanese 계열 path → 2-byte 처리
- Western language path → 1-byte 처리

초기 force-patch 테스트에서는 Western language 상태에서 Korean resource가 깨지는 문제가 있었지만, 최종적으로 **actual global language = 6**을 사용하면 원래 코드가 한국어에 맞는 2-byte path를 자연스럽게 타는 것을 확인했습니다.

따라서 중간 테스트에서 사용했던 parser workaround는 최종판에서 제거되었습니다.

---

## 6. 한국어 Main Menu / Options / Pause

GameCube 디스크 안의 `advertise/K/`에는 한국어 메뉴 archive가 상당히 완성된 상태로 존재했습니다.

실제 런타임에서 다음과 같은 메뉴가 확인되었습니다.

- 메인 메뉴
- 1P / 2P 플레이
- 스토리
- 옵션
- 오디오 설정
- 게임 데이터
- 진동 설정
- 음악감상실
- 주제가
- 스테이지 BGM
- 기타

Pause 역시 `game_dispK.one` 계열에 한국어 리소스가 존재했습니다.

```text
계속하기
다시시작
그만하기
PAUSE
```

즉 이 영역은 새로 번역한 것이 아니라, **원래 GC 디스크에 들어 있던 한국어 자산을 활성화한 것**입니다.

---

## 7. Stage Loading과 미션 문구

Stage Loading에도 한국어 자산이 남아 있었습니다.

예를 들어 Stage 1의 미션 설명:

```text
고래 섬으로 가자!
```

와 같은 한국어 bitmap이 디스크 안에 존재했습니다.

GC판에서도 Korean language 6일 때 PS2 한국 정발판과 유사하게:

- `NOW LOADING`
- `STAGE01`
- Stage Name
- `MISSION`

등의 영문 chrome은 유지하고, 미션 설명을 한국어로 표시하도록 복원할 수 있었습니다.

---

## 8. 실제 한국어 선택 메뉴 복원

최종 목표는 강제 `language=6`이 아니라 **Options에서 사용자가 직접 한국어를 선택할 수 있게 만드는 것**이었습니다.

원본 Options controller는 0~5만 등록하지만, 한국어 asset 자체는 이미 존재했습니다.

확인된 Korean label:

- `option_082` — inactive `한국어`
- `option_103` — active `한국어`

최종적으로 controller를 0~6으로 확장하여:

```text
日本語
English
Français
Deutsch
Español
Italiano
한국어
```

7개 언어를 모두 선택할 수 있게 만들었습니다.

---

## 9. 다른 언어에서 한국어 항목이 보이지 않던 문제

처음 7번째 item을 등록했을 때 기능적으로는 한국어를 선택할 수 있었지만, J/E/F/G/S/I Options archive에는 Korean presentation이 없어서 **투명한 7번째 칸**처럼 보였습니다.

초기 RC에서는 K의 전체 `OPTION.ANM`을 다른 언어에 transplant했지만, 이 방식은 각 언어별 UI geometry를 덮어써 일부 글자가 찌그러지는 regression을 만들었습니다.

최종 해결 방식은:

- J/E/F/G/S/I 각각의 원본 geometry / UV / transform 보존
- 기존 6개 언어 presentation 유지
- Korean 7th item에 필요한 최소 object/state만 추가
- stock GC `option_082`, `option_103` 재사용

이었습니다.

---

## 10. Korean UI의 English active 글자 크기 차이

Korean Language Settings에서 `English`를 highlight하면 글자가 조금 크게 보이는 현상이 있었습니다.

비교 결과:

- inactive / active quad
- UV
- transform
- texture dimensions

는 동일했고, 차이는 **공식 K asset의 active raster 자체**에 있었습니다.

따라서 이것은 복원 과정에서 생긴 regression이 아니라 **stock Korean asset의 visual difference**로 판단하여 수정하지 않았습니다.

---

## 11. Title 화면

Korean `adv_title.one`은 일본어 타이틀 로고를 사용하고 있었습니다.

하지만 PS2 한국 정발판은 English `SONIC HEROES` logo를 사용했습니다.

GameCube 일본판 안에는 이미 `adv_title_us.one`이 존재했기 때문에, 새 graphic을 만들지 않고 **stock GC English title asset**을 Korean title route에 연결했습니다.

결과:

- English `SONIC HEROES` logo
- `Press START`

를 사용할 수 있게 되었습니다.

---

## 12. Idle / Attract Movie의 일본어 로고

타이틀 화면에서 일정 시간 기다리면 재생되는 attract/opening movie에는 일본어 로고가 **영상 프레임 자체에 baked-in** 되어 있었습니다.

JP ISO에는 `S1_JP_GC.sfd`만 존재했고 영어 variant는 없었습니다.

USA GameCube판을 조사한 결과 `S1_Eng_GC.sfd`가 존재했고, 동일한 GC SFD/MPEG program-stream 구조였습니다.

최종 Legacy v1.0에서는 **공식 USA GameCube attract movie를 donor로 사용**하여 영어 `SONIC HEROES` logo가 들어간 영상을 사용했습니다.

### USA GC movie donor

```text
S1_Eng_GC.sfd
Size: 0x030B3800
SHA-256:
36B4092358B87BC0A8D0DCE6451848D0C37FDD8597EF334C17F9129D79647D61
```

---

## 13. GameConfig language 6의 실제 저장

가장 중요한 질문은 이것이었습니다.

> 한국어 `6`이 단순한 임시 값인가?  
> 아니면 게임의 정상 설정/저장 시스템이 실제로 받아들이는 값인가?

Dolphin debugger에서 Korean 선택 순간을 직접 추적했습니다.

```text
r3 = GameConfig instance
r4 = 0x13
r0 = 0x06
```

그리고 실제 write:

```text
stbx r0,r3,r4
```

가 실행되는 것을 확인했습니다.

RAM에서도:

```text
GameConfig + 0x13 = 06
```

을 확인했습니다.

더 나아가:

1. Korean 선택
2. save
3. Dolphin 완전 종료
4. cold boot
5. save load
6. RAM 재확인

결과 language 값 `06`이 그대로 복원되었습니다.

따라서 Korean language index 6은 **게임의 정상 save/load 경로에서 실제로 저장되고 복원되는 값**임을 runtime에서 검증했습니다.

---

## 14. No Memory Card 버그와 Dolphin 디버깅

복원 과정에서 가장 흥미로운 문제 중 하나였습니다.

빈 Memory Card로 새 save를 만들면 Korean 6으로 정상 시작했지만, Memory Card 자체가 없는 상태에서 `저장하지 않고 시작`을 선택하면 Main Menu가 일본어로 돌아갔습니다.

Dolphin debugger에서 직접 RAM write를 추적했습니다.

감시한 주소:

```text
0x803E7848
```

이 값은 global save structure `0x803E782C + 0x1C`의 language field였습니다.

### 정상 Korean 6 write

부팅 초기화에서:

```text
0x80116E80
stb r0,0x001C(r31)
```

실행 시:

```text
r31 = 0x803E782C
r0  = 0x06
```

즉 `[0x803E7848] = 6`이 실제로 기록되었습니다.

### Start 이후 0 overwrite

Memory Write Breakpoint를 걸고 추적한 결과, 타이틀에서 Start를 누른 뒤:

```text
0x801171C8
stb r4,0x001C(r31)
```

에서:

```text
r4 = 0
```

이 들어가며 language field가 다시 0으로 초기화되는 것을 직접 잡았습니다.

원본 일본판에서는 기본 언어 자체가 `0`이므로 `0 → 0`이 되어 문제가 눈에 보이지 않습니다.

한국어 기본값 `6`을 복원하면서 이 잠복 초기화 문제가 처음 가시화되었습니다.

---

## 15. No-Save 최소 패치

최종적으로 no-card 전용 caller의 흐름을 조사해, 구조체 전체 초기화는 그대로 유지하면서 **language field만 다시 6으로 복구**하는 최소 패치를 설계했습니다.

기존 두 instruction 공간을 재사용하여:

```text
li    r0,6
stb   r0,0x001C(r3)
```

로 변경했습니다.

이 patch는 no-card initialization branch에만 적용되며:

- New Save
- Existing Save
- stored language 0~6

경로에는 영향을 주지 않도록 유지했습니다.

Dolphin runtime test에서:

```text
Memory Card 없음
→ Korean warning
→ Start Without Saving
→ Korean Main Menu
```

를 최종 확인했습니다.

---

## 16. 진단용 force patch 제거

프로젝트 초반에는 subsystem별로 language 6을 강제하는 patch가 다수 존재했습니다.

하지만 최종 목표는:

> **“각 화면을 억지로 Korean으로 만드는 것”이 아니라, 실제 global language 6을 선택하면 원래 엔진이 자연스럽게 Korean path를 타게 하는 것”**

이었습니다.

따라서 final Legacy build에서는 DOL force-Korean selector, parser workaround, stage REL 강제 등 진단용 patch를 제거했습니다.

---

## 17. 최종 Korean Restoration Core

Legacy v1.0의 핵심 복원 요소는 다음과 같이 정리할 수 있습니다.

1. **7번째 Korean language unlock** — language index 6을 정상 Options item으로 등록
2. **Native global language 6** — GameConfig와 save/load 시스템을 그대로 사용
3. **Multilingual NECFONT** — GC original glyph + PS2 Korean donor-only glyph
4. **Korean title routing** — stock GC English title asset 사용
5. **7-language Options presentation** — 각 언어 원본 presentation을 보존하면서 Korean item 추가
6. **Korean default for new profile** — 새 save에서 Korean 6을 기본값으로 사용
7. **No-card initialization fix** — `Start Without Saving`에서도 Korean 6 유지
8. **Official USA GC attract movie donor** — 영어 logo가 baked-in 된 `S1_Eng_GC.sfd` 사용

---

## 18. 실제 donor 사용 범위

이 프로젝트는 PS2판 전체를 GameCube에 이식한 것이 아닙니다.

### PS2 Korean — `SEGA_K.PRS`

용도:

> GameCube 일본판에 빠져 있던 Korean Save/System glyph 보충

PS2 실행 코드나 PS2 UI를 가져온 것이 아니라, 동일 계열 NECFONT의 Korean glyph data만 donor로 사용했습니다.

### GameCube USA — `S1_Eng_GC.sfd`

용도:

> JP ISO에 존재하지 않는 English attract movie

동일 GameCube 플랫폼의 공식 movie를 사용했습니다.

---

## 19. Legacy v1.0

최종 Legacy Restoration v1.0은 RC8과 binary-identical 상태로 동결되었습니다.

### Final ISO SHA-256

```text
BD9391146CF84883E603F089A228B591E27B87E52783220725AD896534369AE5
```

### Required clean source ISO SHA-256

```text
46585AF9446CF101021C7AC0BCCD1E7B9A7CF518D86904B1459E198DEDC93A3F
```

---

## 20. xdelta 배포

전체 ISO 자체는 배포하지 않고, clean source ISO에 적용하는 **xdelta patch** 형태로 배포합니다.

Legacy v1.0 xdelta:

```text
Size:
11,105,252 bytes
10.590794 MiB

SHA-256:
DD801EFCEF67D7FB747524DAF17F6E7A8235B7010EE6109094AABF29121E60DC
```

---

## 21. Runtime QA

최종 QA에서 확인한 항목:

- No Memory Card Korean boot
- Start Without Saving Korean 유지
- New Save Korean default
- Existing save language 0~6 보존
- 7-language Options
- 모든 언어에서 Korean item 표시/선택
- Japanese / English / Korean Save-System
- Korean Main Menu
- Korean Pause
- Korean Stage Mission
- Stage Loading
- Omochao / Help
- gameplay dialogue / hint
- in-engine cutscene
- CG/movie subtitle
- boss
- title
- Press START
- English attract movie

Team Dark 기준으로 실제 stage progression과 boss까지 smoke test를 수행했습니다.

---

## 22. Legacy와 Modernized

복원 작업이 끝난 뒤 프로젝트는 다음 구조로 분리했습니다.

```text
ONE PATCH ENGINE
+
TWO DATA PROFILES
```

### Legacy / Original Korean Restoration

- 숨겨진 GC Korean localization을 최대한 원형 보존
- 당시 공식 번역 표현 유지
- verified stock quirks도 그대로 유지

### Modernized Korean

- 동일 Restoration Core 사용
- 번역/용어/문장만 현대적으로 수정
- Legacy data는 immutable reference로 유지

Modernized translation은 JSON 기반 master localization database와 viewer/validator/exporter를 통해 관리하도록 파이프라인을 구축했고, 이후 최종 Modernized profile도 완성했습니다.

---

## 23. 이 프로젝트에서 가장 중요한 발견

처음에는 단순히:

> “GC판에 한국어 파일 몇 개가 남아 있다.”

정도로 보였습니다.

하지만 실제 조사 결과:

- Korean language index `6`
- Korean UTX
- Korean dialogue/hint
- Korean menu archives
- Korean Pause
- Korean mission assets
- Korean stage/cutscene routing
- Korean language labels
- save/load compatibility

까지 상당한 구조가 그대로 남아 있었습니다.

즉 GameCube 일본판에는 **단순한 번역 잔여물이 아니라, 실제로 동작 가능한 미발매 Korean language path가 상당 부분 남아 있었다**고 보는 것이 가장 정확합니다.

이것을 실제 발매된 “공식 한국 정발판”이라고 부를 수는 없습니다.

더 정확한 표현은:

> **“Japanese GameCube release에 남아 있던 unreleased / hidden Korean localization의 restoration”**

입니다.

---

## 24. 마치며

이 프로젝트는 처음에는 단순한 자산 조사로 시작했습니다.

그러나 마지막에는:

- PowerPC assembly
- DOL / REL
- runtime language state
- GameConfig
- RAM inspection
- register tracing
- call stack
- execution breakpoint
- memory write breakpoint
- archive reconstruction
- font format analysis
- xdelta reproducibility

까지 직접 확인하게 되었습니다.

특히 `Start Without Saving` 문제는 Dolphin의 memory breakpoint를 이용해:

```text
06이 들어가는 순간
→ 00으로 덮어쓰는 순간
→ 그 값을 GameConfig에 적용하는 순간
```

을 실제 실행 중에 추적한 뒤 최소 patch로 해결했습니다.

기능적으로는 이미 훨씬 전에 “한국어로 플레이 가능한 상태”에 도달했지만, 최종 목표는 단순히 **한국어가 나오는 버전**이 아니었습니다.

> **“실제로 GameCube 한국 정발판이 존재했다면 이 정도로 자연스럽게 동작했을 것 같다.”**

라는 느낌을 최대한 살리는 것이 목표였습니다.

그 결과가 이 Restoration 프로젝트입니다.

---

## Disclaimer

This is an unofficial fan restoration project.

- No complete game ISO is distributed.
- Patches are intended for legally obtained copies of the game.
- `Sonic Heroes`, Sonic the Hedgehog, SEGA, Nintendo, and all related trademarks and copyrighted assets belong to their respective owners.
- This project is not affiliated with or endorsed by SEGA or Nintendo.
