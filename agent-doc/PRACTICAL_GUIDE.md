# 실전 Agent 활용 가이드 (개정판)

## 문서 목적
4개 Agent(Claude, Gemini, Copilot, Perplexity)의 심도 깊은 분석 결과를 반영한 **실현 가능한 실무 가이드**입니다.

---

## 핵심 전제 (반드시 이해할 것)

### 1. Agent 간 직접 협업 불가능
- ❌ Agent끼리 자동으로 대화하지 못함
- ❌ 이전 대화 내용을 기억하지 못함
- ✅ **모든 협업은 사용자가 중개** (복사-붙여넣기)

### 2. 기본 전략: 순차 활용
```
일반적인 경우: 한 Agent에게 끝까지 맡기기
↓
막힐 때만: 다른 Agent에게 선택적 요청 (1회성)
↓
다시 원래 Agent로 복귀
```

### 3. 오버헤드 인정
- **사용자 중개 시간: 25%** (허용 가능)
- **품질 향상**으로 충분히 상쇄됨

### 4. 실현 가능성: 70%
- Claude: 20% (과도하게 비관적)
- Gemini: 70% (균형 잡힌 평가)
- Perplexity: 60% (타협안)
- Copilot: 75% (실전 검증)
- **최종 합의: 70%**

---

## Phase별 실전 가이드

### Phase 0: 빠른 검증 (1-2시간)

**목표**: PoC로 기술적 실현 가능성 확인

**실행 순서**:
1. **Copilot에게**: "이 기능 30분 안에 PoC 만들어줘"
2. 결과 확인 → 작동하면 Phase 1로, 실패하면 계획 수정
3. **(선택) Gemini에게**: PoC 복사 → "이 기술 스택 적합한가요?" 질문
4. **(선택) Claude에게**: "요구사항 정리해줘"

**소요**: 실제 1-2시간 (Copilot 단독 1시간, 다른 Agent 선택 시 +30분)

---

### Phase 1: 계획 수립 (반나절~1일)

**목표**: 실행 가능한 계획서 작성

**실행 순서**:

**방법 A: Claude 단독** (1인 개발자)
1. Claude에게: "이 요구사항으로 계획서 작성해줘"
2. 결과물 받고 → 명확성만 확인
3. 바로 Phase 2로

**방법 B: 검증 포함** (2-5인 팀)
1. Claude에게: 계획서 작성 (30분)
2. **사용자가 계획서를 Gemini에 복사** (5분)
3. Gemini에게: "이 계획의 기술적 문제점?" (20분)
4. **사용자가 Gemini 피드백을 Claude에 복사** (5분)
5. Claude에게: "피드백 반영해 수정" (20분)
6. **사용자가 수정안을 Copilot에 복사** (5분)
7. Copilot에게: "30분 안에 PoC로 검증" (15분)

**총 소요**: 100분 (사용자 중개 15분 포함)

---

### Phase 2: 구현 (1-3주)

**목표**: 실제 코드 작성

**기본 전략: Copilot 중심**

```
Week 1-2: Copilot 단독 구현 (80%)
  ↓
  막히는 부분 발견
  ↓
  Gemini에게 1회 질문 (설계/알고리즘)
  ↓
  Gemini 답변을 Copilot에 전달
  ↓
다시 Copilot 구현 (20%)
```

**마일스톤 관리** (3-5일 단위):

**마일스톤 1 시작**:
1. **project_status.md 작성**:
```markdown
# 현재 상태
Phase: 2-1 (UserService 구현)
목표: CRUD API 완성
마지막 작업: 계획 완료 (2026-01-08)
다음: Copilot 구현
```

2. **Copilot에게 작업 지시**:
```
- project_status.md 첨부
- "UserService CRUD 구현해줘"
- 막히면 보고
```

3. **막힐 때만 Gemini 호출**:
```
Copilot: "복잡한 동시성 처리 막힘"
↓
사용자: Gemini에게 전달
↓
Gemini: 설계 제공
↓
사용자: Copilot에게 전달
↓
Copilot: 구현 재개
```

**마일스톤 1 종료**:
1. **Copilot이 변경 요약 제공**:
```markdown
## 변경 요약
- 추가 파일: UserService.ts, UserDTO.ts, UserController.ts
- 주요 기능: Create, Read (Update/Delete 미완)
- 테스트: 50% 통과
- Git: 커밋 abc123
```

2. **품질 게이트 체크**:
```
[ ] 모든 테스트 통과?
[ ] get_errors로 오류 확인?
[ ] Git 커밋 완료?
```

3. **project_status.md 업데이트**

**마일스톤 2 시작** → 반복

---

### Phase 3: 정리 (반나절~1일)

**목표**: 문서화 + DevOps

**실행 순서**:

1. **Copilot (DevOps)**:
```
- CI/CD 스크립트 생성
- Docker 설정
- 최종 테스트
```

2. **Claude (문서화)**:
```
- Copilot 변경 요약 받기
- API 명세서 작성
- 사용자 매뉴얼 작성
```

