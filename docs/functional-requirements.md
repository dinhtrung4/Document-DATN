# Tai lieu yeu cau chuc nang - Website hoc tieng Anh va luyen thi IELTS bang AI

## 1. Tong quan

### 1.1. Ten he thong

Website hoc tieng Anh va luyen thi IELTS ca nhan hoa theo trinh do muc tieu.

### 1.2. Muc tieu

He thong ho tro nguoi hoc:

- Xac dinh trinh do hien tai va band IELTS mong muon.
- Luyen 4 ky nang IELTS: Listening, Reading, Writing, Speaking.
- Hoc tu vung theo chu de bang flashcard va bai tap.
- Hoc ngu phap theo cap do va lam bai tap ung dung.
- Nhan bai luyen phu hop voi trinh do, muc tieu va lich su hoc tap.
- Nhan diem uoc luong va feedback Writing/Speaking theo rubric IELTS.

### 1.3. Nguyen tac thiet ke du lieu va AI

He thong khong can luu san toan bo cau hoi thu cong cho moi bai hoc. Thay vao do:

- Database luu **du lieu goc**: passage Reading, transcript/audio Listening, topic, tu vung, ngu phap, rubric IELTS, de Writing/Speaking, metadata ve level va dang bai.
- AI su dung du lieu goc trong database de **tao cau hoi, dap an dung, giai thich, goi y hoc tap va feedback**.
- Ket qua AI tao ra duoc luu lai de:
  - Nguoi hoc lam bai.
  - Tai su dung neu can.
  - Admin kiem duyet/chat luong.
  - Truy vet nguon sinh cau hoi va phan tich ket qua hoc tap.

## 2. Pham vi he thong

### 2.1. Trong pham vi

- Quan ly tai khoan nguoi hoc.
- Ho so hoc tap va band muc tieu.
- Placement test co ban.
- Luyen Reading theo passage trong database.
- Luyen Listening theo audio/transcript trong database.
- Luyen Writing Task 1/Task 2 va cham bang AI theo rubric IELTS.
- Luyen Speaking Part 1/2/3, co the tra loi bang text hoac audio.
- Speech-to-text cho cau tra loi Speaking bang audio.
- Tu vung theo topic IELTS, flashcard va quiz.
- Ngu phap theo cap do, bai hoc va bai tap.
- Dashboard theo doi tien do.
- AI de xuat bai hoc tiep theo.
- Admin quan ly du lieu goc va kiem duyet noi dung AI tao.

### 2.2. Ngoai pham vi phien ban dau

- Cam ket diem Writing/Speaking trung khop 100% voi giam khao that.
- Su dung de thi IELTS co ban quyen.
- Cham pronunciation nang cao neu chua tich hop speech assessment API chuyen dung.
- Thi thu IELTS day du nhu ky thi that trong phien ban MVP.

## 3. Doi tuong su dung

### 3.1. Nguoi hoc

- Dang ky, dang nhap va cap nhat ho so.
- Chon band muc tieu.
- Lam bai luyen, xem diem, xem feedback.
- Hoc tu vung, ngu phap.
- Theo doi tien do va nhan goi y hoc tap.

### 3.2. Quan tri vien

- Quan ly du lieu goc.
- Quan ly chu de, level, dang cau hoi.
- Duyet cau hoi/dap an/giai thich do AI tao.
- Quan ly nguoi dung va thong ke he thong.

### 3.3. AI service

- Tao cau hoi, dap an, giai thich tu du lieu goc.
- Cham Writing/Speaking theo rubric.
- Phan tich loi ca nhan.
- Tao de xuat hoc tap.

## 4. Yeu cau chuc nang tong quat

## 4.1. Quan ly tai khoan va ho so hoc tap

### FR-001. Dang ky tai khoan

Nguoi dung co the tao tai khoan bang email va mat khau.

Du lieu can luu:

- Ho ten.
- Email.
- Mat khau da hash.
- Vai tro: learner/admin.
- Ngay tao tai khoan.

### FR-002. Dang nhap/dang xuat

Nguoi dung co the dang nhap, dang xuat va duy tri phien dang nhap an toan.

### FR-003. Cap nhat ho so hoc tap

Nguoi hoc co the cap nhat:

