# Pipeline thiet ke - Generate cau hoi, lam bai, cham diem va recommendation

## 1. Muc tieu

Tai lieu nay tong hop cac pipeline xu ly chinh cua he thong IELTS AI Learning Platform.

Tap trung vao:

- Pipeline generate cau hoi.
- Pipeline admin review/publish.
- Pipeline learner lam bai Reading/Listening/Vocabulary/Grammar.
- Pipeline cham Writing theo rubric.
- Pipeline cham Speaking theo rubric.
- Pipeline speech-to-text.
- Pipeline recommendation dua tren lich su hoc.
- Pipeline cache, retry va error handling.

## 2. Nguyen tac thiet ke pipeline

## 2.1. Tach offline generation va online learning

Voi cac bai co dap an co dinh:

```text
Reading
Listening
Vocabulary quiz
Grammar exercise
```

Khong generate moi khi learner lam bai.

Dung pipeline:

```text
Source data -> AI generate -> validate -> save DB -> admin review -> publish -> learner uses DB
```

## 2.2. Writing/Speaking la online grading

Writing/Speaking phu thuoc vao cau tra loi rieng cua tung learner.

Dung pipeline:

```text
Learner submission -> rubric grading -> JSON validation -> score validation -> save result -> analytics
```

## 2.3. Spring Boot so huu database

FastAPI AI Service khong ghi truc tiep PostgreSQL trong MVP.

Spring Boot lam:

- Load data tu DB.
- Goi FastAPI.
- Validate response.
- Luu result.
- Cham rule-based.
- Tinh dashboard/recommendation.

FastAPI lam:

- Build prompt.
- Goi model/API/local provider.
- Validate output bang Pydantic.
- Retry neu output sai schema.
- Tra JSON ve Spring Boot.

## 3. Pipeline tong quan

```mermaid
flowchart TD
    A[Source Data in PostgreSQL]
    B[Spring Boot Backend]
    C[FastAPI AI Service]
    D[LLM API or Local Model]
    E[JSON Schema Validation]
    F[Save Generated Content]
    G[Admin Review]
    H[Published Exercises]
    I[Learner Practice]
    J[Rule-based Scoring]
    K[Writing/Speaking Submission]
    L[Rubric Grading]
    M[AI Grading Result]
    N[Learning Analytics]
    O[Recommendation]

    A --> B
    B --> C
    C --> D
    D --> C
    C --> E
    E --> B
    B --> F
    F --> G
    G --> H
    H --> I
    I --> J
    J --> N
    K --> L
    L --> M
    M --> N
    N --> O
```

## 4. Pipeline generate cau hoi Reading

## 4.1. Muc tieu

Tao cau hoi Reading tu passage trong database, gom:

- Question text.
- Options neu co.
- Correct answer.
- Explanation.
- Evidence text.
- Evidence location.
- Difficulty.

## 4.2. Input

Bang lien quan:

- `source_materials`
- `reading_passages`
- `prompt_templates`
- `ai_generation_requests`

Input gui sang FastAPI:

```json
{
  "source_material_id": "uuid",
  "passage": "...",
  "topic": "Environment",
  "level": "B2",
  "target_band": 6.5,
  "question_type": "true_false_not_given",
  "number_of_questions": 8,
  "prompt_version": "reading-generate-v1"
}
```

## 4.3. Pipeline

```mermaid
sequenceDiagram
    participant Admin
    participant BE as Spring Boot
    participant DB as PostgreSQL
    participant AI as FastAPI AI Service
    participant LLM as LLM API or Local Model

    Admin->>BE: Request generate Reading questions
    BE->>DB: Load reading_passage + source metadata
    BE->>DB: Load prompt_template
    BE->>DB: Create ai_generation_request PROCESSING
    BE->>AI: Generate Reading request
    AI->>AI: Build prompt with passage and schema
    AI->>LLM: Generate JSON questions
    LLM-->>AI: Raw response
    AI->>AI: Pydantic schema validation
    alt Valid
        AI-->>BE: Valid generated questions
        BE->>DB: Save generated_exercise DRAFT
        BE->>DB: Save generated_questions DRAFT
        BE->>DB: Update ai_generation_request COMPLETED
        BE-->>Admin: Show draft questions
    else Invalid
        AI->>AI: Retry repair once
        AI-->>BE: Error if still invalid
        BE->>DB: Update ai_generation_request FAILED
        BE-->>Admin: Show error
    end
```

