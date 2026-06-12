# Tong hop cong nghe, model, thu vien, API va cach trien khai

Tai lieu lien quan den cham diem theo IELTS rubric:

```text
docs/rubric-grading-design.md
```

## 1. Kien truc tong the duoc chot

Kien truc nen dung:

```text
React Frontend
    |
    v
Spring Boot Backend modular monolith
    |
    |-- PostgreSQL
    |-- File Storage
    |
    v
Python FastAPI AI Service
    |
    |-- LLM API provider hoac local model
    |-- Speech-to-text model/API
```

Ten kien truc co the ghi trong bao cao:

> Modular monolith ket hop AI service tach biet.

Ly do:

- Spring Boot quan ly nghiep vu chinh va database.
- FastAPI xu ly AI vi Python manh ve AI/NLP/audio.
- Khong dung full microservice de tranh phuc tap qua muc cho do an.
- AI service tach rieng giup co the doi provider, dung mock/local/API linh hoat.

## 2. Bang tong hop cong nghe can dung

| Phan | Cong nghe/API/Thu vien | Vai tro |
| --- | --- | --- |
| Frontend | React + TypeScript | Xay giao dien learner/admin |
| UI | Tailwind CSS, shadcn/ui | Tao UI nhanh, dong nhat |
| Form | React Hook Form, Zod | Form va validation |
| Data fetching | TanStack Query | Goi API, cache client state |
| Chart | Recharts | Dashboard tien do |
| Backend | Spring Boot 3.x | Backend chinh |
| Security | Spring Security + JWT | Dang nhap, phan quyen |
| ORM | Spring Data JPA/Hibernate | Map entity voi PostgreSQL |
| Migration | Flyway | Quan ly schema database |
| API docs | OpenAPI/Swagger | Tai lieu/test API |
| Database | PostgreSQL | Luu du lieu he thong |
| Flexible data | JSONB | Options, answers, AI feedback |
| AI service | Python FastAPI | Xu ly AI orchestration |
| AI schema | Pydantic | Validate input/output AI |
| AI provider | OpenAI/Gemini/Claude API | LLM cho generation/grading |
| Local LLM option | Ollama/vLLM/llama.cpp | Chay model da train san local |
| STT | Whisper/faster-whisper hoac Whisper API | Chuyen audio thanh text |
| Audio processing | pydub, librosa | Xu ly audio co ban |
| NLP helper | spaCy, textstat, LanguageTool | Ho tro grammar/readability/keyword |
| Storage | Local storage, S3-compatible, Cloudinary | Luu audio/image |
| Backend tests | JUnit, Mockito, Testcontainers | Test service/repository/API |
| Frontend tests | Playwright | E2E flow |
| Deployment | Docker Compose | Chay frontend/backend/db/ai service |

## 3. Chon model/API/thu vien AI nhu the nao

## 3.1. Khong chi co mot cach dung AI

He thong nen ho tro 4 che do:

```text
Mock provider:
  Dung cho dev/test, khong ton chi phi.

API provider:
  Dung LLM API cho tac vu can chat luong cao.

Local model provider:
  Dung model da train san chay local de giam chi phi/demo offline.

Rule-based:
  Dung code backend cho tac vu co dap an co dinh.
```

## 3.2. Model/API khuyen nghi theo muc do

### MVP thuc te

- LLM API: OpenAI/Gemini/Claude cho:
  - Sinh cau hoi Reading/Listening.
  - Cham Writing.
  - Cham Speaking.
  - Tao feedback.
- Mock AI: dung khi test flow.
- Whisper API hoac local Whisper: speech-to-text neu lam audio.

### Neu muon do an noi bat hon

Them local model mode:

- Ollama + Qwen/Llama/Mistral/Gemma.
- FastAPI goi Ollama qua HTTP.
- Dung local model cho task don gian:
  - Vocabulary quiz.
  - Grammar exercise.
  - Draft Reading questions.
- Dung API model cho task kho:
  - Writing grading.
  - Speaking grading.

### Khong khuyen nghi cho MVP