3. **Gemini (최종 검수 - 선택)**:
```
- 보안 리뷰
- 성능 분석
- 기술 부채 목록
```

---

## 팀 규모별 전략

### 1인 개발자
```
Phase 0: Copilot PoC (생략 가능)
Phase 1: Claude 계획 (검증 생략)
Phase 2: Copilot 구현 (막힐 때만 Gemini)
Phase 3: Claude 문서화
```
**오버헤드**: 10%

### 2-5인 팀
```
Phase 0: Copilot → Gemini 검증
Phase 1: Claude → Gemini 검토 → Copilot 검증
Phase 2: Copilot 주도 + Gemini 선택적 호출
Phase 3: Copilot DevOps + Claude 문서 + Gemini 검수
```
**오버헤드**: 25%  
**효율 증가**: +40-50%

### 10인 이상 팀
```
팀 1: Claude (계획/문서)
팀 2: Copilot (Module A)
팀 3: Copilot (Module B)
팀 4: Gemini (리뷰/분석)
```
**오버헤드**: 30%  
**효율 증가**: +60-70%

---

## 실무 체크리스트

### Agent 전환 시

**Claude → Gemini**:
```
[ ] Claude 결과물 전체 복사
[ ] "이 계획의 기술적 타당성 검토" 명시
[ ] Gemini 피드백 중 변경 필요 부분만 추출
[ ] Claude에게 재요청
```

**Gemini → Copilot**:
```
[ ] Gemini 설계 + 의사코드 복사
[ ] "이 설계를 파일로 구현" 명시
[ ] 파일 경로 명확히 지정
[ ] 구현 후 get_errors로 검증
```

### 컨텍스트 손실 방지

**Sprint 요약 템플릿**:
```markdown
# Sprint N 요약 (Copilot 자동 생성)
- 변경 파일: [git diff --name-only]
- 추가 기능: [3줄 요약]
- 다음 입력: [이 요약 + 새 요구사항]
```

**Git 커밋 메시지**:
```bash
git commit -m "Add: UserService CRUD
(Claude 테스트 케이스 통과, Gemini 설계 반영)"
```

### 함정 회피

❌ **하지 말 것**:
- 100개 파일 한번에 수정 요청
- "코드 개선해줘" (추상적)
- "에러 났어요" (컨텍스트 없음)

✅ **할 것**:
- 10개씩 나눠 요청 + 중간 검증
- "UserService.ts 에러 핸들링 추가 (try-catch)"
- "Phase 2-3, UserService 테스트 실패: [로그 전문]"

---

## 상태 관리: project_status.md

**목적**: 컨텍스트 손실 최소화

**템플릿**:
```markdown
# 프로젝트 상태

**현재 Phase**: 2-3 (UserService 구현)
**목표**: REST API CRUD 완성
**마지막 작업**: Gemini 설계 완료 (2026-01-08 14:00)
**다음 할 일**: Copilot 구현 및 테스트

## 핵심 산출물
- [Claude] 계획서: docs/plan.md
- [Gemini] 설계: docs/design.md
- [Copilot] 구현: src/services/UserService.ts (50%)

## 주요 변경 (최근 3일)
- Day 1: 계획 수립 (Claude)
- Day 2: 아키텍처 설계 (Gemini)
- Day 3: CRUD 중 Create/Read 완료 (Copilot)

## Git 참조
- 마지막 커밋: abc123 "Add: UserService Create/Read"
- 브랜치: feature/user-service
```

**활용**:
1. 각 Agent 작업 종료 시 업데이트
2. 다음 Agent 호출 시 이 파일 함께 전달
3. 컨텍스트 100% 유지

---

## 품질 게이트

**Gemini → Copilot 전환 시**:
```
[ ] 데이터 모델과 API 인터페이스 명확히 정의
[ ] 테스트 전략 및 커버리지 목표 명시
[ ] 복잡한 로직의 의사코드 제공
[ ] 성능/보안 요구사항 문서화
```

**Copilot → Claude 전환 시**:
```
[ ] 모든 테스트 통과 (get_errors)
[ ] Git 커밋 히스토리 정리
[ ] API 변경사항 요약 작성
[ ] 주요 버그 픽스 내역 정리
```

---

## 최종 권장사항

### 성공의 핵심
1. ✅ **순차 활용 기본** (한 Agent에게 집중)
2. ✅ **선택적 협업** (막힐 때만)
3. ✅ **project_status.md 활용** (컨텍스트 유지)
4. ✅ **품질 게이트 준수** (단계별 검증)

### 실패 방지
1. ❌ **자동 협업 기대하지 말 것**
2. ❌ **과도한 Agent 전환 피할 것**
3. ❌ **컨텍스트 없는 요청 금지**

---

**작성 기준**: 4개 Agent 합의 (Claude, Gemini, Perplexity, Copilot)  
**실현 가능성**: 70%  
**검증 프로젝트**: Agent Workflow 문서 작업 (실전 데이터 기반)  
**개정 일자**: 2026-01-08