## 4.4. Validation rules

FastAPI validate:

- Response dung JSON schema.
- Co du so cau hoi.
- Moi cau co `question_text`.
- Moi cau co `correct_answer`.
- Moi cau co `explanation`.

Spring Boot validate:

- Reading questions nen co `evidence_text`.
- `evidence_text` nen ton tai trong passage hoac gan dung passage.
- Question type khop voi request.
- Khong publish neu thieu answer/explanation.

## 4.5. Output luu DB

- `generated_exercises`
- `generated_questions`
- `ai_generation_requests`

Status ban dau:

```text
generated_exercises.status = draft
generated_questions.status = draft
```

## 5. Pipeline generate cau hoi Listening

## 5.1. Muc tieu

Tao cau hoi Listening tu transcript/audio metadata.

AI khong can nghe audio trong MVP. AI dung transcript va transcript segments.

## 5.2. Input

Bang lien quan:

- `listening_materials`
- `transcript_segments`
- `source_materials`
- `prompt_templates`

Input FastAPI:

```json
{
  "source_material_id": "uuid",
  "transcript": "...",
  "segments": [
    {
      "start_time": 10.5,
      "end_time": 14.2,
      "speaker": "A",
      "text": "..."
    }
  ],
  "topic": "Travel",
  "level": "B1",
  "question_type": "form_completion",
  "number_of_questions": 6
}
```

## 5.3. Pipeline

```text
Admin selects listening material
-> Spring Boot loads transcript and segments
-> Spring Boot creates ai_generation_request
-> FastAPI builds listening prompt
-> LLM generates questions/answers/explanations/timestamps
-> FastAPI validates schema
-> Spring Boot saves generated exercise/questions as draft
-> Admin reviews
-> Published for learner
```

## 5.4. Validation rules

- Cau hoi phai dua tren transcript.
- Dap an phai co trong transcript hoac suy ra truc tiep.
- Neu co segments, nen co `timestamp_start` va `timestamp_end`.
- Transcript chi hien sau khi learner nop bai.

## 6. Pipeline generate Vocabulary quiz

## 6.1. Muc tieu

Tao quiz tu vocabulary items da co trong DB.

Co the dung:

- Rule/template.
- Local model.
- LLM API.

## 6.2. Pipeline

```text
Admin selects topic/level
-> Spring Boot loads vocabulary_items
-> FastAPI/local rule generates quiz questions
-> Validate JSON
-> Save generated_exercise/questions
-> Admin review/publish
-> Learner answers quiz
-> Spring Boot scores rule-based
```

## 6.3. Vi du question types

- Meaning multiple choice.
- Fill in blank.
- Synonym/antonym.
- Collocation.
- Match word with definition.

## 7. Pipeline generate Grammar exercise

## 7.1. Muc tieu

Tao bai tap ngu phap tu grammar topic.

Input:

- Explanation.
- Formula.
- Examples.
- Common mistakes.
- Level.

## 7.2. Pipeline

```text
Admin selects grammar topic
-> Spring Boot loads grammar topic details
-> FastAPI/local model/template generates exercises
-> Validate JSON
-> Save grammar_exercises or generated_questions
-> Admin review/publish
-> Learner answers
-> Spring Boot scores rule-based
-> Update user_grammar_progress
```

## 8. Pipeline admin review va publish

## 8.1. Muc tieu

Dam bao noi dung AI generate khong duoc learner su dung truoc khi duoc duyet.

## 8.2. Status workflow

