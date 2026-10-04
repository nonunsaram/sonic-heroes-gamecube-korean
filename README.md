# 소닉 히어로즈 GC 한국어 패치

제작: **노는사람 (nonunsaram)**

**일본 음성판과 영어 음성판을 모두 제공하는 한국어 복원·번역수정 패치입니다.**

## 다운로드 — v1.1.0 통합 최종 배포

[최신 릴리스](https://github.com/nonunsaram/sonic-heroes-gamecube-korean/releases/tag/v1.1.0)에 아래 **4종**이 함께 있습니다. 원하는 음성과 번역을 골라 `.xdelta` **하나**를 받으세요.

| 음성 | 번역 | 파일명 | 적용할 원본 |
|---|---|---|---|
| 일본어 | 복원판 | `Sonic_Heroes_GC_JP_Korean_Restoration_v1.0.xdelta` | 일본판 **G9SJ8P** |
| 일본어 | 번역수정판 | `Sonic_Heroes_GC_JP_Korean_Correction_v1.0.xdelta` | 일본판 **G9SJ8P** |
| 영어 | 복원판 | `Sonic_Heroes_GC_USA_Korean_Restoration_v1.1.0.xdelta` | 미국판 **G9SE8P, Rev 0** |
| 영어 | 번역수정판 | `Sonic_Heroes_GC_USA_Korean_Correction_v1.1.0.xdelta` | 미국판 **G9SE8P, Rev 0** |

- **복원판**: 원래 한국어 번역과 표현을 유지합니다.
- **번역수정판**: 명칭·오역·글꼴·미션 문구 등을 교정합니다.
- 일본 음성판은 기존 공개 v1.0과 동일합니다. 영어 음성판은 사용자 실행 확인을 마친 최종본이며, 수정판에는 마지막 스토리 자막 복구가 포함됩니다.

## 설치

1. 선택한 음성에 맞는 **깨끗한 일본판 또는 미국판 ISO**를 준비합니다.
2. xdelta 패처에서 원본 ISO와 원하는 패치 하나를 선택합니다.
3. 새 ISO로 저장하고 실행합니다.

**두 패치를 연속 적용하지 마세요.** 다른 지역판이나 이미 패치된 ISO에는 적용할 수 없습니다.

두 원본 모두 크기는 `1,459,978,240 bytes`입니다.

| 원본 | SHA-256 |
|---|---|
| 일본판 `Sonic Heroes (Japan) (En,Ja,Fr,De,Es,It).iso` | `46585AF9446CF101021C7AC0BCCD1E7B9A7CF518D86904B1459E198DEDC93A3F` |
| 미국판 `Sonic Heroes (USA) (En,Ja,Fr,De,Es,It).iso` | `1F90FAD188B0904AC4C5F615F5553B3614694EEE7D22B87C54D988B0928DAC78` |

패치·결과 확인값: [일본판](CHECKSUMS.txt) · [미국판](USA_CHECKSUMS.txt)

영어 음성 복원판 참고: 언어 전환 뒤 한국어 선택 행이 투명해질 수 있으며, 일부 `으쌰！`가 `으！`로 표시되는 기존 제한이 있습니다.

## 추가 자료

[일본판 세이브·Gecko 코드](extras) · [전체 변경 기록](SONIC_HEROES_GC_KOREAN_RESTORATION_FULL_CHANGELOG.md) · [복원 역사](SONIC_HEROES_GC_KOREAN_RESTORATION_HISTORY.md)

세이브·Gecko 코드는 일본판 전용입니다. 비공식 팬 패치이며 게임 ISO는 배포하지 않습니다.
