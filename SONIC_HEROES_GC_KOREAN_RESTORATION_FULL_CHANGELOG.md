# Sonic Heroes GameCube Korean Restoration — 전체 복원·교정 기록 및 변경 사항

> **Sonic Heroes 일본판 GameCube 디스크에 남아 있던 숨겨진/미발매 한국어 로컬라이즈를 실제 게임의 언어 경로로 복원하고, 그 위에서 2004년 당시 한국어 번역의 명백한 오류·표기 문제·런타임 버그를 교정한 비공식 팬 프로젝트입니다.**
>
> 이 프로젝트는 존재하지 않았던 “공식 한국 GameCube 정발판”을 재현한 것은 아닙니다. **일본판 GameCube 릴리스 내부에 남아 있던 Korean language path와 한국어 자산을 복원한 뒤, 별도의 Corrected 패치에서 이를 보수적으로 교정한 것**입니다.
> 이 글은 ChatGPT 5.6 Sol 이 복원 과정을 요약하여 하나의 글로 정리했습니다.

---

## 1. 배포판 구성

최종 배포는 성격이 다른 두 패치로 나뉩니다.

### Korean Restoration / Legacy v1.0

숨겨진 GameCube 한국어 로컬라이즈를 가능한 한 **원형 그대로 복원**하는 버전입니다.

- 원본 디스크의 한국어 문장과 그래픽을 최대한 유지
- 7번째 언어 슬롯 `6 = 한국어` 활성화
- Options에서 한국어를 직접 선택 가능
- 저장/불러오기 및 콜드 부트에서도 language 6 유지
- 한국어 Main Menu / Options / Pause / Stage Mission / Gameplay Hint / Cutscene / Save-System 등을 원래 엔진 경로로 활성화
- 검증된 stock quirk는 임의로 수정하지 않음

### Korean Corrected

Legacy Restoration Core를 기반으로, 실제 플레이에 권장할 수 있도록 **고유명사, 맞춤법, 띄어쓰기, 명백한 오역, 깨진 글리프, 잘못된 미션 문구, 런타임 통합 문제 등을 교정**한 버전입니다.

번역 교정의 기본 원칙은 다음과 같습니다.

> **의미는 일본어 원문이 심판하고, 문체는 2004년 YBM 한국어판의 느낌을 최대한 존중한다.**

즉 전면 재번역이 아니라, 당시 번역의 말투와 캐릭터성을 최대한 살리면서 **명백히 고칠 이유가 있는 부분만 수정**했습니다.

---

# Part I. 숨겨진 GameCube 한국어판 복원

## 2. 일본판 디스크 안에서 발견된 한국어 자산

조사 시작 당시 GameCube 일본판에는 단순한 문자열 몇 개가 아니라 상당히 완성된 한국어 리소스가 남아 있었습니다.

대표적으로 확인한 항목은 다음과 같습니다.

- `font/event*_k.txt`
- `font/hint*k.txt`
- 한국어 이벤트/힌트 BMP + MET 폰트
- `text/TextKorean.utx`
- `startbtn_ko_GC.anm/.txd`
- `hintmenuk.bin/.txt`
- `advertise/K/...`
- 한국어 Stage Mission BMP
- 한국어 Pause 관련 archive
- 한국어 메뉴 및 시스템 그래픽
- 한국어 gameplay/cutscene routing에 사용되는 언어별 리소스

초기에는 이것이 단순한 개발 잔여물인지 알 수 없었지만, 런타임 조사 결과 게임 코드에도 **실제로 처리 가능한 7번째 언어 값**이 남아 있었습니다.

---

## 3. 숨겨진 7번째 언어 슬롯 `6 = 한국어`

게임의 언어 값은 다음 구조를 가집니다.

| Index | Language |
|---:|---|
| 0 | 日本語 |
| 1 | English |
| 2 | Français |
| 3 | Deutsch |
| 4 | Español |
| 5 | Italiano |
| **6** | **한국어** |

초기 디버깅에서는 여러 subsystem의 language selector를 강제로 `6`으로 만들어 한국어 경로의 존재를 확인했습니다.

그 결과 다음 영역이 실제 한국어 자산을 정상적으로 읽을 수 있음을 확인했습니다.

- CG / movie subtitle
- gameplay dialogue
- hint / instruction
- in-engine cutscene
- Main Menu
- Options
- Pause
- Stage Mission / Stage Loading
- Omochao / Help
- Save / System

최종판에서는 이 진단용 force patch를 유지하지 않았습니다.

목표는 화면마다 억지로 한국어를 강제하는 것이 아니라,