```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Reviewed
    Draft --> Rejected
    Reviewed --> Published
    Reviewed --> Rejected
    Published --> Archived
    Rejected --> Draft
```

## 8.3. Pipeline

```text
Generated content DRAFT
-> Admin opens review screen
-> Admin checks question, answer, explanation, evidence/timestamp
-> Admin edits if needed
-> Admin marks reviewed/published/rejected
-> Only published content appears to learner
```

## 8.4. Review checklist

Reading:

- Question clear.
- Correct answer correct.
- Explanation correct.
- Evidence exists in passage.

Listening:

- Answer appears in transcript.
- Timestamp/evidence correct.
- Question type suitable.

Vocabulary/Grammar:

- Correct answer accurate.
- Distractors not ambiguous.
- Explanation understandable.

## 9. Pipeline learner lam bai co dap an co dinh

Ap dung cho:

- Reading.
- Listening.
- Vocabulary quiz.
- Grammar exercise.

## 9.1. Pipeline

```mermaid
sequenceDiagram
    participant Learner
    participant FE as React Frontend
    participant BE as Spring Boot
    participant DB as PostgreSQL

    Learner->>FE: Open exercise
    FE->>BE: GET published exercise detail
    BE->>DB: Load generated_exercise/questions
    DB-->>BE: Exercise data
    BE-->>FE: Questions without hidden explanation
    Learner->>FE: Submit answers
    FE->>BE: POST answers
    BE->>DB: Load correct_answer
    BE->>BE: Rule-based scoring
    BE->>DB: Save practice_attempt/user_answers
    BE-->>FE: Score + explanation + evidence
```

## 9.2. Scoring logic

Backend compare:

```text
user_answers.answer
vs
generated_questions.correct_answer
```

Normalization:

- Trim spaces.
- Lowercase.
- Remove unnecessary punctuation.
- Accept aliases if configured.

Example `correct_answer`:

```json
{
  "type": "text",
  "accepted_values": ["two nights", "2 nights", "two"]
}
```

## 10. Pipeline cham Writing theo rubric

## 10.1. Muc tieu

Cham Writing Task 1/Task 2 theo IELTS rubric, theo tung criterion.

## 10.2. Pipeline

```mermaid
sequenceDiagram
    participant Learner
    participant FE as React Frontend
    participant BE as Spring Boot
    participant DB as PostgreSQL
    participant AI as FastAPI AI Service
    participant LLM as LLM API or Local Model

    Learner->>FE: Submit essay
    FE->>BE: POST writing submission
    BE->>DB: Save writing_submission
    BE->>DB: Load writing_prompt
    BE->>DB: Load rubrics by task_type/version
    BE->>DB: Load prompt_template
    BE->>BE: Build cache key
    BE->>DB: Check cached grading result
    alt Cache hit
        DB-->>BE: ai_grading_result
        BE-->>FE: Return existing grading
    else Cache miss
        BE->>AI: Grade Writing request
        AI->>AI: Build rubric prompt
        AI->>LLM: Criterion-level grading
        LLM-->>AI: Raw JSON
        AI->>AI: Pydantic validation
        AI-->>BE: Valid grading JSON
        BE->>BE: Score logic validation
        BE->>BE: Compute/check overall band
        BE->>DB: Save ai_grading_result
        BE-->>FE: Return grading feedback
    end
```

## 10.3. Criterion scoring

Writing Task 1:

- Task Achievement.
- Coherence and Cohesion.
- Lexical Resource.
- Grammatical Range and Accuracy.

Writing Task 2:

- Task Response.
- Coherence and Cohesion.
- Lexical Resource.
- Grammatical Range and Accuracy.

MVP:

```text
One model call -> returns all criterion scores -> backend computes/checks overall
```

Advanced:

```text
Multi-pass scoring -> one focused call per criterion -> backend aggregates
```

## 10.4. Output

