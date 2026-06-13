# Bo tai lieu chuan - IELTS AI Learning Platform

Tai lieu trong thu muc nay mo ta qua trinh phan tich, thiet ke va dinh huong trien khai cho he thong hoc tieng Anh va luyen thi IELTS bang AI.

## 1. Thu tu doc khuyen nghi

Doc theo thu tu sau de nam he thong tu tong quan den chi tiet:

1. `functional-requirements.md`
   - Dac ta yeu cau chuc nang.
   - Xac dinh scope, actor, module va yeu cau du lieu.

2. `use-case-analysis.md`
   - Phan tich actor va use case.
   - Co use case diagram, flow learner/admin, traceability FR -> UC.

3. `database-design.md`
   - Thiet ke PostgreSQL.
   - Co ERD Mermaid, bang, quan he, index, constraint, seed data.

4. `dbdiagram-erd.dbml`
   - File DBML de import vao dbdiagram.io.
   - Dung de xem database diagram truc quan.

5. `springboot-entities.md`
   - Huong dan map database sang Spring Boot Entity/JPA.
   - Co enum, JSONB mapping, entity examples, repository examples.

6. `tech-stack-and-roadmap.md`
   - Chon tech stack va lo trinh trien khai.
   - Co phase implementation, MVP priority, API outline, testing strategy.

7. `technology-ai-summary.md`
   - Tong hop cong nghe, model, thu vien, API va cach lam tung module.
   - File nay phu hop de doc nhanh truoc khi bat dau implementation.

8. `pipeline-design.md`
   - Mo ta cac pipeline chinh: generate cau hoi, review/publish, learner practice, grading, STT, recommendation.
   - File nay nen doc truoc khi thiet ke API/sequence diagram chi tiet.

9. `ai-service-design.md`
   - Thiet ke Python FastAPI AI Service.
   - Co provider adapter, prompt/schema validation, cache, workflow, cost control.

10. `rubric-grading-design.md`
   - Thiet ke chi tiet grading engine cho Writing/Speaking theo IELTS rubric.
   - Co criterion-level scoring, backend aggregation, validation, calibration.

11. `ai-context-summary.md`
    - Tom tat ngan gon cho AI/agent doc lai o cac phien lam viec sau.
    - Dung de tiet kiem token va tranh doc lai toan bo tai lieu dai.

## 2. File nao dung cho muc dich nao

| Muc dich | File nen doc |
| --- | --- |
| Hieu bai toan va scope | `functional-requirements.md` |
| Hieu actor/use case | `use-case-analysis.md` |
| Thiet ke database | `database-design.md`, `dbdiagram-erd.dbml` |
| Code backend entity | `springboot-entities.md` |
| Chon cong nghe va plan code | `tech-stack-and-roadmap.md`, `technology-ai-summary.md` |
| Hieu pipeline xu ly | `pipeline-design.md` |
| Thiet ke AI service | `ai-service-design.md` |
| Thiet ke cham Writing/Speaking | `rubric-grading-design.md` |
| Context ngan cho AI lan sau | `ai-context-summary.md` |

## 3. Quyet dinh thiet ke da chot

### 3.1. Kien truc

```text
React Frontend
    |
Spring Boot Backend modular monolith
    |
    |-- PostgreSQL
    |-- File Storage
    |
Python FastAPI AI Service
    |
    |-- LLM API provider hoac local model
    |-- Speech-to-text model/API
```

Khong dung full microservice cho MVP. Chi tach AI service rieng vi Python phu hop AI/NLP/audio.

### 3.2. Backend va database

- Spring Boot la backend chinh.
- Spring Boot so huu database.
- FastAPI AI Service khong ghi truc tiep PostgreSQL trong MVP.
- PostgreSQL luu source data, generated content, submissions, grading results, progress va recommendations.

### 3.3. AI strategy

Dung hybrid approach:

- AI sinh truoc Reading/Listening/Vocabulary/Grammar, luu DB, admin duyet.
- Learner lam bai tu DB, backend cham rule-based.
- Writing/Speaking goi AI khi submit, nhung phai co rubric, JSON schema, cache, validation.
- Recommendation core dung SQL + rule-based analytics; AI chi optional de viet study plan tu nhien.

### 3.4. Grading strategy

- Model cham tung criterion.
- Backend validate criterion scores.
- Backend tinh/kiem tra overall band.
- Luu ket qua vao `ai_grading_results`.
- Dung criterion scores va errors cho dashboard/recommendation.

## 4. MVP scope

MVP nen tap trung:

1. Auth/Profile.
2. Admin source content.
3. AI generation foundation.
4. Reading end-to-end.
5. Listening end-to-end.
6. Writing Task 2 grading.
7. Speaking text grading.
8. Vocabulary flashcard.
9. Grammar lesson/exercise.
10. Dashboard co ban.

Lam sau MVP:

- Placement test day du.
- Speaking audio + STT.
- Writing Task 1 chart image.
- Speaking follow-up question.
- Vocabulary quiz nang cao.
- AI grammar exercise generation.
- Recommendation AI text.
- Local model provider.
- Pronunciation scoring nang cao.

## 5. Ghi chu ve tinh nhat quan tai lieu

- Cac file su dung tieng Viet khong dau de giu ASCII va tranh loi encoding.
- `docs/README.md` la muc luc chuan.
- `ai-context-summary.md` la file ngan cho AI/agent, khong thay the tai lieu chi tiet.
- Neu co thay doi quyet dinh kien truc, can cap nhat ca:
  - `docs/README.md`
  - `docs/ai-context-summary.md`
  - Tai lieu chi tiet lien quan.