> **Options에서 실제 global language 6을 선택하면 원래 엔진이 자연스럽게 Korean path를 타게 만드는 것**

이었습니다.

---

## 4. Options에 실제 한국어 항목 추가

원본 Options controller는 0~5까지만 노출하지만 한국어 label asset 자체는 이미 존재했습니다.

확인된 stock asset:

- `option_082` — inactive `한국어`
- `option_103` — active `한국어`

Options controller를 0~6으로 확장하여 다음 7개 항목을 모두 선택할 수 있게 했습니다.

```text
日本語
English
Français
Deutsch
Español
Italiano
한국어
```

초기에는 다른 언어의 Options 화면에서 7번째 한국어 항목이 투명하게 보이는 문제가 있었습니다.

Korean `OPTION.ANM` 전체를 다른 언어에 이식하는 방법도 시험했지만 각 언어의 geometry를 덮어쓰면서 UI가 찌그러지는 regression이 발생했습니다.

최종적으로는:

- 각 언어 원본 geometry / UV / transform 유지
- 기존 6개 언어 presentation 유지
- Korean 7th item에 필요한 최소 object/state만 추가
- stock GC Korean label asset 재사용

방식으로 해결했습니다.

---

## 5. Save/System 한글 흰 네모 문제와 multilingual NECFONT

`TextKorean.utx`의 한국어 문자열은 실제로 정상 선택되었지만 Save/System 화면에서는 처음에 한글이 흰 네모로 출력됐습니다.

예:

```text
게임 데이터를 로드했습니다.
A 버튼을 누르십시오.
```

문자열 문제가 아니라 Save/System renderer가 global NECFONT를 사용하고 있었고, 일본판 GC의 `advertise/sega.prs`에 필요한 한국어 glyph가 없었던 것이 원인이었습니다.

PS2 한국어판의 `SEGA_K.PRS`가 같은 계열 NECFONT 구조임을 확인한 뒤:

- GameCube 원본: **8,176 glyph**
- PS2 Korean donor-only: **245 glyph**

를 합쳐 **8,421 glyph multilingual NECFONT**를 제작했습니다.

단순히 PS2 한국어 폰트로 통째 교체하면 일본어 glyph가 사라지기 때문에, **GC 원본 record를 우선하고 GC에 없는 Korean record만 donor로 보충**했습니다.

결과적으로 일본어/서양어/한국어 Save-System을 하나의 font로 모두 처리할 수 있게 되었습니다.

---

## 6. Omochao / Help의 2-byte 한국어 경로 복원

Omochao/Help는 language state에 따라 parser가 달랐습니다.

- Japanese/Korean 계열: 2-byte
- Western language 계열: 1-byte

초기 강제 패치에서는 Western parser 상태에서 한국어 리소스를 읽어 글자가 깨졌지만, 실제 global language 값을 `6`으로 사용하면 엔진이 자연스럽게 Korean 2-byte path를 선택한다는 것을 확인했습니다.

따라서 중간 단계의 parser workaround도 최종판에서는 제거했습니다.

---

## 7. Main Menu / Pause / Stage Loading 복원

디스크의 `advertise/K/`와 관련 archive에는 상당히 완성된 한국어 메뉴가 남아 있었습니다.

복원된 예:

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

Pause 화면도 stock Korean archive를 이용하여:

```text
계속하기
다시시작
그만하기
```

등이 정상 표시됩니다.

Stage Loading / Mission 역시 원본 한국어 bitmap을 통해 한국어로 표시되도록 복원했습니다.

---

## 8. 타이틀 로고와 Attract Movie

Korean `adv_title.one`은 일본어 타이틀 로고를 사용하고 있었지만 GameCube 디스크 안에는 이미 `adv_title_us.one`이 존재했습니다.

새 그래픽을 만들지 않고 **stock GC English `SONIC HEROES` title asset**을 Korean title route에 연결했습니다.

또한 일정 시간 후 재생되는 오프닝/Attract Movie의 일본어 로고는 영상 프레임 자체에 baked-in 되어 있어 JP ISO만으로는 교체가 불가능했습니다.

USA GameCube판의 공식 `S1_Eng_GC.sfd`를 donor로 사용하여 영문 `SONIC HEROES` 로고가 들어간 동일 플랫폼 영상을 사용했습니다.

---

## 9. 한국어 설정의 저장/불러오기 검증

Dolphin debugger에서 Korean 선택 순간의 GameConfig write를 직접 추적했습니다.

확인된 핵심 상태:

```text
GameConfig + 0x13 = 06
```