```json
{
  "overall_band": 6.0,
  "criterion_scores": {
    "task_response": 6.0,
    "coherence_cohesion": 6.0,
    "lexical_resource": 6.5,
    "grammatical_range_accuracy": 5.5
  },
  "criterion_feedback": {},
  "strengths": [],
  "weaknesses": [],
  "grammar_errors": [],
  "vocabulary_suggestions": [],
  "feedback_text": "...",
  "improved_answer_text": "..."
}
```

## 10.5. Backend validation

- Score between 0.0 and 9.0.
- Score step should be 0.5.
- Overall close to average of criterion scores.
- If essay too short, Task Response/Task Achievement should not be too high.
- If many grammar errors, grammar score should not be too high.

## 11. Pipeline cham Speaking theo rubric

## 11.1. Muc tieu

Cham Speaking theo rubric, ho tro text answer truoc va audio/STT sau.

## 11.2. Text MVP pipeline

```text
Learner selects speaking prompt
-> Spring Boot creates speaking_session
-> Learner submits text answer
-> Spring Boot saves speaking_turn
-> Spring Boot loads speaking rubric
-> FastAPI grades answer by criterion
-> Spring Boot validates scores
-> Save ai_grading_result with speaking_turn_id
-> Optional AI follow-up question
```

## 11.3. Audio pipeline

```mermaid
sequenceDiagram
    participant Learner
    participant BE as Spring Boot
    participant Storage
    participant AI as FastAPI AI Service
    participant STT as Speech-to-Text
    participant LLM as LLM Provider
    participant DB as PostgreSQL

    Learner->>BE: Submit audio answer
    BE->>Storage: Save audio
    Storage-->>BE: audio_url
    BE->>AI: Transcribe audio_url
    AI->>STT: Run Whisper/STT
    STT-->>AI: Transcript + segments
    AI-->>BE: Transcript response
    BE->>DB: Save speaking_turn with audio_url/transcript
    BE->>DB: Load speaking rubric
    BE->>AI: Grade Speaking request
    AI->>LLM: Rubric grading
    LLM-->>AI: Raw JSON
    AI->>AI: Validate schema
    AI-->>BE: Valid grading
    BE->>DB: Save ai_grading_result
    BE-->>Learner: Feedback + optional follow-up
```

## 11.4. Criterion scoring

- Fluency and Coherence.
- Lexical Resource.
- Grammatical Range and Accuracy.
- Pronunciation.

Pronunciation note:

- If only transcript exists, pronunciation is limited-confidence estimate.
- For real pronunciation scoring, use speech assessment API such as Azure Speech Assessment.

## 12. Pipeline speech-to-text

## 12.1. MVP

Speaking can accept text answer first.

## 12.2. Audio STT pipeline

```text
React records audio
-> Spring Boot receives file
-> Storage saves file
-> Spring Boot sends audio_url to FastAPI
-> FastAPI uses Whisper/faster-whisper/API
-> FastAPI returns transcript/segments/duration
-> Spring Boot saves transcript in speaking_turns
```

## 12.3. Output

```json
{
  "transcript": "...",
  "duration_seconds": 83,
  "segments": [
    {
      "start_time": 0.0,
      "end_time": 4.2,
      "text": "..."
    }
  ]
}
```

## 13. Pipeline recommendation

## 13.1. Muc tieu

De xuat bai hoc tiep theo dua tren lich su hoc.

Core recommendation khong can AI API.

## 13.2. Pipeline

```mermaid
flowchart TD
    A[practice_attempts]
    B[user_answers]
    C[ai_grading_results]
    D[user_vocabulary_progress]
    E[user_grammar_progress]
    F[Analytics Service]
    G[Rule Engine]
    H[learning_recommendations]
    I[Optional AI Natural Language Plan]

    A --> F
    B --> F
    C --> F
    D --> F
    E --> F
    F --> G
    G --> H
    G --> I
    I --> H
```

## 13.3. Analytics

Backend tinh:

