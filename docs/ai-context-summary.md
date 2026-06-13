# AI Context Summary - IELTS AI Learning Platform

Muc dich: file ngan cho AI/agent doc dau tien o cac phien lam viec sau de tiet kiem token. Neu can chi tiet, xem cac file duoc link o cuoi.

## 1. Project summary

Xay dung website hoc tieng Anh va luyen thi IELTS ca nhan hoa theo trinh do muc tieu.

He thong gom:

- Luyen 4 ky nang IELTS: Reading, Listening, Writing, Speaking.
- Hoc vocabulary bang flashcard.
- Hoc grammar va lam exercises.
- AI sinh cau hoi/dap an/giai thich tu source data trong DB.
- AI cham Writing/Speaking theo IELTS rubric.
- Dashboard va recommendation dua tren lich su hoc.
- Admin quan ly source data va review noi dung AI tao.

## 2. Architecture decisions

Kien truc da chot:

```text
React Frontend
-> Spring Boot Backend modular monolith
-> PostgreSQL + File Storage
-> Python FastAPI AI Service
-> LLM API/local model + Speech-to-text
```

Khong dung full microservice cho MVP.

Ly do:

- Do an can tap trung vao san pham, AI pipeline va ca nhan hoa.
- Full microservice tang do phuc tap khong can thiet.
- AI tach rieng bang FastAPI vi Python phu hop AI/NLP/audio.

Spring Boot:

- So huu database.
- Xu ly auth, user, admin, business logic, scoring, dashboard.
- Goi FastAPI khi can AI.

FastAPI:

- Khong ghi truc tiep PostgreSQL trong MVP.
- Xu ly prompt, provider adapter, Pydantic schema validation, STT, cache.

## 3. Tech stack

Frontend:

- React + TypeScript.
- Tailwind CSS + shadcn/ui.
- React Hook Form + Zod.
- TanStack Query.
- Recharts.

Backend:

- Spring Boot 3.x.
- Spring Security + JWT.
- Spring Data JPA/Hibernate.
- Flyway.
- OpenAPI/Swagger.

Database:

- PostgreSQL.
- UUID primary key.
- JSONB for flexible fields.

AI:

- Python FastAPI.
- Pydantic.
- httpx.
- LLM provider adapter.
- OpenAI/Gemini/Claude API optional.
- Ollama/local model optional.
- Whisper/faster-whisper for STT.
- Mock provider for dev/test.

Testing/deploy:

- JUnit, Mockito, Testcontainers.
- Playwright.
- Docker Compose.

## 4. AI strategy

Khong dinh vi do an la "goi AI API". Dinh vi la:

> AI pipeline + rubric-based grading + schema validation + admin review + rule-based analytics + personalized recommendation.

Hybrid approach:

```text
Reading/Listening/Vocabulary/Grammar:
  AI generate before use -> save DB -> admin review -> learner uses DB -> backend rule-based scoring

Writing/Speaking:
  learner submits answer -> AI rubric grading -> validate JSON -> backend score validation -> cache -> save DB

Recommendation:
  SQL + rule-based analytics core -> optional AI for natural language study plan
```

Canonical pipeline file:

- `docs/pipeline-design.md`
  - Content generation pipeline.
  - Learning/scoring pipeline.
  - Rubric grading pipeline.
  - STT pipeline.
  - Recommendation pipeline.

## 5. Model/API decisions

Use LLM API for high quality tasks:

- Reading/Listening question generation.
- Writing grading.
- Speaking grading.
- Feedback generation.

Use local model optionally:

- Ollama + Qwen/Llama/Mistral/Gemma.
- Good for vocabulary quiz, grammar exercise, draft question generation, offline demo.

Use Whisper/faster-whisper:

- Speech-to-text for Speaking audio.

Use rule-based logic:

- Reading/Listening scoring.
- Vocabulary/Grammar scoring.
- Dashboard analytics.
- Recommendation selection.

Do not train/fine-tune model in MVP unless dataset/GPU/evaluation are ready.

Performance-first rule:

```text
1. Rule-based code if logic/answer is deterministic.
2. SQL aggregation if task is analytics.
3. Local library if task is NLP/audio preprocessing.
4. Local model if task is simple generation/offline demo.
5. LLM API only for high-quality language reasoning/grading.
```

Per-pipeline optimized choices:

- Reading/Listening generation: standard LLM API for quality; local model optional for draft; batch generate; save DB.
- Reading/Listening scoring: Spring Boot rule-based only.
- Vocabulary/Grammar: template/rule-based first; local model optional; avoid API by default.
- Writing grading: strong LLM API, single-pass criterion scoring for MVP; LanguageTool/textstat optional helpers.
- Speaking grading: faster-whisper/STT for transcript, pydub/librosa for metrics, strong LLM API for rubric grading.
- Recommendation: SQL + rule engine; AI optional only to write natural language study plan.

## 6. Database decisions

Core groups:

1. Source data:
   - `source_materials`
   - `reading_passages`
   - `listening_materials`
   - `writing_prompts`
   - `speaking_prompts`
   - `vocabulary_items`
   - `grammar_topics`
   - `rubrics`

2. AI-generated data:
   - `ai_generation_requests`
   - `generated_exercises`
   - `generated_questions`
   - `ai_grading_results`