- Trinh do hien tai.
- Band IELTS muc tieu.
- Ky nang uu tien.
- Thoi luong hoc moi ngay.
- Chu de quan tam.

### FR-004. Placement test

He thong cung cap bai test dau vao de uoc luong trinh do ban dau.

Phien ban MVP co the gom:

- Reading ngan.
- Listening ngan.
- Writing Task 2 rut gon.
- Speaking Part 1 bang text hoac audio.

Ket qua can tra ve:

- Estimated overall band.
- Estimated band tung ky nang.
- Diem manh/yeu ban dau.
- Goi y lo trinh hoc.

## 4.2. Dashboard ca nhan hoa

### FR-005. Hien thi tong quan tien do

Dashboard hien thi:

- Band muc tieu.
- Band hien tai uoc luong.
- Diem trung binh tung ky nang.
- So bai da lam.
- Tu vung da hoc.
- Chu diem ngu phap da hoan thanh.
- Streak hoc tap.
- Goi y bai hoc tiep theo.

### FR-006. Phan tich diem yeu

He thong phan tich lich su hoc tap de xac dinh:

- Dang cau hoi Reading hay sai.
- Dang cau hoi Listening hay sai.
- Tieu chi Writing con yeu.
- Tieu chi Speaking con yeu.
- Loi ngu phap lap lai.
- Tu vung/collocation dung sai hoac thieu.

## 4.3. Luyen Reading

### FR-007. Chon bai Reading theo trinh do

Nguoi hoc co the chon bai Reading theo:

- Band muc tieu.
- Level: A2, B1, B2, C1 hoac IELTS 4.0-8.0.
- Topic.
- Dang cau hoi.

### FR-008. AI tao cau hoi Reading tu passage trong database

Input:

- Reading passage trong database.
- Level muc tieu.
- Dang cau hoi can tao.
- So luong cau hoi.
- Yeu cau ve do kho.

Output AI can tao:

- Cau hoi.
- Cac lua chon neu la multiple choice.
- Dap an dung.
- Giai thich.
- Vi tri bang chung trong passage.
- Tu vung quan trong.

Dang cau hoi can ho tro:

- Multiple Choice.
- True/False/Not Given.
- Matching Headings.
- Matching Information.
- Sentence Completion.
- Summary Completion.
- Short Answer.

### FR-009. Lam bai va cham Reading

Nguoi hoc tra loi cau hoi, he thong:

- Cham cau dung/sai.
- Tinh diem.
- Hien thi dap an.
- Hien thi giai thich.
- Luu ket qua va loi sai.

## 4.4. Luyen Listening

### FR-010. Chon bai Listening theo trinh do

Nguoi hoc chon bai nghe theo:

- Level.
- Topic.
- Dang cau hoi.
- Accent neu co metadata.

### FR-011. AI tao cau hoi Listening tu transcript trong database

Input:

- Audio metadata.
- Transcript.
- Level muc tieu.
- Dang cau hoi.
- So luong cau hoi.

Output AI can tao:

- Cau hoi.
- Dap an dung.
- Giai thich.
- Timestamp hoac doan transcript lien quan neu co.
- Tu/cum tu quan trong.

Dang cau hoi can ho tro:

- Form Completion.
- Multiple Choice.
- Matching.
- Sentence Completion.
- Map/Diagram Labeling.
- Short Answer.

### FR-012. Lam bai va cham Listening

Nguoi hoc nghe audio va tra loi. He thong:

- Cham dap an.
- Hien thi transcript sau khi nop bai.
- Hien thi giai thich.
- Luu ket qua va loi sai.

## 4.5. Luyen Writing

### FR-013. Luyen Writing Task 1

Nguoi hoc chon de Writing Task 1 theo dang:

- Line graph.
- Bar chart.
- Pie chart.
- Table.
- Map.
- Process.

Nguoi hoc viet bai va nop de AI cham.

### FR-014. Luyen Writing Task 2

Nguoi hoc chon de Writing Task 2 theo dang:

- Opinion Essay.
- Discussion Essay.
- Advantage/Disadvantage Essay.
- Problem/Solution Essay.
- Two-part Question.

Nguoi hoc viet bai va nop de AI cham.

### FR-015. AI cham Writing theo IELTS rubric

Input:

- De bai.
- Bai viet nguoi hoc.
- Loai task.
- Rubric IELTS tu database.
- Band muc tieu cua nguoi hoc.

Output:

- Estimated overall band.
- Diem tung tieu chi.
- Nhan xet diem manh.
- Nhan xet diem yeu.
- Loi ngu phap.
- Loi tu vung/collocation.
- Goi y cai thien.
- Phien ban bai viet cai thien.

Tieu chi Task 1:

- Task Achievement.
- Coherence and Cohesion.
- Lexical Resource.
- Grammatical Range and Accuracy.

Tieu chi Task 2:

- Task Response.
- Coherence and Cohesion.
- Lexical Resource.
- Grammatical Range and Accuracy.

Luu y hien thi:

- Diem phai duoc goi la **Estimated IELTS Band Score**.
- He thong can giai thich ly do cho diem tung tieu chi.

## 4.6. Luyen Speaking

### FR-016. Luyen Speaking Part 1

Nguoi hoc tra loi cac cau hoi ngan theo topic quen thuoc.

### FR-017. Luyen Speaking Part 2

He thong hien thi cue card. Nguoi hoc tra loi bang text hoac audio.

### FR-018. Luyen Speaking Part 3

AI tao cau hoi follow-up dua tren topic va cau tra loi truoc cua nguoi hoc.

### FR-019. Speech-to-text cho cau tra loi audio

Neu nguoi hoc ghi am:

- He thong luu audio.
- Speech-to-text tao transcript.
- Transcript duoc dung de AI cham noi dung, grammar, vocabulary va coherence.
- Neu co speech assessment API, he thong bo sung pronunciation metrics.

### FR-020. AI cham Speaking theo IELTS rubric

Input:

- Cau hoi Speaking.
- Cau tra loi text hoac transcript.
- Audio metadata neu co.
- Rubric IELTS Speaking tu database.
- Band muc tieu.

Output:

- Estimated overall band.
- Fluency and Coherence.
- Lexical Resource.
- Grammatical Range and Accuracy.
- Pronunciation.
- Feedback chi tiet.
- Loi grammar/vocabulary.
- Goi y cau tra loi tot hon.
- Follow-up question.

Tieu chi Speaking:

- Fluency and Coherence.
- Lexical Resource.
- Grammatical Range and Accuracy.
- Pronunciation.

## 4.7. Hoc tu vung

### FR-021. Tu vung theo topic IELTS

He thong cung cap tu vung theo topic:

- Education.
- Environment.
- Technology.
- Health.
- Work.
- Travel.
- Family.
- Culture.
- Media.
- Crime.
- Government.
- Economy.
- Science.
- Globalization.

Moi tu vung can co:

- Word.
- IPA.
- Part of speech.
- Nghia tieng Viet.
- English definition.
- Example sentence.
- Collocation.
- Synonym.
- Antonym.
- Topic.
- Level.
- Audio pronunciation neu co.

### FR-022. Flashcard

Nguoi hoc co the:

- Hoc flashcard theo topic/level.
- Xem mat truoc/mat sau.
- Danh dau: da nho, chua nho, can on lai.
- Luu tu yeu thich.
- Nghe phat am.

### FR-023. Spaced repetition

He thong lap lich on tap dua tren muc do ghi nho:

- Nho tot: on lai sau thoi gian dai hon.
- Chua nho: on lai som.
- Sai nhieu lan: uu tien xuat hien lai.

### FR-024. Vocabulary quiz

He thong tao bai tap tu vung tu database:

- Chon nghia dung.
- Dien tu vao cho trong.
- Chon collocation dung.
- Synonym/antonym.
- Ghep tu voi dinh nghia.
- Viet cau voi tu cho san.

AI co the tao cau hoi quiz, dap an va giai thich dua tren vocabulary items trong database.

## 4.8. Hoc ngu phap

### FR-025. Chu diem ngu phap theo cap do

He thong quan ly chu diem ngu phap:

Co ban:

- Tenses.
- Articles.
- Prepositions.
- Subject-verb agreement.
- Countable/uncountable nouns.

Trung cap:

- Passive voice.
- Relative clauses.
- Conditional sentences.
- Modal verbs.
- Gerund/infinitive.
- Comparatives.

IELTS Writing/Speaking:

- Complex sentences.
- Participle clauses.
- Inversion.
- Nominalization.
- Hedging language.
- Cause-effect structures.
- Concession structures.

### FR-026. Bai hoc ngu phap

Moi bai hoc gom:

- Giai thich.
- Cong thuc.
- Vi du dung.
- Loi sai thuong gap.
- Ung dung trong IELTS.
- Bai tap di kem.

### FR-027. AI tao bai tap ngu phap tu database

Input:

- Grammar topic.
- Level.
- Vi du va loi sai thuong gap trong database.
- So luong cau hoi.
- Dang bai tap.

Output:

- Cau hoi.
- Dap an.
- Giai thich.
- Goi y bai hoc lien quan.

Dang bai tap:

- Multiple choice.
- Fill in the blank.
- Error correction.
- Sentence transformation.
- Rewrite sentence.
- Combine sentences.

## 4.9. Ca nhan hoa bang AI

### FR-028. Goi y bai hoc tiep theo

AI de xuat bai hoc dua tren:

- Band hien tai.
- Band muc tieu.
- Lich su lam bai.
- Loi sai lap lai.
- Tu vung chua nho.
- Ngu phap yeu.

### FR-029. Tao study plan

He thong tao ke hoach hoc gom:

- Ky nang can uu tien.
- Bai luyen Reading/Listening.
- De Writing/Speaking.
- Flashcard can on.
- Grammar topic can hoc.

### FR-030. Goi y tu vung/ngu phap tu loi Writing/Speaking

Sau khi cham Writing/Speaking, AI:

- Trich loi ngu phap.
- Trich loi word choice/collocation.
- Goi y tu vung thay the.
- Goi y bai grammar can hoc.
- Tao bai tap luyen lai loi sai.

## 4.10. Quan tri noi dung

### FR-031. Quan ly du lieu goc

Admin co the them/sua/xoa:

- Reading passages.
- Listening audio/transcripts.
- Writing prompts.
- Speaking topics/questions/cue cards.
- Vocabulary items.
- Grammar lessons.
- Rubrics.
- AI prompt templates.

### FR-032. Tao noi dung bang AI

Admin co the chon mot ban ghi du lieu goc va yeu cau AI tao:

- Cau hoi.
- Dap an.
- Giai thich.
- Tu vung lien quan.
- Bai tap lien quan.

### FR-033. Duyet noi dung AI tao

Noi dung AI tao can co trang thai:

- Draft.
- Reviewed.
- Published.
- Rejected.

Chi noi dung Published moi hien thi cho nguoi hoc neu he thong bat che do kiem duyet.

## 5. Yeu cau du lieu

## 5.1. Nguyen tac du lieu

He thong can tach 2 nhom du lieu:

1. **Du lieu goc**: noi dung nen tang duoc luu trong database.
2. **Du lieu AI tao**: cau hoi, dap an, giai thich, feedback, study plan duoc AI tao tu du lieu goc.

Vi du:

- Reading passage la du lieu goc.
- Cau hoi True/False/Not Given, dap an va giai thich la du lieu AI tao.
- Listening transcript la du lieu goc.
- Form completion questions va timestamp dap an la du lieu AI tao.
- Rubric IELTS la du lieu goc.
- Diem va feedback Writing/Speaking la du lieu AI tao.

## 5.2. Nhom du lieu nguoi dung

### Users

Muc dich: luu thong tin tai khoan.

Truong du lieu goi y:

- id.
- full_name.
- email.
- password_hash.
- role.
- created_at.
- updated_at.

### LearnerProfiles

Muc dich: luu ho so hoc tap.

Truong du lieu goi y:

- id.
- user_id.
- current_level.
- target_band.
- preferred_skills.
- daily_study_minutes.
- weak_skills.
- placement_completed.
- created_at.
- updated_at.

## 5.3. Nhom du lieu goc cho IELTS skills

### SourceMaterials

Muc dich: luu noi dung goc de AI tao bai tap.

Truong du lieu goi y:

- id.
- skill: reading/listening/writing/speaking/vocabulary/grammar.
- title.
- topic_id.
- level.
- content_text.
- media_url.
- transcript.
- source_type: self_created/ai_created/open_dataset/user_generated.
- license_info.
- status.
- created_by.
- created_at.
- updated_at.

