---
name: 3-layer Agent 파이프라인 (Worker → Audit → Me)
description: 대량/복잡 작업 시 검증된 실행 구조. Worker 병렬 실행 → 독립 Audit Agent → 내가 최종 빌드/커밋/푸시. www.korvia.com SEO 라운드에서 2회 연속 0 blocker 달성.
type: feedback
originSessionId: 10654995-dff2-4b5c-a086-7c644c9a3bb1
---
> **SSOT**: 3-레이어 파이프라인(Worker→Audit→최종) 패턴의 원본은 이 문서 하나다. 다른 문서(WEB_PLAYBOOK §0, README 등)에서 이 패턴을 언급할 때는 요약 1줄+이 파일 링크만 쓰고 본문을 복제하지 않는다.

Henry가 선호하는 작업 패턴:
```
Worker Agent A + B + C (병렬 실행, 각자 독립 파일 셋)
          ↓
   Audit Agent (독립, 감사만, fix 금지)
          ↓
   내가 최종 (build + git + push)
```

**Why**: Worker는 자기 일 평가 시 과신. 독립 Audit이 자주 "실행 Agent가 한 것 ≠ 실제로 된 것" 불일치 포착.

**How to apply**:
- 파일 개수 10+ 또는 서로 다른 도메인 작업일 때 활용
- Worker 프롬프트에 "do NOT commit, just edit + report" 명시
- Audit 프롬프트에 "Trust but verify — 실행 주장 검증, 0 fix 권한"
- 감사 Agent는 별도 Agent 타입 사용 가능 (general-purpose는 괜찮음)
- 실행 Worker는 도메인 특화 선택 (예: CLS는 vercel:performance-optimizer)
- Audit 완료 후 내가 `git diff --cached` + 논리적 2~3 커밋으로 분리

**실적 (2026-04-17~18)**:
- Round 1 (SEO metadata): 26 파일, 0 blocker
- Round 2 (OG + HowTo): 26 파일, 0 blocker  
- Round 3 (CLS + GTM + Security): 11 파일, 0 blocker
- Round 4 (Desktop CLS + perf): 16 파일, 0 blocker

**병렬 세션 주의**: 다른 Claude 세션이 동시에 같은 repo 작업하면 내 Worker 파일에 섞인 변경사항이 포함됨. Git diff로 스코프 확인 후 선택적 `git add` 권장.