3. User learning data:
   - `practice_attempts`
   - `user_answers`
   - `writing_submissions`
   - `speaking_sessions`
   - `speaking_turns`

4. Personalization:
   - `user_vocabulary_progress`
   - `user_grammar_progress`
   - `learning_recommendations`

Important relationship decision:

- `ai_grading_results` must not use polymorphic `submission_id`.
- Use explicit FKs:
  - `writing_submission_id`
  - `speaking_turn_id`

## 7. Rubric grading decisions

Writing Task 1 criteria:

- Task Achievement.
- Coherence and Cohesion.
- Lexical Resource.
- Grammatical Range and Accuracy.

Writing Task 2 criteria:

- Task Response.
- Coherence and Cohesion.
- Lexical Resource.
- Grammatical Range and Accuracy.

Speaking criteria:

- Fluency and Coherence.
- Lexical Resource.
- Grammatical Range and Accuracy.
- Pronunciation.

Recommended grading flow:

```text
Submission
-> load rubric from DB
-> model evaluates each criterion
-> FastAPI validates JSON schema
-> Spring Boot validates score logic
-> backend computes/checks overall band
-> save ai_grading_results
-> use result for dashboard/recommendation
```

MVP:

- Single model call returns all criterion scores.
- Backend computes/checks overall.

Advanced:

- Multi-pass criterion scoring.
- Optional reviewer model.
- Calibration dataset.

Criterion technology choices:

- Writing Task Response/Achievement: strong LLM API.
- Writing Coherence/Cohesion: strong LLM API + paragraph/linking stats optional.
- Writing Lexical Resource: strong LLM API + spaCy/textstat/topic vocabulary signals optional.
- Writing Grammar Range/Accuracy: LanguageTool precheck + strong LLM API.
- Speaking Fluency/Coherence: faster-whisper segments/audio metrics + strong LLM API.
- Speaking Lexical Resource: strong LLM API + topic vocabulary signals optional.
- Speaking Grammar Range/Accuracy: LanguageTool + strong LLM API.
- Speaking Pronunciation: speech assessment API such as Azure Speech Assessment for real scoring; transcript-only LLM is limited-confidence fallback.

MVP grading:

- Use one strong LLM API call to return all criterion scores.
- Backend computes/checks overall.
- Use local helper libraries only as signals.

Advanced grading:

- Multi-pass criterion-specific LLM calls.
- Speech assessment API for pronunciation.

Revision/regrade strategy:

- Grading is not fully AI API based; it is hybrid: local analysis + LLM judgement + backend validation.
- If content hash is unchanged, reuse cached grading.
- If user only makes tiny grammar/spelling edits, allow local-only quick feedback with LanguageTool/text stats and do not update official estimated band.
- If edit is minor/moderate and affected criteria are clear, partial regrade only affected criteria.
- If edit is major, structure changes, task changes, or learner requests final/mock-test score, run full regrade.
- MVP recommendation: cache exact repeats, full regrade changed submissions, store grading history; add partial regrade later.

## 8. Personalization decisions

Core recommendation should not depend on AI API.

Use SQL + Spring Boot rules:

- Average score by skill.
- Error rate by question type.
- Grammar error frequency.
- Vocabulary topic weakness.
- Writing/Speaking criterion weakness.
- Speaking duration/fluency weakness.

AI optional:

- Turn structured recommendation into natural language study plan.

## 9. MVP implementation order

1. Monorepo + Docker Compose.
2. Spring Boot backend.
3. React frontend.
4. PostgreSQL + Flyway core schema.
5. Auth JWT + profile.
6. Topics/levels/rubrics/prompt templates seed.
7. Admin source materials.
8. FastAPI Mock AI.
9. Generated exercises/questions.
10. Reading end-to-end.
11. Listening end-to-end.
12. Real AI provider for generation/grading.
13. Writing Task 2 grading.
14. Speaking text grading.
15. Vocabulary flashcard.
16. Grammar lesson/exercise.
17. Dashboard analytics.
18. Rule-based recommendation.
19. Speaking audio + STT if scope allows.
20. Local model provider if scope allows.

## 10. Canonical docs

Read these if more detail is needed:

- `docs/README.md`: documentation index.
- `docs/functional-requirements.md`: functional requirements.
- `docs/use-case-analysis.md`: use cases.
- `docs/database-design.md`: PostgreSQL design.
- `docs/dbdiagram-erd.dbml`: dbdiagram.io ERD.
- `docs/springboot-entities.md`: Spring Boot entity mapping.
- `docs/tech-stack-and-roadmap.md`: tech stack and roadmap.
- `docs/technology-ai-summary.md`: technology/model/API summary.
- `docs/pipeline-design.md`: generation, scoring, grading and recommendation pipelines.
- `docs/ai-service-design.md`: AI service design.
- `docs/rubric-grading-design.md`: rubric grading engine.

## 11. Next recommended artifact

If continuing analysis/design, next most useful documents are:

1. Activity diagrams / business flows for key modules.
2. Sequence diagrams for Reading generation, Writing grading, Speaking grading.
3. REST API specification.
4. UI wireframe document.
5. Flyway migration script draft.
