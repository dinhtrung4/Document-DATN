# Speaking Pronunciation Assessment Pipeline

## 1. Muc tieu

Tai lieu nay mo ta pipeline danh gia pronunciation cho IELTS Speaking theo huong chinh xac nhat co the trong pham vi do an.

Nguyen tac quan trong:

- Khong de LLM tu cham pronunciation tu transcript.
- Dung audio processing, ASR, G2P, forced alignment va phoneme recognition de tao metrics.
- Scoring engine tinh diem dua tren metrics.
- LLM chi dung de giai thich feedback cho learner theo ngon ngu tu nhien.

Pipeline chuan:

```text
Audio
-> Preprocessing
-> WhisperX
-> Transcript + Word Timestamps
-> Text Normalization
-> G2P
-> Expected Phonemes

Audio + Transcript
-> Forced Alignment
-> Phoneme/Syllable Boundaries

Audio
-> Phoneme Recognizer
-> Actual Phoneme Probabilities

Alignment + Probabilities
-> Feature Extraction
   - Phoneme Error Rate
   - Substitution / Deletion / Insertion
   - Speech Rate
   - Pause Analysis
   - Pitch Features
   - Energy Features
   - Stress Features

Feature Extraction
-> Scoring Engine
   - Accuracy Score
   - Fluency Score
   - Prosody Score
   - Completeness Score

Scoring Engine
-> LLM Feedback Generator
```

## 2. Danh gia pipeline

Pipeline nay tot hon cac cach don gian:

```text
Audio -> transcript -> LLM cham pronunciation
```

Ly do:

- Co actual audio evidence.
- Co expected phoneme sequence.
- Co actual phoneme probabilities.
- Co forced alignment de biet loi nam o word/phoneme nao.
- Co feature extraction rieng cho accuracy, fluency, prosody, completeness.
- LLM khong cham diem truc tiep tu text, ma chi dien giai metrics.

Day la huong phu hop neu muon phan Speaking co chieu sau ky thuat.

## 3. Cong nghe de xuat cho tung buoc

| Buoc | Cong nghe/model/thu vien | Vai tro |
| --- | --- | --- |
| Audio preprocessing | ffmpeg, pydub, librosa | Convert wav, mono, 16kHz, trim silence, normalize volume |
| ASR + timestamps | WhisperX | Transcript va word-level timestamps |
| Text normalization | Python rules, regex, num2words | Chuan hoa text truoc G2P |
| G2P | CMUdict, g2p-en, phonemizer, espeak-ng | Tao expected phonemes |
| Forced alignment | WhisperX alignment, Montreal Forced Aligner | Tao word/phoneme/syllable boundaries |
| Phoneme recognizer | Allosaurus, wav2vec2 phoneme model, NeMo | Tao actual phoneme probabilities |
| Feature extraction | librosa, praat-parselmouth, numpy/scipy | Pitch, energy, pause, stress features |
| Scoring engine | Python rule-based/statistical scoring | Accuracy, fluency, prosody, completeness |
| Feedback generator | LLM API/local LLM | Giai thich loi va goi y luyen tap |

## 4. Step 1 - Audio preprocessing

## 4.1. Muc tieu

Chuan hoa audio de cac model sau xu ly on dinh.

Input:

```text
webm/mp3/wav from frontend
```

Output:

```text
wav, mono, 16kHz
```

## 4.2. Xu ly

Nen lam:

- Convert to wav.
- Mono channel.
- 16kHz sample rate.
- Normalize volume.
- Trim leading/trailing silence optional.
- Detect duration.

Cong cu:

```text
ffmpeg
pydub
librosa
```

Example:

```bash
ffmpeg -i input.webm -ar 16000 -ac 1 output.wav
```

Metrics luu lai:

```json
{
  "duration_seconds": 82.4,
  "sample_rate": 16000,
  "channels": 1
}
```

## 5. Step 2 - WhisperX transcript + word timestamps

## 5.1. Muc tieu

Lay:

- Transcript.
- Segment timestamps.
- Word timestamps.

Output:

```json
{
  "transcript": "I think technology is important.",
  "words": [
    {"word": "I", "start": 0.10, "end": 0.20},
    {"word": "think", "start": 0.31, "end": 0.68},
    {"word": "technology", "start": 0.82, "end": 1.52}
  ]
}
```

## 5.2. Ly do chon WhisperX

WhisperX tot hon Whisper thuong cho pipeline nay vi:

- Co word-level timestamps.
- Co alignment pipeline.
- Co the ket hop speaker diarization neu can.

## 5.3. Luu y

