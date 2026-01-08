# AI 워크플로우 실제 사용 예시: URL 단축기 API 개발

이 문서는 `README.md`에서 설명하는 AI 워크플로우를 실제 프로젝트에 어떻게 적용하는지 단계별로 보여줍니다.

**프로젝트 목표:** 긴 URL을 입력받아 짧은 URL을 생성하고, 짧은 URL로 접속 시 원래 URL로 리디렉션하는 간단한 API 서버를 개발합니다.

---

### Phase 0: 프로젝트 준비

**1. `project_status.md` 파일 생성**

> `d:\workspace\url-shortener\project_status.md`

```markdown
# Project Status: URL Shortener

_Last Updated: 2026-01-10 09:00 (Operator: John)_

## 1. Current Milestone & Objective
- **Objective:** URL 단축기 핵심 기능(생성, 리디렉션)을 갖춘 API 프로토타입 개발

## 2. Key Artifacts & Location
- (아직 없음)

## 3. Last Action Summary
- (아직 없음)

## 4. Next Action
- **Agent:** Claude
- **Objective:** 프로젝트 기본 계획 및 API 명세 초안 작성
```

---

### Phase 1: 기획 및 설계

**2. [Planner 모드] `Claude`에게 기본 계획 요청**

> **Operator → Claude:**
> "Python FastAPI를 사용하여 URL 단축기 API를 만들려고 해. 필요한 API 엔드포인트와 각 엔드포인트의 기본 기능을 포함한 개발 계획 초안을 작성해줘."

**3. `project_status.md` 업데이트**

> `Claude`가 API (`POST /shorten`, `GET /{short_code}`) 및 기본 데이터 모델(`url_mappings` 테이블) 초안을 제공했습니다.

```markdown
# ... (이전 내용 생략) ...
## 2. Key Artifacts & Location
- **Plan:** `docs/plan_v1.md`
## 3. Last Action Summary
- **Agent:** Claude
- **Action:** 기본 계획 및 API 명세 초안 작성 완료.
## 4. Next Action
- **Agent:** Gemini
- **Objective:** 짧은 URL 해시 생성 알고리즘 설계 및 DB 스키마 확정
```

**4. [Architect 모드] `Gemini`에게 핵심 설계 자문**

> **Operator → Gemini:**
> "첨부한 `project_status.md`를 참고해. 짧은 URL을 생성하기 위한 해시 충돌이 적은 알고리즘을 제안해줘. 그리고 `url_mappings` 테이블의 구체적인 SQL 스키마를 설계해줘."

**5. `project_status.md` 업데이트**

> `Gemini`가 `Base62` 인코딩과 `id`를 조합하는 방식을 제안하고, `long_url`에 `UNIQUE` 제약 조건을 추가한 `CREATE TABLE` 구문을 제공했습니다.

```markdown
# ... (이전 내용 생략) ...
## 4. Next Action
- **Agent:** Copilot
- **Objective:** `POST /shorten` 엔드포인트 기능 구현
## 5. Decisions
- **Hashing:** `id` + `Base62` encoding
- **DB Schema:** `long_url` column에 `UNIQUE` 제약조건 추가
```

---

### Phase 2: 구현 및 전문가 호출

**6. [Implementer 모드] `Copilot`에게 구현 요청**

> **Operator → Copilot:**
> "첨부한 `project_status.md`의 결정사항을 바탕으로, Python FastAPI 코드를 작성해줘. `POST /shorten` 엔드포인트부터 구현할 거야. 데이터베이스는 SQLite를 사용해줘."

**7. 문제 발생 및 전문가 호출**

> `Copilot`이 기본 코드를 생성했지만, Operator는 '만약 두 사용자가 거의 동시에 같은 long_url을 요청하면, 중복된 데이터가 생성될 수 있는 레이스 컨디션'이 발생할 수 있음을 인지했습니다. (Gemini가 제안한 `UNIQUE` 제약조건만으로는 애플리케이션 레벨에서 처리가 애매함)

**8. [Architect 모드] `Gemini`에게 문제 해결 자문**

> **Operator → Gemini:**
> "`Copilot`이 작성한 코드와 `project_status.md`를 참고해. 동일한 `long_url`에 대한 동시 요청이 발생했을 때의 레이스 컨디션을 막기 위한 가장 효율적인 코드 레벨 처리 방법을 제안해줘. DB의 `UNIQUE` 제약조건 에러를 `try-except`로 잡아서 처리하는 게 좋을까?"

**9. `project_status.md` 업데이트**

> `Gemini`는 `try-except` 블록으로 `IntegrityError`를 잡은 뒤, 해당 에러 발생 시 기존에 저장된 `short_code`를 조회하여 반환하는 것이 가장 깔끔하고 효율적인 방법이라고 답변했습니다.

```markdown
# ... (이전 내용 생략) ...
## 5. Decisions
# ... (이전 결정사항) ...
- **Race Condition Handling:** `try-except`로 `IntegrityError`를 처리하고, 에러 발생 시 기존 데이터를 조회하여 반환하는 방식으로 확정.
```

**10. [Implementer 모드] `Copilot`에게 수정 지시**

> **Operator → Copilot:**
> "`project_status.md`에 추가된 'Race Condition Handling' 결정사항을 반영해서, 이전에 작성한 `POST /shorten` 코드를 수정해줘."

---

### Phase 3: 최종화 (반복)

이후 `GET /{short_code}` 기능 구현, 테스트 코드 작성, 최종 문서화 등의 과정도 **`역할 모드 전환 → 주력 AI 선택 → 막히면 전문가 호출 → 결정사항 기록`** 사이클을 반복하여 프로젝트를 완료합니다.
