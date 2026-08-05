# Albatross Golf

파3 · 야외 인도어 골프 연습장의 **요금 · 좌타석 · 드라이버 사용 여부 · 주차** 정보를 정리해 보여주는 사이트입니다.

**정적 HTML 사이트**이며 Cloudflare Pages로 배포됩니다.
검색엔진이 첫 HTML에서 본문을 그대로 읽을 수 있도록(색인·속도 목적) 페이지를 미리 생성하는 방식입니다.
빌드 과정이 없고 `public/` 폴더를 그대로 배포합니다.

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
public/                     ← 배포되는 폴더 (수집기가 생성)
  index.html                연습장 목록 (지역·유형 필터)
  range/<slug>/index.html   연습장 상세 (+ 지역업체 구조화 데이터)
  sitemap.xml, robots.txt   검색 색인용
  css/site.css              스타일 (수동 관리)
PROMPT-연습장정제.md          LLM 정제 프롬프트
```

`public/` 안의 HTML은 **직접 수정하지 마세요.** 수집기가 다시 생성하면 덮어씁니다.
내용을 바꾸려면 DB 데이터를 고치고 재생성하면 됩니다.

## 페이지 생성 방법

수집기(별도 저장소 `Albatross`)에서 실행합니다.

```bash
# LLM이 정제한 JSON을 DB에 넣고 → 정적 페이지까지 생성
dotnet run --project Albatross.Collector --configuration Release -- --import-golf "경로\ranges.json"

# 데이터 변경 없이 페이지만 다시 생성
dotnet run --project Albatross.Collector --configuration Release -- --export-golf
```

## 로컬 확인

```bash
cd public
python -m http.server 8080    # 또는 아무 정적 서버
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

승인 후 생성기(`GolfSiteGenerator.cs`)의 페이지 템플릿에 애드센스 스크립트와 광고 단위를 넣으면
모든 페이지에 일괄 적용됩니다. 콘텐츠가 충분히 쌓인 뒤 신청하는 것을 권장합니다.