다음 순서로 실제 저장 지속성을 검증했습니다.

1. 한국어 선택
2. 정상 save
3. Dolphin 완전 종료
4. cold boot
5. save load
6. RAM 재확인

결과 language `06`이 정상 복원되었습니다.

즉 Korean language 6은 임시 강제 값이 아니라 **게임의 원래 save/load 경로가 실제로 저장하고 복원할 수 있는 값**입니다.

---

## 10. No Memory Card / Start Without Saving 버그

Memory Card가 없을 때 `저장하지 않고 시작`을 선택하면 한국어로 부팅한 뒤 Main Menu가 일본어로 돌아가는 문제가 있었습니다.

Memory Write Breakpoint로 추적한 결과 부팅 시 language 6이 정상 기록된 뒤, no-card 초기화 경로에서 다시 `0`으로 덮어쓰는 instruction을 확인했습니다.

구조체 전체 초기화는 그대로 유지하고 no-card branch에서 language field만 다시 `6`으로 복원하는 최소 패치를 적용했습니다.

검증:

```text
Memory Card 없음
→ Korean warning
→ Start Without Saving
→ Korean Main Menu
```

Existing Save와 다른 언어의 저장 값은 그대로 보존합니다.

---

# Part II. Corrected 한국어판 제작

## 11. 전체 한국어 데이터 추출

Corrected 작업은 게임 화면을 눈으로만 고치는 방식이 아니라, 한국어 텍스트 subsystem을 구조화하여 전수 추출하는 것으로 시작했습니다.

### Cutscene / Event

- **67 events**
- **492 records**
  - 475 dialogue
  - 17 SFX / non-dialogue

주요 런타임 데이터:

- `movieD.rel`
- `EventMessage` table
- 각 `eventXXXX_k.txt / .met / .bmp`

### Gameplay Dialogue / Hint

한국어 `hint*.bin` 27개를 분석해 고정 12-byte record + string pool 구조를 규명했습니다.

- **10,931 physical records**
- **4,048 unique JA/KO pair groups**
- 일본어 대응 관계 100% 추출

### System / UI

`TextKorean.utx`의 big-endian UTF-16 section 구조를 분석했습니다.

- **238 records**
- 10 sections
- 145 populated Korean records
- **93 blank SEC_03 records는 의도적으로 그대로 유지**

### Mission Objective

뒤늦게 미션 목표 문장이 텍스트가 아니라 별도의 pre-rendered bitmap임을 발견했습니다.

- **122 Korean mission BMP**
- 기본 형식: 512×64 / 4bpp / 16-color indexed bitmap

이 발견 덕분에 한국어 텍스트 subsystem을 사실상 전 범위로 검토할 수 있게 되었습니다.

---

## 12. 교정 철학

Corrected판은 “현대식으로 새로 번역한 버전”이 아닙니다.

기본 원칙:

> **의미는 일본어 원문이 심판하고, 문체는 YBM을 최대한 존중한다.**

따라서 다음과 같은 경우에 주로 수정했습니다.

- 현재 공식 표기와 명확히 다른 고유명사
- 객관적인 맞춤법/띄어쓰기 오류
- 의미가 반대로 된 오역
- 원문 의미가 크게 누락된 경우
- 폰트 제약 때문에 생긴 것으로 보이는 비정상 표기
- 실제 미션 조건과 문구가 다른 경우
- 런타임에서 글자가 깨지거나 사라지는 경우

반대로 단순히 “더 세련되게 번역할 수 있다”는 이유만으로 2004년 문체를 전면적으로 갈아엎지는 않았습니다.

---

## 13. 주요 용어 통일

최종 Corrected판의 대표 표기:

| 기존/혼재 표기 | 최종 표기 |
|---|---|
| 테일스 | **테일즈** |
| 쉐도우 | **섀도우** |
| 챠미 | **차미** |
| 쵸코라 | **초코라** |
| 타겟 | **타깃** |
| 팀웍 | **팀워크** |
| 닥터 에그맨 | **Dr. 에그맨** |
| 개굴이 / 개굴아 | **개구리 군** |
| 바늘 두더지 / 두더쥐 | **가시두더지** |

### 의도적으로 유지한 표기

`チャオ`는 프로젝트 표준을 **`챠오`**로 정했습니다.

즉:

- 차오 → 사용하지 않음
- **챠오 → 최종 표기**

Froggy는 일본어 호칭에 따라:

- `カエルくん / カエル君` → **개구리 군**
- `カエルさん` → **개구리 씨**
- bare `カエル` → **개구리**