- Tu train model tu dau.
- Fine-tune model neu chua co dataset IELTS du lon.

Ly do:

- Ton dataset.
- Can GPU.
- Kho evaluate.
- De lam lech trong tam khoi san pham web.

## 4. Cach lam tung phan

## 4.1. Frontend React

### Cong nghe

- React.
- TypeScript.
- Tailwind CSS.
- shadcn/ui.
- TanStack Query.
- React Hook Form.
- Zod.
- Recharts.

### Cach lam

Frontend chia thanh hai khu:

```text
Learner app:
  dashboard
  reading
  listening
  writing
  speaking
  vocabulary
  grammar
  profile

Admin app:
  source materials
  generated exercises
  review content
  vocabulary management
  grammar management
  rubrics
  prompt templates
```

Frontend chi goi Spring Boot API, khong goi AI API truc tiep.

Vi du flow Writing:

```text
Learner viet bai tren React
-> POST /api/writing/submissions
-> Spring Boot goi FastAPI AI Service
-> React hien feedback tra ve tu Spring Boot
```

## 4.2. Backend Spring Boot

### Cong nghe

- Spring Boot 3.x.
- Spring Web.
- Spring Data JPA.
- Spring Security.
- JWT.
- Bean Validation.
- Flyway.
- OpenAPI/Swagger.

### Cach lam

Spring Boot la trung tam nghiep vu:

- Auth va role.
- CRUD source data.
- CRUD generated exercise/question.
- Cham bai co dap an co dinh.
- Luu submissions.
- Goi FastAPI AI Service.
- Luu response AI vao PostgreSQL.
- Tinh dashboard va recommendation.

Module nen co:

```text
auth
user
profile
source-material
generated-exercise
practice
reading
listening
writing
speaking
vocabulary
grammar
recommendation
admin
ai-client
file-storage
```

Spring Boot khong nen de FastAPI ghi truc tiep database chinh.

## 4.3. Database PostgreSQL

### Cong nghe

- PostgreSQL.
- UUID.
- JSONB.
- Flyway.

### Cach lam

Database chia 4 nhom:

```text
1. Source data:
   passages, transcripts, prompts, vocabulary, grammar, rubric

2. AI-generated data:
   generated_exercises, generated_questions, ai_grading_results

3. User learning data:
   attempts, answers, submissions, speaking turns

4. Personalization data:
   vocabulary progress, grammar progress, recommendations
```

Nguyen tac:

- Reading/Listening/Vocabulary/Grammar co dap an co dinh thi luu question/answer/explanation vao DB.
- Writing/Speaking luu submission va AI grading result.
- Dung JSONB cho options, correct answer, criterion scores, grammar errors.
- Dung status draft/reviewed/published/rejected cho noi dung AI tao.

## 4.4. Python FastAPI AI Service

### Cong nghe

- FastAPI.
- Pydantic.
- httpx.
- Provider SDK:
  - openai hoac google-genai hoac anthropic.
- Optional:
  - ollama client/local HTTP.
  - faster-whisper.
  - pydub/librosa.
  - spaCy/textstat/LanguageTool.

### Cach lam

FastAPI co cac endpoint:

```text
POST /ai/reading/generate-questions
POST /ai/listening/generate-questions
POST /ai/writing/grade
POST /ai/speaking/grade
POST /ai/speaking/follow-up
POST /ai/vocabulary/generate-quiz
POST /ai/grammar/generate-exercise
POST /ai/speech/transcribe
```

Noi bo AI service:

```text
Router
-> Service
-> Prompt Manager
-> Provider Adapter
-> Schema Validator
-> Cache
-> Response
```

Khong tra output text tu do. Moi output phai co schema Pydantic.

## 5. Cach lam AI cho tung tinh nang

## 5.1. Reading

### Cong nghe/model

- LLM API hoac local model.
- Pydantic schema validation.
- PostgreSQL luu generated questions.

### Cach lam