- WhisperX transcript co the sua loi phat am thanh tu dung, nen khong du de cham pronunciation.
- Transcript chi la co so de tao expected phonemes va alignment.

## 6. Step 3 - Text normalization

## 6.1. Muc tieu

Chuan hoa transcript truoc khi G2P.

Xu ly:

- Lowercase.
- Remove punctuation.
- Expand contractions.
- Convert numbers to words.
- Handle filler words.
- Remove duplicated ASR artifacts.

Example:

```text
"I think technology's important, um, in 2026."
-> "i think technology is important um in twenty twenty six"
```

## 6.2. Cong cu

- Python regex.
- `num2words`.
- Custom normalization rules.

## 7. Step 4 - G2P expected phonemes

## 7.1. Muc tieu

Tao expected phoneme sequence tu normalized transcript.

Input:

```text
i think technology is important
```

Output ARPABET example:

```text
AY TH IH NG K T EH K N AA L AH JH IY IH Z IH M P AO R T AH N T
```

## 7.2. Cong cu

Khuyen nghi:

```text
CMUdict first
g2p-en fallback
phonemizer/espeak-ng optional
```

## 7.3. Phone set

Nen chon mot phone set duy nhat.

Khuyen nghi MVP:

```text
ARPABET
```

Ly do:

- De xu ly trong code.
- CMUdict/g2p-en ho tro tot.
- De so sanh/edit distance hon IPA.

Neu phoneme recognizer tra phone set khac, can mapping ve phone set chung.

## 8. Step 5 - Forced alignment

## 8.1. Muc tieu

Can alignment de biet word/phoneme nao nam o thoi diem nao trong audio.

Input:

```text
audio + transcript or expected phonemes
```

Output:

```json
{
  "word": "think",
  "start": 0.31,
  "end": 0.68,
  "phonemes": [
    {"phoneme": "TH", "start": 0.31, "end": 0.39},
    {"phoneme": "IH", "start": 0.39, "end": 0.47},
    {"phoneme": "NG", "start": 0.47, "end": 0.58},
    {"phoneme": "K", "start": 0.58, "end": 0.68}
  ]
}
```

## 8.2. Lua chon cong cu

### MVP

```text
WhisperX word alignment
```

Sau do uoc luong phoneme boundaries trong word.

Uu diem:

- De tich hop.
- Dung chung voi ASR.
- Du de demo word-level/rough phoneme analysis.

Nhuoc diem:

- Phoneme boundary khong chinh xac cao.

### Advanced

```text
Montreal Forced Aligner
```

Uu diem:

- Co phoneme-level alignment tot hon.
- Phu hop pronunciation analysis hon.

Nhuoc diem:

- Setup phuc tap.
- Can dictionary/acoustic model.

## 9. Step 6 - Phoneme recognizer actual probabilities

## 9.1. Muc tieu

Tu audio, nhan dien actual phoneme probabilities.

Khong chi can:

```text
actual phoneme sequence
```

Ma tot hon la:

```text
phoneme probability distribution over time
```

Vi du:

```json
{
  "time": 0.35,
  "phoneme_probs": {
    "TH": 0.25,
    "T": 0.62,
    "S": 0.05
  }
}
```

Output nay giup biet learner co xu huong phat am /TH/ thanh /T/.

## 9.2. Cong cu

Lua chon:

```text
Allosaurus
wav2vec2 phoneme recognition model
NVIDIA NeMo phoneme/acoustic model
```

## 9.3. Luu y

- Output phone set co the khac expected phonemes.
- Can mapping phone set.
- Actual phoneme recognition se co nhieu noise voi learner accent.
- Khong nen dua raw actual sequence cho learner; nen aggregate thanh metrics.

## 10. Step 7 - Feature extraction

## 10.1. Input

- Expected phonemes.
- Actual phoneme probabilities.
- Alignment boundaries.
- Audio waveform.
- Transcript.
- Word timestamps.

## 10.2. Feature groups

### Phoneme error features

```text
phoneme_error_rate
substitution_count
deletion_count
insertion_count
weak_phoneme_count
problem_phonemes
problem_words
```

### Fluency features

```text
duration_seconds
speech_duration_seconds
silence_duration_seconds
words_per_minute
pause_count
long_pause_count
average_pause_seconds
silence_ratio
filler_word_count
```

### Prosody features

```text
pitch_mean
pitch_std
pitch_range
energy_mean
energy_std
stress_pattern_score
rhythm_stability
intonation_variation
```

### Completeness features

```text
expected_word_count
recognized_word_count
missing_word_count
completion_ratio
```

## 10.3. Cong cu