원칙으로 정리했습니다.

---

## 14. 맞춤법·띄어쓰기·표현 교정

전체 corpus를 규칙 기반 검사와 별도 독립 번역 검토 세션으로 반복 교차 검수했습니다.

대표 수정 예:

```text
틀림없을거야
→ 틀림없을 거야

좋지않네요
→ 좋지 않네요

하나 밖에
→ 하나밖에

더이상
→ 더 이상

맛 보거라
→ 맛보거라

신경쓰여
→ 신경 쓰여

속인건
→ 속인 건

꼴 사납게 됐다구
→ 꼴사납게 됐다고

저게 메탈 소닉이라구!?
→ 저게 메탈 소닉이라고!?
```

### 수정하지 않은 표현

플레이테스트 중 다음 표현도 재검토했지만 문법상 정상이라 유지했습니다.

```text
나쁘지 않은걸?
너무 아름다운걸?
```

여기서 `-ㄴ걸`은 문장 종결 어미이므로 붙여쓰기가 맞다고 판단했습니다.

Casino Park의 `릴을 멈출 수 있어`에서 **릴(reel)** 역시 슬롯머신의 회전 릴을 뜻하는 정상 용어로 보고 유지했습니다.

---

## 15. 대표적인 의미 오류 수정

### 지하요새 → 지상요새

Team Dark Rail Canyon 인트로:

```text
이 앞은 에그맨의 지하요새다
```

일본어 원문은 `地上要塞`이므로:

```text
이 앞은 에그맨의 지상요새다
```

로 수정했습니다.

### 월세를 위해서 → 밀린 월세 때문에

Team Chaotix Grand Metropolis에서 일본어의 체납 월세 뉘앙스가 사라진 문장을:

```text
월세를 위해서！
→ 밀린 월세 때문에！
```

로 수정했습니다.

### Team Chaotix `악즉멸`

Egg Emperor 진입 이벤트 `event0316`:

```text
받아라！ 닌자의 기술
눈을 크게 뜨고 봐라！
```

를 일본어:

```text
悪即滅！
忍びの奥義括目せよ！
```

에 맞춰:

```text
악즉멸！ 닌자의 오의를
똑똑히 봐라！
```

로 수정했습니다.

이 이벤트는 초기 정적 분석에서 미사용으로 오판했지만, **인간 플레이테스트에서 Boss 7 / Egg Emperor에서 실제 실행되는 것을 확인**했습니다.

---

# Part III. 폰트 및 렌더링 수정

## 16. `섀`, `깃`, `웬`, `쌰` 등 글리프 보강

Corrected 텍스트는 2004년 원본 번역에 없던 글자를 요구하는 경우가 있었습니다.

대표적으로:

- `섀` — 섀도우
- `깃` — 타깃
- `웬` — 웬 놈
- `쌰` — 기존 2004 gameplay 문장 `으쌰！`

등을 처리했습니다.

`섀`, `깃`, `웬`은 프로젝트에서 검수한 수동/보존 글리프를 사용했고, 다른 글자들은 가능한 경우 **같은 2004 GameCube 폰트에 존재하는 exact same-codepoint donor**를 우선 활용했습니다.

---

## 17. Gameplay PNG decoder 버그 수정

Test2에서 gameplay `섀`가 깨진 채 출력되는 실제 런타임 버그를 발견했습니다.

원인은 폰트 빌더가 PNG의 압축을 풀면서도 **PNG scanline filter를 복원하지 않고 raw difference byte를 픽셀처럼 읽은 것**이었습니다.

`섀`와 `깃` PNG에는 Filter Type 2(Up), 4(Paeth)가 사용되어 깨졌지만, Filter 0으로 만들어진 `쌰`는 우연히 정상 출력되었습니다.

최종 빌더에서는 RFC 2083 방식으로:

- None
- Sub
- Up
- Average
- Paeth

필터를 모두 정상 unfilter하도록 수정했습니다.

그 결과 gameplay `섀`, `깃`, `쌰`의 atlas 삽입 결과를 원본 승인 PNG와 pixel-exact로 검증했습니다.

---

## 18. Event MET serializer 회귀 버그

초기 Corrected font build에서는 `.met` 파일을 새로 serialize하면서 원본의 필수 header가 사라져 **일부 이벤트의 자막 트랙 전체가 나오지 않는 문제**도 있었습니다.

필수 형식:

```text
METRICS1
<BMP filename>
5
```

이후 metric rows가 이어집니다.