```text
Admin nhap Reading passage
-> Spring Boot luu source data
-> Admin bam Generate
-> Spring Boot goi FastAPI
-> AI sinh cau hoi/dap an/giai thich/evidence
-> FastAPI validate JSON
-> Spring Boot luu generated_questions
-> Admin review/publish
-> Learner lam bai tu DB
-> Spring Boot cham rule-based
```

### Diem dac biet

Khong goi AI khi learner lam bai. AI chi sinh truoc.

Moi cau hoi nen co:

- question_text.
- correct_answer.
- explanation.
- evidence_text.
- evidence_location.

## 5.2. Listening

### Cong nghe/model

- LLM API/local model de sinh cau hoi tu transcript.
- File storage cho audio.
- Optional transcript segments.

### Cach lam

```text
Admin upload audio + transcript
-> Spring Boot luu audio_url va transcript
-> AI sinh cau hoi tu transcript
-> Luu question/answer/explanation/timestamp
-> Admin publish
-> Learner nghe audio va lam bai
-> Backend cham bang correct_answer trong DB
-> Sau khi nop, hien transcript va explanation
```

### Diem dac biet

AI khong nghe audio truc tiep trong MVP. AI dung transcript de sinh cau hoi. Audio dung cho learner nghe.

## 5.3. Writing

### Cong nghe/model

- LLM API nen uu tien cho chat luong.
- Co the fallback local model nhung can review chat luong.
- IELTS rubric trong database.
- Pydantic schema.
- Cache bang hash.

### Cach lam

```text
Learner chon prompt
-> Viet essay
-> Spring Boot luu writing_submission
-> Spring Boot lay rubric va prompt template
-> Spring Boot tao request sang FastAPI
-> FastAPI goi LLM voi temperature thap
-> AI tra JSON grading
-> FastAPI validate schema
-> Spring Boot luu ai_grading_results voi writing_submission_id
-> Learner xem diem va feedback
```

### Output can co

- overall_band.
- task_response/task_achievement.
- coherence_cohesion.
- lexical_resource.
- grammatical_range_accuracy.
- strengths.
- weaknesses.
- grammar_errors.
- vocabulary_suggestions.
- improved_answer_text.

### Diem dac biet

Day khong phai "goi API cham bai" don gian. He thong co:

- rubric version.
- prompt template.
- JSON schema.
- cache.
- luu grading history.
- dashboard analytics.

## 5.4. Speaking

### Cong nghe/model

- LLM API cho grading theo transcript.
- Whisper/faster-whisper cho speech-to-text neu co audio.
- Optional pydub/librosa cho metrics audio co ban.

### Cach lam MVP

```text
Learner chon Speaking part/topic
-> He thong tao speaking_session
-> Learner tra loi bang text
-> Spring Boot luu speaking_turn
-> Goi FastAPI grade speaking
-> AI cham theo rubric
-> Luu ai_grading_results voi speaking_turn_id
-> AI co the tao follow-up question
```

### Cach lam audio

```text
Learner ghi am
-> Upload audio vao storage
-> Spring Boot gui audio_url sang FastAPI
-> FastAPI dung Whisper de transcribe
-> Spring Boot luu transcript
-> FastAPI/LLM cham speaking tu transcript
```

### Diem can luu y

Neu chi co transcript, pronunciation score chi la uoc luong han che. Muon pronunciation tot can speech assessment API nhu Azure Speech Assessment.

## 5.5. Vocabulary/Flashcard

### Cong nghe/model

- PostgreSQL.
- Rule-based spaced repetition.
- Optional LLM/local model de tao quiz.

### Cach lam

```text
Admin nhap vocabulary items theo topic/level
-> Learner hoc flashcard
-> Learner danh dau da nho/chua nho
-> Backend cap nhat user_vocabulary_progress
-> SRS rule tinh next_review_at
```

Vocabulary quiz:

```text
AI/template tao quiz tu vocabulary_items
-> Luu generated_questions
-> Learner lam quiz
-> Backend cham rule-based
```

Khong can goi AI moi lan learner hoc flashcard.

## 5.6. Grammar

### Cong nghe/model

