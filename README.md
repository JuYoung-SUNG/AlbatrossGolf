# Albatross Golf

파3 · 야외 인도어 골프 연습장의 **요금 · 좌타석 · 드라이버 사용 여부 · 주차** 정보를 정리해 보여주는 사이트입니다.

Blazor WebAssembly로 만든 정적 사이트이며, Cloudflare Pages로 배포됩니다.

## 콘텐츠 파이프라인

연습장 정보는 아래 흐름으로 채웁니다.

```
[1] 원천 수집        홈페이지 · 블로그 후기에서 원문/URL 확보
      ↓
[2] LLM 정제         PROMPT-연습장정제.md 의 프롬프트로 사실 정보만 JSON 추출
      ↓
[3] 검증             요금·수치가 원문과 맞는지, 원문 복사가 없는지 확인
      ↓
[4] 포스팅           수집기에서 --import-golf 로 DB 저장 후
                     wwwroot/data/golf-ranges.json 생성 → 커밋/푸시하면 자동 배포
```

정제 프롬프트와 검증 체크리스트는 [PROMPT-연습장정제.md](PROMPT-연습장정제.md) 참고.

## 구조

```
Pages/
  Index.razor         연습장 목록 (지역·유형 필터)
  RangeDetail.razor   연습장 상세 (요금/좌타석/드라이버/주차, 장점·주의점, 출처)
Shared/
  NavMenu.razor       상단 네비게이션
  AdSlot.razor        애드센스 광고 자리 (ID 미설정 시 렌더링하지 않음)
Models/
  GolfModels.cs       연습장 데이터 모델
wwwroot/
  data/golf-ranges.json   사이트가 읽는 연습장 데이터
```

## 로컬 실행

```bash
dotnet run
```

## 배포

`main` 브랜치에 푸시하면 GitHub Actions가 Cloudflare Pages로 배포합니다.
아래 저장소 시크릿이 필요합니다.

| 시크릿 | 설명 |
|---|---|
| `CLOUDFLARE_API_TOKEN` | Cloudflare API 토큰 (Pages 편집 권한) |
| `CLOUDFLARE_ACCOUNT_ID` | Cloudflare 계정 ID |
| `CLOUDFLARE_PROJECT_NAME` | Pages 프로젝트 이름 (예: albatross-golf) |

## 애드센스

승인 후 아래 두 곳을 채우면 광고가 노출됩니다.

- `Shared/AdSlot.razor` 의 `ClientId`, `SlotId`
- `wwwroot/index.html` 의 애드센스 스크립트 주석 해제

미설정 상태에서는 광고 영역이 아예 렌더링되지 않습니다.