최종 파이프라인에서는 원본 MET/TXT의 CRLF, header, 기존 byte 구조를 최대한 보존하고 필요한 record만 최소 추가하는 방식을 사용했습니다.

---

## 19. `event0217` — `개구리 군！`이 `개구 ！`로 잘리던 문제

Team Rose 컷신에서:

```text
개구리 군！
```

이:

```text
개구 ！
```

로 출력되는 실제 런타임 오류를 발견했습니다.

`event0217`에는 k00/k01 page가 존재하는데, 빌더가 새 `리`, `군` 글리프를 runtime이 기대하지 않는 page에 넣은 것이 원인이었습니다.

최종적으로 runtime mapping을 추적한 뒤 Sheet 0의 올바른 record에 배치해 정상 출력되는 것을 인간 플레이테스트로 확인했습니다.

---

## 20. `Dr. 에그맨` — 명칭과 타이포그래피

현재 SEGA 한국어 표기를 조사한 뒤 Corrected판의 정식 표기를:

```text
Dr. 에그맨
```

으로 통일했습니다.

다만 GameCube 폰트에서 Latin과 한글의 폭이 달라 여러 번 실제 화면 비교가 필요했습니다.

원본 GameCube 한국어 자산에서:

- `VIP`
- `OK`
- `TV`
- `CG`

같은 Latin 문자열이 12px halfwidth cell을 사용한다는 사실을 확인했습니다.

최종 정책:

- `D` = 12px
- `r` = 12px
- `.` = **24px**
- ASCII space = 12px

처음에는 period도 12px로 사용했지만 실제 화면에서는 `Dr.에그맨`처럼 붙어 보였습니다.

따라서 **Latin 글자는 원본 halfwidth 리듬을 유지하되 마침표만 원본 24px punctuation cell을 사용**하도록 조정했고, CG / 보스 컷신 / gameplay hint에서 모두 실제 화면 확인을 마쳤습니다.

### `Dr2에그맨` 버그

한 빌드에서는 period donor 좌표를 잘못 잡아 숫자 `2` 그림을 마침표로 삽입해:

```text
Dr2에그맨
```

처럼 출력되는 문제도 있었습니다.

원본 폰트의 실제 period donor를 다시 추적해 해결했습니다.

---

## 21. Final Story `렀` 글리프와 최종 롤백 결정

Final Story `event0402`의 일본어 의미를 더 직접적으로 살리기 위해 한때:

```text
메탈 소닉！
기어코 일을 저질렀구나？！
```

를 사용했습니다.

하지만 실제 런타임에서 `렀`이 이상하게 출력되어 전체 원본 GameCube Korean corpus를 전수 검색한 결과, **2004년 GC 한국어 폰트에는 `렀` 자체가 존재하지 않는 것**을 확인했습니다.

`섀`나 `깃`처럼 프로젝트 핵심 용어를 위해 반드시 새 글리프를 만들어야 하는 경우와 달리, 이 문장은 원본 번역도 충분히 자연스럽다고 판단했습니다.

최종판:

```text
메탈 소닉！
결국 완성한 건가？！
```

로 복원하고 임시 합성 `렀` 글리프는 완전히 제거했습니다.

즉 `메탈 소닉`이라는 의미 보강은 유지하면서, 두 번째 문장은 2004 번역과 정품 glyph를 보존했습니다.

---

# Part IV. Gameplay / UI / Mission 교정

## 22. Gameplay Hint 전수 추출 및 교정

Gameplay hint subsystem:

- 27 Korean `hint*.bin`
- 10,931 physical records
- 4,048 unique Japanese/Korean pair groups

를 구조적으로 추출했습니다.

대표적으로 수정한 범위:

- 공식 고유명사
- 타깃 표기
- 객관적 띄어쓰기
- 명백한 오역
- 버튼/플랫폼 잔재 검증
- literal `\n` 제어 문자 보존

특히 `HINT_022_0009`에서는 줄바꿈이 실제 LF가 아니라 literal bytes `5C 6E` (`\n`)이라는 것을 확인해 2행 구조가 깨지지 않도록 보존했습니다.

---

## 23. System / UI UTX 교정

`TextKorean.utx`:

- 238 records
- 10 sections

을 분석해 variable-length reinsertion을 구현했습니다.

SEC_03의 93개 빈 slot은 일본어에는 내용이 있어도 한국어/영어 stock 데이터에서 비어 있던 영역이므로 **Corrected판에서도 의도적으로 채우지 않고 유지**했습니다.

UI는 번역 개선보다 원본 GameCube 동작 보존을 우선했습니다.

---

## 24. Mission Objective bitmap 전수 조사