### ReadingPassages

Muc dich: luu passage Reading.

Truong du lieu goi y:

- id.
- source_material_id.
- title.
- passage_text.
- topic_id.
- level.
- estimated_band.
- word_count.
- status.

### ListeningMaterials

Muc dich: luu audio va transcript.

Truong du lieu goi y:

- id.
- source_material_id.
- title.
- audio_url.
- transcript.
- topic_id.
- level.
- accent.
- duration_seconds.
- status.

### WritingPrompts

Muc dich: luu de Writing.

Truong du lieu goi y:

- id.
- task_type: task_1/task_2.
- prompt_text.
- chart_image_url.
- essay_type.
- topic_id.
- level.
- status.

### SpeakingPrompts

Muc dich: luu cau hoi/cue card Speaking goc.

Truong du lieu goi y:

- id.
- part: part_1/part_2/part_3.
- topic_id.
- prompt_text.
- cue_points.
- level.
- status.

## 5.4. Nhom du lieu AI tao cau hoi/dap an/giai thich

### AIGenerationRequests

Muc dich: luu moi lan goi AI de tao noi dung.

Truong du lieu goi y:

- id.
- source_material_id.
- requested_by.
- generation_type: reading_questions/listening_questions/vocabulary_quiz/grammar_exercise/speaking_followup.
- input_parameters_json.
- prompt_template_id.
- model_name.
- status.
- error_message.
- created_at.

### GeneratedExercises

Muc dich: luu mot bo bai tap do AI tao.

Truong du lieu goi y:

- id.
- generation_request_id.
- source_material_id.
- skill.
- title.
- topic_id.
- level.
- question_type.
- estimated_time_minutes.
- status: draft/reviewed/published/rejected.
- created_at.

### GeneratedQuestions

Muc dich: luu cau hoi do AI tao.

Truong du lieu goi y:

- id.
- exercise_id.
- question_text.
- question_type.
- order_index.
- options_json.
- correct_answer_json.
- explanation.
- evidence_text.
- evidence_location.
- timestamp_start.
- timestamp_end.
- difficulty.
- status.

Ghi chu:

- `options_json` dung cho multiple choice/matching.
- `correct_answer_json` luu dap an linh hoat cho nhieu dang cau hoi.
- `evidence_text` luu cau/doan trong passage/transcript lam can cu.
- `timestamp_start` va `timestamp_end` dung cho Listening neu co.

## 5.5. Nhom du lieu cham diem Writing/Speaking

### Rubrics

Muc dich: luu rubric IELTS.

Truong du lieu goi y:

- id.
- skill: writing/speaking.
- task_type.
- criterion.
- band_score.
- descriptor_text.
- source_reference.
- created_at.

### WritingSubmissions

Muc dich: luu bai Writing cua nguoi hoc.

Truong du lieu goi y:

- id.
- user_id.
- writing_prompt_id.
- essay_text.
- word_count.
- submitted_at.

### SpeakingSubmissions

Muc dich: luu cau tra loi Speaking.

Truong du lieu goi y:

- id.
- user_id.
- speaking_prompt_id.
- answer_text.
- audio_url.
- transcript.
- duration_seconds.
- submitted_at.

### AIGradingResults

Muc dich: luu ket qua cham Writing/Speaking bang AI.

Truong du lieu goi y:

- id.
- user_id.
- submission_type: writing/speaking.
- writing_submission_id: dung khi cham Writing.
- speaking_turn_id: dung khi cham Speaking.
- overall_band.
- criterion_scores_json.
- strengths_json.
- weaknesses_json.
- grammar_errors_json.
- vocabulary_suggestions_json.
- pronunciation_metrics_json.
- feedback_text.
- improved_answer_text.
- model_name.
- prompt_template_id.
- created_at.

## 5.6. Nhom du lieu tu vung

### VocabularyItems

Muc dich: luu tu vung.

Truong du lieu goi y:

- id.
- word.
- ipa.
- part_of_speech.
- meaning_vi.
- definition_en.
- example_sentence.
- collocations_json.
- synonyms_json.
- antonyms_json.
- topic_id.
- level.
- audio_url.
- status.

### UserVocabularyProgress

Muc dich: luu tien do hoc flashcard.

