# faqpage.json — 사용 캐비앗 (필독)

> `faqpage.json`(FAQPage JSON-LD 템플릿) 옆에 두는 주의사항. JSON 내부에는 주석을 넣을 수 없어 이 파일로 전달한다.

## Google 리치결과는 폐지됨 (2026-05-07)

- **FAQPage 리치결과 = Google 검색에서 미표시(2026-05-07), 관련 문서 제거(2026-06-15).** 마크업 API는 ~2026-08까지 잔존하나 **리치결과(SERP 아코디언) 이득은 0**이다.
- 따라서 이 템플릿의 목적은 **Google 리치결과 노출이 아니다.** 다음 목적으로만 사용한다:
  1. **Naver** — FAQPage를 여전히 수집·활용한다(생존 스키마).
  2. **AI 답변엔진(AEO/GEO)** — ChatGPT/Perplexity/Gemini 등이 Q&A 쌍을 파싱한다.
  3. **Google 페이지 이해** — 리치결과는 없어도 Googlebot이 본문 Q&A를 파싱해 페이지 주제 이해에 활용한다.
- **하지 말 것:** "FAQ 스키마 넣으면 Google에 FAQ가 뜬다"는 전제로 페이지를 설계하지 말 것. 리치결과 노출을 KPI로 잡지 말 것.
- **넣어도 무방:** 위 3목적(특히 Naver·AI) 대비면 유지 가치가 있다. 제거는 불필요(패널티 없음). 단, on-page에 실제 Q&A 콘텐츠가 있을 때만 마크업할 것(마크업-only 금지).

## 근거 문서 (SSOT)

- 엔진별 생사표: `docs/MULTI_ENGINE_MASTER_2026.md` §4 · `docs/SCHEMA_JSONLD_CATALOG.md`
- 정정 요약(FAQPage/HowTo 리치결과 폐지): `docs/CONTENT_AUTORULES.md`
- 발행 전 게이트: `docs/GATE_CARD.md` `[AUTO]`

_last_verified: 2026-07 · reverify_by: 2026-Q4_