플레이테스트 중 Egg Fleet의:

```text
적에게 들키기 말고 전함에 잠입하자！
```

라는 오타를 발견하면서 미션 목표가 일반 텍스트 subsystem이 아니라 **별도의 pre-rendered BMP**임을 뒤늦게 확인했습니다.

한국어 mission bitmap은 총 **122개**였습니다.

수정한 최종 6개 문구:

### Lost Jungle / Team Chaotix Extra

```text
챠오 10마리를 구출하자！
→ 챠오 20마리를 구출하자！
```

실제 게임 조건도 20마리임을 플레이로 확인했습니다.

### Egg Fleet / Team Chaotix

```text
적에게 들키기 말고 전함에 잠입하자！
→ 적에게 들키지 말고 전함에 잠입하자！
```

### Bullet Station / Team Dark

```text
에그맨 기지를 전멸시켜라！
→ 에그맨 기지를 파괴하자！
```

### Casino Park / Team Rose

```text
커다란 핀볼을 사용해 보자！
→ 커다란 핀볼에서 놀아보자！
```

### BINGO Highway / Team Rose

```text
빙고를 맞추면서 달려나가자！
→ 빙고 슬라이더를 타 보자！
```

### Final Fortress / Team Chaotix

```text
의뢰인의 감옥 열쇠를 찾아라！
→ 의뢰인이 갇힌 감옥 열쇠를 찾아라！
```

이 bitmap들은 최종적으로 사람이 직접 원본 폰트/도너를 참고해 조판했으며, 게임용 4bpp 16색 BMP로 다시 변환한 뒤 **roundtrip 0 pixel diff**를 검증했습니다.

### 의도적으로 유지한 문구

Hang Castle / Team Rose의:

```text
유령의 성을 찾아라！
```

는 더 직역적인 수정안을 검토했지만, 수정 가치 대비 그래픽 작업 범위가 크고 기존 표현도 게임 진행에 문제가 없어 그대로 유지했습니다.

---

# Part V. 가장 큰 런타임 통합 버그 — Stage REL

## 25. Test4까지 스테이지 인트로 교정이 반영되지 않았던 이유

정적 검증만 보면 Test4는 거의 완성된 것처럼 보였지만, 실제 인간 QA에서 다음과 같은 문제가 반복 발견됐습니다.

예:

```text
팀웍
테일스
쉐도우
지하요새
개굴이
닥터 에그맨
챠미
```

Gameplay hint나 CG에서는 이미 교정됐지만 **스테이지 진입 전 실시간 컷신과 보스 이벤트만 2004 문자열을 계속 출력**했습니다.

원인은 구조적이었습니다.

Sonic Team은 492-record Event Subtitle system을 `movieD.rel`에만 둔 것이 아니라:

- `movieD.rel`
- **15개의 `stageXXD.rel`**

총 **16개 REL 모듈에 중복 컴파일**해 두었습니다.

Test1~Test4 빌더는 `movieD.rel`만 수정하고 stage REL의 Korean string table은 그대로 두고 있었습니다.

---

## 26. Test5 — 16개 REL 전체 교정

최종적으로 `movieD.rel + 15 stageXXD.rel`의 모든 Korean EventMessage table/string pool을 함께 패치하도록 파이프라인을 수정했습니다.

- 각 REL의 492 record 재매핑
- string pool 재패킹
- relocation addend 재계산
- 16개 REL × 492 record roundtrip 검증

가장 작은 `stage40D.rel`의 string capacity에도 들어가도록 suffix-sharing packing을 사용했습니다.

이 수정으로 스테이지 인트로, 보스 전후 이벤트, scene-instruction cutscene 전체에 Corrected master가 실제 런타임 적용되었습니다.

전수 감사에서 이 경로의 영향을 받는 교정 record는 **157개**로 정리됐습니다.

---

## 27. 인간 QA에서 확인된 대표 scene-instruction 수정

### Team Sonic

```text
우리들의 팀웍을 보여주자！
→ 우리들의 팀워크를 보여주자！

테일스！ 시간은！？
→ 테일즈！ 시간은！？

쉐도우 일행도...
→ 섀도우 일행도...

틀림없을거야
→ 틀림없을 거야
```

### Team Dark

```text
이 앞은 에그맨의 지하요새다
→ 이 앞은 에그맨의 지상요새다

쉐도우
→ 섀도우
```

### Team Rose

```text
개굴이
→ 개구리 군

좋지않네요
→ 좋지 않네요

닥터 에그맨
→ Dr. 에그맨
```