- PostgreSQL.
- Rule-based scoring.
- Optional LLM/local model de tao bai tap.
- Optional LanguageTool de check grammar co ban.

### Cach lam

```text
Admin tao grammar topic
-> Admin/AI tao exercises
-> Luu grammar_exercises hoac generated_questions
-> Learner hoc lesson
-> Learner lam bai tap
-> Backend cham correct_answer
-> Cap nhat user_grammar_progress
```

Tu loi Writing/Speaking:

```text
AI grading tra grammar_errors
-> Backend dem error_type
-> Backend recommend grammar topic lien quan
```

## 5.7. Personalization/Recommendation

### Cong nghe/model

- SQL + Spring Boot analytics.
- Rule-based recommendation.
- Optional LLM de viet loi khuyen tu nhien.

### Cach lam

Khong nen dung AI API cho phan core analytics.

Backend tinh:

- Average score theo skill.
- Error rate theo question type.
- Grammar error frequency.
- Vocabulary topic progress.
- Speaking duration/fluency weakness.
- Writing criterion weakness.

Sau do rule-based recommend:

```text
Neu Reading sai True/False/Not Given > 40%
-> recommend Reading exercise cung question type

Neu grammar_errors articles xuat hien nhieu
-> recommend Grammar topic Articles

Neu vocabulary topic Environment yeu
-> recommend flashcard Environment
```

AI optional:

```text
Backend tao structured recommendation
-> AI viet thanh study plan tu nhien
```

Neu muon tiet kiem, dung template text thay AI.

## 6. Cac thu vien Python nen dung cho AI service

## 6.1. Bat buoc

```text
fastapi
uvicorn
pydantic
httpx
python-dotenv
```

## 6.2. Goi AI provider

Tuy provider:

```text
openai
google-genai
anthropic
```

## 6.3. Local model

Neu dung Ollama:

```text
httpx goi Ollama REST API
```

Hoac:

```text
transformers
torch
accelerate
```

Nhung Transformers local se nang hon, nen Ollama de demo de hon.

## 6.4. Speech-to-text/audio

```text
faster-whisper
pydub
librosa
```

## 6.5. NLP helper

```text
spacy
textstat
language-tool-python
nltk
```

Dung cho:

- readability.
- keyword extraction.
- grammar check co ban.
- word/sentence statistics.

## 7. Cac API co the dung

## 7.1. LLM API

Chon mot provider chinh:

- OpenAI API.
- Gemini API.
- Claude API.

Dung cho:

- Generate questions.
- Writing grading.
- Speaking grading.
- Feedback generation.

## 7.2. Speech-to-text API

Lua chon:

- Whisper API.
- Google Speech-to-Text.
- Azure Speech.

Neu muon local:

- faster-whisper.

## 7.3. Pronunciation assessment

Neu muon nang cao:

- Azure Speech Assessment.

Neu khong co, chi danh gia pronunciation o muc han che dua tren transcript/audio metrics.

## 8. Local model co nen dung khong?

Nen ho tro nhu mot option, nhung khong nen phu thuoc hoan toan.

### Stack local de demo

```text
Ollama + Qwen/Llama/Mistral/Gemma
```

Model goi y:

- Qwen2.5 7B.
- Llama 3.1 8B.
- Mistral 7B.
- Gemma 2 9B.

May yeu hon:

- Qwen2.5 3B.
- Phi-3 mini.
- Gemma 2B.

### Dung local model cho

- Vocabulary quiz.
- Grammar exercise.
- Draft Reading/Listening questions.
- Demo offline.

### Khong nen chi dung local model cho

- Writing grading can sat rubric.
- Speaking grading can feedback chat luong cao.

Vi model local nho co the cham lech va feedback chung chung.

## 9. Cach giam chi phi AI

1. Reading/Listening/Vocabulary/Grammar sinh truoc va luu DB.
2. Learner lam bai tu DB, backend cham rule-based.
3. Writing/Speaking co cache theo hash input.
4. Dung model re cho task don gian.
5. Dung model tot cho grading quan trong.
6. Dung Mock AI khi dev/test.
7. Co quota noi bo cho grading.
8. Batch generation khi admin tao cau hoi.
9. Luu prompt_version/model_name/token_usage.