Truong du lieu goi y:

- id.
- user_id.
- vocabulary_item_id.
- memory_status: new/learning/reviewed/mastered.
- ease_score.
- next_review_at.
- review_count.
- last_reviewed_at.

## 5.7. Nhom du lieu ngu phap

### GrammarTopics

Muc dich: luu chu diem ngu phap.

Truong du lieu goi y:

- id.
- title.
- level.
- explanation.
- formula.
- examples_json.
- common_mistakes_json.
- ielts_application.
- status.

### GrammarExercises

Muc dich: luu bai tap ngu phap do admin tao hoac AI tao.

Truong du lieu goi y:

- id.
- grammar_topic_id.
- generated_exercise_id.
- exercise_type.
- question_text.
- options_json.
- correct_answer_json.
- explanation.
- level.
- status.

### UserGrammarProgress

Muc dich: luu tien do hoc ngu phap.

Truong du lieu goi y:

- id.
- user_id.
- grammar_topic_id.
- mastery_level.
- correct_count.
- incorrect_count.
- last_practiced_at.

## 5.8. Nhom du lieu lam bai va lich su hoc tap

### PracticeAttempts

Muc dich: luu moi lan nguoi hoc lam bai.

Truong du lieu goi y:

- id.
- user_id.
- exercise_id.
- skill.
- started_at.
- submitted_at.
- score.
- max_score.
- estimated_band.
- time_spent_seconds.

### UserAnswers

Muc dich: luu cau tra loi cho tung cau hoi.

Truong du lieu goi y:

- id.
- attempt_id.
- question_id.
- answer_json.
- is_correct.
- score.
- feedback.

### LearningRecommendations

Muc dich: luu goi y hoc tap do AI tao.

Truong du lieu goi y:

- id.
- user_id.
- recommendation_type.
- recommendation_text.
- recommended_items_json.
- reason.
- status.
- created_at.

## 6. Luong AI tao cau hoi, dap an va giai thich

### 6.1. Luong tao cau hoi Reading

1. Admin chon Reading passage trong database.
2. Admin chon level, dang cau hoi va so luong cau hoi.
3. He thong lay passage, topic, level va prompt template.
4. AI tao cau hoi, dap an, giai thich va evidence.
5. He thong validate output theo JSON schema.
6. Luu vao GeneratedExercises va GeneratedQuestions.
7. Admin duyet hoac he thong publish neu dat dieu kien.
8. Nguoi hoc lam bai.

### 6.2. Luong tao cau hoi Listening

1. Admin chon Listening material co audio va transcript.
2. He thong gui transcript, level, dang cau hoi cho AI.
3. AI tao cau hoi, dap an, giai thich va timestamp/evidence neu co.
4. He thong luu ket qua.
5. Admin duyet.
6. Nguoi hoc nghe audio va lam bai.

### 6.3. Luong tao vocabulary quiz

1. He thong chon tap tu vung theo topic/level.
2. AI tao quiz tu vocabulary items trong database.
3. AI tao dap an va giai thich.
4. He thong luu cau hoi.
5. Nguoi hoc lam quiz va ket qua duoc cap nhat vao UserVocabularyProgress.

### 6.4. Luong tao grammar exercise

1. He thong chon grammar topic.
2. AI dung explanation, examples va common mistakes trong database.
3. AI tao bai tap, dap an va giai thich.
4. He thong luu cau hoi.
5. Nguoi hoc lam bai va cap nhat UserGrammarProgress.

### 6.5. Luong cham Writing

1. Nguoi hoc nop bai Writing.
2. He thong lay prompt, essay, rubric va band muc tieu.
3. AI cham tung tieu chi.
4. AI tao feedback, loi sai, goi y va improved version.
5. He thong validate output va luu AIGradingResults.
6. He thong cap nhat dashboard va goi y bai hoc tiep theo.

### 6.6. Luong cham Speaking

1. Nguoi hoc tra loi Speaking bang text hoac audio.
2. Neu la audio, he thong tao transcript bang speech-to-text.
3. He thong lay cau hoi, transcript, rubric va band muc tieu.
4. AI cham tung tieu chi.
5. AI tao feedback, suggested answer va follow-up question.
6. He thong luu AIGradingResults.
7. Follow-up question co the duoc luu thanh GeneratedQuestions hoac SpeakingPrompts tam thoi cho session.