### Team Chaotix

```text
월세를 위해서！
→ 밀린 월세 때문에！

덕분에 꼴 사납게 됐다구
→ 덕분에 꼴사납게 됐다고

챠미
→ 차미
```

### Boss / Final Story

```text
타겟，닥터 에그맨
→ 타깃，Dr. 에그맨

테일스
→ 테일즈

쉐도우
→ 섀도우
```

그리고 앞서 언급한 `event0316`의 `악즉멸` 역시 Boss 7에서 실제 실행됨을 확인해 최종 적용했습니다.

---

# Part VI. CG Theater 및 마지막 polish

## 28. CG Theater의 일본어 로고 교체

한국어 CG 극장은 기능적으로 정상 복원됐지만 우측 작품 로고가 여전히:

```text
ソニックヒーローズ
```

로 표시되었습니다.

최종 polish 단계에서 새 이미지를 그리지 않고 게임의 **공식 영문 `SONIC HEROES` 로고 자산**을 찾아 교체했습니다.

최종 한국어 CG 극장에서는 영문 공식 로고가 표시됩니다.

---

# Part VII. 실제 플레이 검증

## 29. 인간 런타임 QA

정적 분석만으로 끝내지 않고 Dolphin 디버그/이벤트 기능을 이용해 실제 화면을 반복 검증했습니다.

최종 QA 범위:

- Team Sonic 일반 스테이지 전부 진입
- Team Dark 일반 스테이지 전부 진입
- Team Rose 일반 스테이지 전부 진입
- Team Chaotix 일반 스테이지 전부 진입
- 각 스테이지 인트로 이벤트 확인
- 주요 gameplay hint 확인
- 모든 싱글플레이 보스 콘텐츠 확인
- Boss별 팀 이벤트 확인
- Final Story 확인
- CG Theater / CG 자막 확인
- Mission Objective 화면 확인
- 직접 수정한 glyph의 런타임 출력 확인
- `Dr. 에그맨`을 CG / Boss / Gameplay 세 경로에서 각각 확인
- `event0217`의 `개구리 군！` 확인
- `event0316`의 실제 Boss runtime 도달 확인
- `섀`, `깃`, `쌰` gameplay 렌더링 확인

특히 이 과정에서 **정적 분석이 “unused”라고 판단했던 event0316이 실제 게임에 사용됨을 인간 플레이테스트가 반증**했고, Test4의 Stage REL 누락 구조도 실제 플레이를 통해 발견했습니다.

즉 이 프로젝트의 최종 품질은 단순 binary validation뿐 아니라 실제 런타임 QA 결과를 함께 기준으로 삼았습니다.

---

# Part VIII. 최종 배포

## 30. xdelta 2종 배포

전체 ISO는 배포하지 않습니다.

사용자는 자신이 보유한 **Sonic Heroes 일본판 GameCube 원본 ISO**에 xdelta 패치를 적용합니다.

배포 파일:

```text
Sonic_Heroes_GC_Korean_Restoration_Legacy_v1.0_from_G9SJ8P.xdelta
Sonic_Heroes_GC_KR_Corrected_FullGame_FINAL_from_G9SJ8P.xdelta
```

두 패치는 서로 이어서 적용하는 방식이 아니라, **동일한 깨끗한 일본판 ISO를 각각 base로 직접 적용**하는 독립 패치입니다.

### 요구 원본

```text
Sonic Heroes (Japan) (En,Ja,Fr,De,Es,It).iso
SHA-256:
46585AF9446CF101021C7AC0BCCD1E7B9A7CF518D86904B1459E198DEDC93A3F
Size:
1,459,978,240 bytes
```

### Legacy v1.0

복원 ISO SHA-256:

```text
BD9391146CF84883E603F089A228B591E27B87E52783220725AD896534369AE5
```

Legacy xdelta SHA-256:

```text
DD801EFCEF67D7FB747524DAF17F6E7A8235B7010EE6109094AABF29121E60DC
```

Corrected 최종 배포본은 마지막 CG Theater 로고 polish까지 반영된 별도 FINAL build이므로, **최종 Corrected ISO/xdelta checksum은 Release의 `CHECKSUMS.txt`를 기준으로 합니다.**

---

# 31. 두 버전의 차이 요약