- `librosa`: pitch/energy/signal features.
- `praat-parselmouth`: pitch, intensity, prosody features.
- `pydub`: silence detection.
- `numpy/scipy`: aggregation.
- `rapidfuzz` or edit distance: compare expected vs actual sequences.

## 11. Step 8 - Expected vs Actual comparison

## 11.1. Sequence comparison

Dung dynamic programming/edit distance:

```text
Expected: TH IH NG K
Actual:   T  IH NG K
```

Ket qua:

```json
{
  "word": "think",
  "expected_phonemes": ["TH", "IH", "NG", "K"],
  "actual_phonemes": ["T", "IH", "NG", "K"],
  "errors": [
    {
      "type": "substitution",
      "expected": "TH",
      "actual": "T"
    }
  ],
  "phoneme_accuracy": 75.0
}
```

## 11.2. Probability-based scoring

Neu co phoneme probabilities, nen tinh:

```text
expected_phoneme_probability
```

Example:

```text
Expected TH at 0.31-0.39s
Average P(TH) = 0.25
Average P(T) = 0.62
=> likely substitution TH -> T
```

Cach nay tot hon chi lay actual sequence vi giam noise.

## 12. Step 9 - Scoring engine

Scoring engine nen deterministic/rule-based, khong phai LLM.

## 12.1. Accuracy Score

Du lieu dung:

- Phoneme error rate.
- Expected phoneme probability.
- Word-level problem count.
- Substitution/deletion/insertion.

Formula goi y:

```text
accuracy_score = 100 - weighted_phoneme_error_rate
```

Example:

```text
weighted_phoneme_error_rate =
  substitution_rate * 1.0
  + deletion_rate * 1.2
  + insertion_rate * 0.8
```

## 12.2. Fluency Score

Du lieu dung:

- words_per_minute.
- pause_count.
- long_pause_count.
- average_pause_seconds.
- silence_ratio.
- filler_word_count.

Formula goi y:

```text
fluency_score = base_score
  - pause_penalty
  - long_pause_penalty
  - filler_penalty
  - very_slow_or_fast_rate_penalty
```

## 12.3. Prosody Score

Du lieu dung:

- pitch variation.
- energy variation.
- rhythm stability.
- stress pattern score.
- intonation variation.

Formula goi y:

```text
prosody_score = weighted_sum(
  pitch_variation_score,
  stress_pattern_score,
  rhythm_score,
  energy_variation_score
)
```

Luu y:

- Prosody local scoring kho.
- Nen bat dau voi rough score va confidence medium/low.

## 12.4. Completeness Score

Du lieu dung:

- expected word count.
- recognized word count.
- missing word count.
- completion ratio.

Formula:

```text
completeness_score = recognized_word_count / expected_word_count * 100
```

Voi IELTS Speaking tu do, completeness khong phai "doc dung mot cau co san" nen chi nen dung khi co target phrase/reading aloud task.

Voi open-ended Speaking, completeness co the thay bang:

```text
answer_development_score
```

do LLM danh gia dua tren transcript.

## 12.5. Pronunciation overall score

Goi y:

```text
pronunciation_overall =
  accuracy_score * 0.45
  + fluency_score * 0.25
  + prosody_score * 0.20
  + completeness_score * 0.10
```

Voi open-ended IELTS Speaking:

```text
pronunciation_overall =
  accuracy_score * 0.55
  + fluency_score * 0.25
  + prosody_score * 0.20
```

Completeness nen giam hoac bo neu khong co expected script.

## 13. Step 10 - Map score sang IELTS pronunciation band

Backend/scoring engine nen map score sang band so bo.

Example:

```text
90-100 -> 8.0-9.0
80-89  -> 7.0-7.5
70-79  -> 6.0-6.5
60-69  -> 5.0-5.5
50-59  -> 4.0-4.5
<50    -> below 4.0
```

Can calibration bang sample audio de chinh threshold.

## 14. Step 11 - LLM Feedback Generator

## 14.1. LLM khong cham pronunciation tu raw text

LLM nhan:

- Transcript.
- Pronunciation metrics.
- Problem words.
- Problem phonemes.
- Fluency metrics.
- Prosody metrics.
- IELTS rubric descriptors.
- Confidence level.

LLM tra:

- Feedback de hieu.
- Loi cu the.
- Bai tap luyen tap.
- Estimated band explanation.

## 14.2. Input cho LLM