## 10. Cach dam bao tinh nhat quan

1. Prompt template co version.
2. Rubric luu trong database.
3. Temperature thap cho grading.
4. Output JSON schema bat buoc.
5. Pydantic validate trong FastAPI.
6. Backend validate nghiep vu truoc khi luu.
7. Admin review truoc khi publish.
8. Cache ket qua grading neu input giong nhau.
9. Luu generated questions vao DB thay vi generate moi moi lan.

## 11. Phan nao la diem noi bat cua do an

Khong dinh vi do an la "goi AI API".

Nen dinh vi la:

> He thong luyen IELTS ca nhan hoa voi AI pipeline, rubric-based grading, schema validation, admin review, learning analytics va recommendation engine.

Diem noi bat:

- AI generation pipeline co kiem duyet.
- Rubric-based grading engine cho Writing/Speaking.
- Hybrid AI + rule-based scoring.
- Personalization dua tren lich su hoc.
- Provider adapter ho tro API/local/mock.
- Cache va cost control.
- Data-driven dashboard.

## 12. Tom tat cach lam theo module

| Module | Dung AI khong | Cach lam |
| --- | --- | --- |
| Auth/Profile | Khong | Spring Security + JWT |
| Reading generation | Co | AI sinh truoc, luu DB, admin duyet |
| Reading practice | Khong | Backend cham correct_answer |
| Listening generation | Co | AI sinh tu transcript, luu DB |
| Listening practice | Khong | Backend cham correct_answer |
| Writing grading | Co | LLM + rubric + schema + cache |
| Speaking grading | Co | LLM + transcript + rubric + schema |
| Speech-to-text | Co | Whisper/API/local model |
| Vocabulary flashcard | Khong | SRS rule-based |
| Vocabulary quiz generation | Optional | AI/local/template sinh truoc |
| Vocabulary quiz scoring | Khong | Rule-based |
| Grammar lesson | Khong | Admin content |
| Grammar exercise generation | Optional | AI/local/template sinh truoc |
| Grammar scoring | Khong | Rule-based |
| Recommendation core | Khong | SQL + rule-based analytics |
| Study plan text | Optional | AI viet loi khuyen hoac template |
| Dashboard | Khong | Query + aggregation |

## 13. Thu tu xay dung khuyen nghi

1. Backend Spring Boot core + PostgreSQL + Flyway.
2. React frontend core layout.
3. Auth/Profile.
4. Admin source data.
5. FastAPI Mock AI service.
6. Generated exercises/questions.
7. Reading end-to-end.
8. Listening end-to-end.
9. Real AI provider cho generation.
10. Writing grading.
11. Speaking text grading.
12. Vocabulary flashcard.
13. Grammar lesson/exercise.
14. Dashboard analytics.
15. Recommendation rule-based.
16. Cache/cost control.
17. Speech-to-text/audio.
18. Local model provider neu muon tang diem ky thuat.

## 14. Ket luan

Bo cong nghe va cach lam nen chot:

```text
Frontend:
  React + TypeScript + Tailwind CSS + shadcn/ui

Backend:
  Spring Boot + Spring Security + Spring Data JPA + Flyway

Database:
  PostgreSQL + JSONB + UUID

AI Service:
  Python FastAPI + Pydantic + provider adapter

AI Models/API:
  LLM API cho task kho
  Local model qua Ollama cho optional/demo
  Whisper/faster-whisper cho speech-to-text
  Mock provider cho dev/test

Analytics:
  SQL + rule-based recommendation

Deployment:
  Docker Compose
```

Gia tri cua do an nam o pipeline va thiet ke he thong:

- AI co schema.
- AI co cache.
- AI co review.
- AI co luu ket qua vao DB.
- Cham bai theo rubric.
- Phan tich lich su hoc bang rule-based analytics.
- De xuat hoc tap dua tren du lieu that cua learner.