| 항목 | Korean Restoration / Legacy | Korean Corrected |
|---|---|---|
| 숨겨진 한국어 슬롯 복원 | O | O |
| 7-language Options | O | O |
| Save/Load Korean 6 | O | O |
| 한국어 메뉴/시스템/대사 | O | O |
| 2004 번역 원형 보존 | **최우선** | 최대한 보존 |
| 현대 공식 고유명사 | 원본 유지 | **교정** |
| 맞춤법/띄어쓰기 수정 | X | **O** |
| 명백한 오역 수정 | X | **O** |
| 미션 목표 문구 수정 | X | **O** |
| 신규/보강 glyph | 복원 Core 범위 | **필요 항목 적용** |
| Stage REL 전체 교정 | 원형 유지 | **O** |
| CG Theater 영문 로고 | Legacy 정책에 따름 | **O** |

---

# 32. 프로젝트의 보존 원칙

이 프로젝트는 단순히 “한국어가 나오게 하는 패치”에서 끝내지 않았습니다.

최종 목표는:

> **실제로 GameCube 한국어판이 발매되었다면 이 정도로 자연스럽게 동작했을 것 같은 상태**

에 최대한 가까워지는 것이었습니다.

동시에 Corrected판에서도 “2026년에 새로 번역한 게임”처럼 만들지 않기 위해 2004년 YBM 번역 특유의 문체와 분위기를 가능한 한 유지했습니다.

새 글자를 만들거나 문장을 바꾸는 것도 무조건 하지 않았습니다.

- `섀`, `깃`처럼 핵심 공식 명칭을 위해 필요한 경우에는 적극 수정
- 미션 조건이 실제 게임과 다른 경우에는 그래픽까지 수정
- 의미가 반대로 된 번역은 수정
- 단순 취향 차이라면 기존 YBM 표현 유지
- 없는 glyph 하나 때문에 개선 이득이 작다면 원본 문장을 복원

이라는 식으로 **보존성과 실제 플레이 품질 사이의 경계**를 정했습니다.

---

# 33. 주요 기술적 발견

프로젝트를 진행하며 확인한 핵심 사항:

- GameCube 일본판에는 실제 `6 = Korean` language path가 남아 있음
- GameConfig와 save/load가 language 6을 정상 보존
- Korean UTX / dialogue / hints / menus / pause / mission asset이 대량으로 존재
- Save/System은 별도 global NECFONT를 사용
- PS2 Korean NECFONT는 GC의 missing Korean glyph donor로 활용 가능
- Gameplay hint는 27개 binary resource로 분리
- Mission Objective는 122개 pre-rendered Korean BMP
- Event subtitle system은 `movieD.rel`뿐 아니라 15개의 stage REL에 중복 존재
- event reachability를 정적 call scan만으로 판단하면 놓칠 수 있음
- MET/TXT/font atlas의 byte-level 형식 보존이 자막 트랙 안정성에 중요
- PNG scanline filter 처리를 생략하면 font atlas가 조용히 깨질 수 있음
- 실제 플레이테스트는 정적 검증만으로 발견하기 어려운 integration bug를 찾아내는 데 필수적이었음

---

# 34. 주요 개발/감사 문서

저장소의 상세 기술 기록은 다음 보고서에서 확인할 수 있습니다.

### Restoration Core

- `SONIC_HEROES_GC_KOREAN_RESTORATION_HISTORY.md`
- `reports/KOREAN_RESTORATION_LEGACY_V1_FINAL_MANIFEST.md`
- `README_LEGACY_v1.0.md`

### Localization / Corrected

- `reports/PRESERVATION_ORTHOGRAPHY_FINAL_AUDIT.md`
- `reports/FINAL_FULL_GAME_KOREAN_QA_V2.md`
- `reports/FULL_GAME_TEST3_EDITORIAL_FINALIZATION.md`
- `reports/STAGE_INTRO_MISSION_OBJECTIVES_DISCOVERY.md`
- `reports/TEST5_SCENE_INSTRUCTION_INTEGRATION_AUDIT.md`
- `reports/FULL_GAME_TEST5_RC_FINALIZATION.md`
- `reports/FINAL_DR_TYPOGRAPHY_MICROFIX.md`
- `reports/FINAL_EVENT0402_REOT_GLYPH_FIX.md`

이 문서는 위 개발 기록과 실제 인간 플레이테스트에서 최종 확정된 결정을 한곳에 요약한 **배포용 종합 변경 기록**입니다.

---

# 35. Disclaimer

This is an unofficial fan restoration project.

- No complete game ISO is distributed.
- Users must provide their own legally obtained, hash-matching source image.
- `Sonic Heroes`, Sonic the Hedgehog, SEGA, Nintendo, and all related trademarks and copyrighted assets belong to their respective owners.
- This project is not affiliated with or endorsed by SEGA or Nintendo.