## 7. Nguon du lieu thu thap

### 7.1. Nguon nen uu tien

- Noi dung tu bien soan.
- Noi dung do AI tao va admin duyet.
- Public domain/open datasets co license ro rang.
- Du lieu nguoi hoc tao trong he thong.
- IELTS public band descriptors de xay dung rubric.

### 7.2. Du lieu nen tranh

- De thi IELTS Cambridge bi ban quyen.
- De thi that khong co quyen su dung.
- Audio, passage, bai mau copy tu website hoc IELTS neu khong co license.
- Du lieu nguoi dung dung cho huan luyen/phu hop AI khi chua co dong y.

### 7.3. Cach tao dataset phu hop cho do an

Phuong an thuc te:

1. Tao danh sach topic IELTS.
2. Tao Reading passages bang AI hoac tu bien soan.
3. Tao Listening scripts bang AI, sau do dung text-to-speech de tao audio.
4. Tao Writing/Speaking prompts theo topic.
5. Tao vocabulary items theo topic/level.
6. Tao grammar lessons va examples.
7. Luu tat ca vao database voi metadata level/topic.
8. Dung AI tao cau hoi/dap an/giai thich tu cac ban ghi nay.
9. Admin duyet va publish.

## 8. Yeu cau phi chuc nang

### 8.1. Bao mat

- Mat khau phai duoc hash.
- Phan quyen learner/admin.
- Audio va bai viet cua nguoi dung can duoc bao ve.
- Khong cong khai du lieu hoc tap ca nhan.

### 8.2. Chat luong AI

- Output AI phai theo JSON schema.
- Moi ket qua cham diem can luu model_name va prompt_template_id.
- Cau hoi AI tao can co evidence/explanation.
- Noi dung AI tao nen co quy trinh kiem duyet.
- Diem Writing/Speaking phai hien thi la diem uoc luong.

### 8.3. Kha nang mo rong

- Co the them nhieu level, topic va dang cau hoi.
- Co the doi AI provider thong qua AI service layer.
- Co the cache cau hoi AI tao de giam chi phi.

### 8.4. Hieu nang

- Cac bai da duoc AI tao nen lay tu database thay vi goi AI moi moi lan.
- Goi AI nen chay bat dong bo voi tac vu sinh bai lon.
- File audio nen luu tren object storage hoac dich vu luu tru phu hop.

## 9. Uu tien trien khai MVP

### 9.1. Bat buoc

1. Dang ky/dang nhap.
2. Ho so hoc tap va band muc tieu.
3. Dashboard co ban.
4. Reading: passage trong database, AI tao cau hoi/dap an/giai thich.
5. Listening: transcript/audio trong database, AI tao cau hoi/dap an/giai thich.
6. Writing Task 2: AI cham theo rubric.
7. Speaking Part 1/2: text hoac audio, AI cham theo rubric.
8. Tu vung theo topic va flashcard.
9. Ngu phap va bai tap co dap an/giai thich.
10. Admin quan ly du lieu goc va noi dung AI tao.

### 9.2. Nen co neu con thoi gian

1. Placement test day du 4 ky nang.
2. Writing Task 1.
3. Speaking Part 3 voi follow-up question.
4. Spaced repetition nang cao.
5. Study plan ca nhan hoa.
6. Mini mock test IELTS.
7. Pronunciation scoring bang speech assessment API.

## 10. Ket luan

He thong nen duoc thiet ke theo huong database luu du lieu goc va AI tao noi dung luyen tap tu du lieu do. Cach tiep can nay giup:

- Giam cong suc nhap thu cong cau hoi.
- De ca nhan hoa theo level va band muc tieu.
- Co the truy vet nguon cau hoi qua passage/transcript/rubric.
- Phu hop voi do an tot nghiep co trong tam ve ung dung AI.

Pham vi hop ly cho do an la tap trung vao MVP, trong do diem noi bat la:

- AI tao cau hoi Reading/Listening tu database.
- AI cham Writing/Speaking theo IELTS rubric.
- Tu vung va ngu phap duoc lien ket voi loi sai ca nhan.
- Dashboard goi y bai hoc tiep theo theo trinh do mong muon.