- Average score by skill.
- Error rate by question type.
- Weak topic.
- Writing criterion weakness.
- Speaking criterion weakness.
- Grammar error frequency.
- Vocabulary review status.

## 13.4. Rules

Examples:

```text
If reading error_rate true_false_not_given > 40%
-> recommend Reading exercise with same question type

If writing grammar score < target_band - 1.0
-> recommend grammar topics from grammar_errors

If vocabulary topic progress < 70%
-> recommend flashcards for that topic

If speaking answer duration < threshold
-> recommend Speaking Part 2 expansion practice
```

## 13.5. AI optional

AI chi dung de viet recommendation thanh study plan tu nhien.

Khong goi AI moi lan learner mo dashboard.

## 14. Cache pipeline

## 14.1. Dung cho AI grading

Cache key:

```text
hash(
  submission_type
  + prompt_text/question_text
  + answer_text/transcript
  + rubric_version
  + prompt_version
  + model_name
)
```

## 14.2. Pipeline

```text
Build cache key
-> Check existing ai_grading_results/cache table
-> If hit: return old result
-> If miss: call AI
-> Save result with cache key
```

## 15. Error handling pipeline

## 15.1. AI generate fail

```text
AI output invalid
-> FastAPI retry repair once
-> If still invalid, return error
-> Spring Boot marks ai_generation_request failed
-> Admin can retry/regenerate
```

## 15.2. AI grading fail

```text
Learner submission saved first
-> AI grading fails
-> Mark grading status failed/pending retry
-> Learner does not lose answer
-> Allow retry grading later
```

## 15.3. STT fail

```text
Audio saved
-> STT fails
-> Allow retry
-> Allow learner to enter transcript manually
```

## 16. Pipeline theo module tom tat

| Module | Pipeline chinh | AI dung luc nao | Cham diem |
| --- | --- | --- | --- |
| Reading | Source -> AI generate -> review -> publish -> learner practice | Admin generate only | Rule-based |
| Listening | Transcript -> AI generate -> review -> publish -> learner practice | Admin generate only | Rule-based |
| Writing | Learner essay -> rubric grading -> save feedback | On submission | AI criterion scoring + backend aggregate |
| Speaking | Learner answer -> STT optional -> rubric grading -> follow-up | On submission | AI criterion scoring + backend aggregate |
| Vocabulary | Items -> flashcard/quiz -> progress | Optional generate quiz | Rule-based |
| Grammar | Topic -> exercise -> progress | Optional generate exercise | Rule-based |
| Recommendation | Learning history -> analytics -> rules -> recommendation | Optional text generation | Not scoring |

## 17. Pipeline implementation order

1. Implement source data CRUD.
2. Implement AI generation request records.
3. Implement FastAPI Mock AI.
4. Implement generated_exercises/generated_questions save flow.
5. Implement admin review/publish.
6. Implement Reading practice scoring.
7. Implement Listening practice scoring.
8. Implement real LLM provider for generation.
9. Implement Writing grading pipeline.
10. Implement Speaking text grading pipeline.
11. Implement STT/audio pipeline.
12. Implement recommendation analytics.
13. Implement cache and retry.

## 18. Ket luan

He thong nen co 3 pipeline cot loi:

```text
1. Content generation pipeline:
   Source data -> AI -> schema validation -> DB -> admin review -> publish

2. Learning/scoring pipeline:
   Published exercise -> learner answers -> rule-based scoring -> attempts/history

3. Rubric grading pipeline:
   Writing/Speaking submission -> rubric -> AI criterion scoring -> backend validation -> ai_grading_results
```

Them pipeline recommendation:

```text
Learning history -> SQL analytics -> rule engine -> recommended items -> optional AI study plan text
```

Diem noi bat cua do an nam o viec cac pipeline nay duoc thiet ke co:

- Source traceability.
- Schema validation.
- Admin review.
- Rule-based scoring cho bai co dap an co dinh.
- Rubric-based criterion scoring cho Writing/Speaking.
- Cache va retry.
- Analytics va personalization.