```json
{
  "transcript": "I think technology is important.",
  "pronunciation_metrics": {
    "accuracy_score": 72,
    "fluency_score": 68,
    "prosody_score": 65,
    "pronunciation_overall": 69,
    "estimated_band": 5.5,
    "confidence": "medium",
    "problem_words": [
      {
        "word": "think",
        "expected": ["TH", "IH", "NG", "K"],
        "actual": ["T", "IH", "NG", "K"],
        "issue": "TH pronounced as T"
      }
    ]
  },
  "ielts_rubric": {
    "criterion": "Pronunciation",
    "descriptors": []
  }
}
```

## 14.3. Output tu LLM

```json
{
  "pronunciation_band": 5.5,
  "confidence": "medium",
  "feedback": "Your pronunciation is generally understandable, but some individual sounds reduce clarity.",
  "specific_issues": [
    "The /TH/ sound in 'think' was closer to /T/."
  ],
  "practice_suggestions": [
    "Practice minimal pairs such as think/sink and thin/tin.",
    "Record yourself producing /TH/ words and compare with a model pronunciation."
  ]
}
```

## 15. Confidence levels

Moi result nen co confidence.

```text
high:
  phoneme-level alignment/probabilities reliable
  audio quality good
  enough speech duration

medium:
  word-level alignment + phoneme recognizer available
  audio quality acceptable

low:
  only transcript + rough audio metrics

very_low:
  text-only answer
```

## 16. Implementation phases

## Phase P0 - MVP audio metrics

```text
Audio -> WhisperX/faster-whisper -> transcript + timestamps
Audio -> pydub/librosa -> duration, silence, WPM
LLM -> feedback with limited pronunciation confidence
```

Output:

- Transcript.
- WPM.
- Pause count.
- Limited pronunciation feedback.

## Phase P1 - Expected phonemes

```text
Transcript -> normalization -> CMUdict/g2p-en -> expected phonemes
```

Output:

- Word -> expected phoneme sequence.
- Basic phoneme display.

## Phase P2 - Forced alignment

```text
Audio + transcript -> WhisperX/MFA -> word/phoneme boundaries
```

Output:

- Word/phoneme timestamps.

## Phase P3 - Actual phoneme probabilities

```text
Audio -> Allosaurus/wav2vec2 phoneme model -> actual phoneme probabilities
```

Output:

- Actual phoneme distribution.
- Problem phoneme candidates.

## Phase P4 - Pronunciation scoring engine

```text
Expected phonemes + actual probabilities + alignment
-> feature extraction
-> accuracy/fluency/prosody/completeness scores
-> estimated IELTS pronunciation band
```

Output:

- Pronunciation metrics.
- Problem words/phonemes.
- Estimated band.

## Phase P5 - LLM feedback

```text
Metrics + rubric -> LLM feedback generator
```

Output:

- Natural feedback.
- Practice suggestions.
- IELTS-style explanation.

## 17. Performance considerations

Expensive steps:

- WhisperX alignment.
- Phoneme recognizer.
- MFA.
- LLM feedback.

Optimization:

- Run pronunciation pipeline async.
- Cache result by audio hash.
- Store intermediate artifacts:
  - transcript.
  - word timestamps.
  - expected phonemes.
  - alignment.
  - pronunciation metrics.
- Allow quick feedback first, full pronunciation analysis later.
- Limit full pronunciation assessment by quota.

## 18. Data nen luu

Co the bo sung bang hoac JSONB field:

```text
speaking_turns:
  audio_url
  transcript
  duration_seconds
  stt_metadata_json

speaking_pronunciation_results:
  speaking_turn_id
  audio_hash
  expected_phonemes_json
  alignment_json
  phoneme_probabilities_summary_json
  phoneme_errors_json
  acoustic_features_json
  accuracy_score
  fluency_score
  prosody_score
  completeness_score
  pronunciation_overall_score
  estimated_pronunciation_band
  confidence
  created_at
```

Neu khong muon tao bang moi trong MVP, co the luu tam vao:

```text
ai_grading_results.pronunciation_metrics
```

Nhung ve lau dai nen tach bang `speaking_pronunciation_results`.

## 19. Ket luan

Pipeline tot nhat cho pronunciation:

```text
Audio
-> preprocessing
-> WhisperX transcript + timestamps
-> text normalization
-> G2P expected phonemes
-> forced alignment boundaries
-> phoneme recognizer probabilities
-> feature extraction
-> deterministic scoring engine
-> LLM feedback generator
```

Trong do:

- Model/audio tools tao metrics.
- Scoring engine tinh diem.
- LLM chi giai thich.
- Backend validate va luu ket qua.

Day la huong chinh xac va co chieu sau hon nhieu so voi viec de LLM cham pronunciation tu transcript.
