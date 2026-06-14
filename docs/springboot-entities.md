# Tai lieu code Entity Spring Boot - IELTS AI Learning Platform

## 1. Muc tieu

Tai lieu nay mo ta cach map database PostgreSQL sang Spring Boot Entity/JPA cho he thong hoc tieng Anh va luyen thi IELTS bang AI.

Thiet ke entity dua tren database da ra soat:

- Dung UUID lam khoa chinh.
- Dung PostgreSQL `jsonb` cho du lieu linh hoat.
- Dung enum dang string de de doc va de migration.
- Dung quan he JPA ro rang, tranh foreign key gia.
- `ai_grading_results` dung 2 FK that: `writing_submission_id` va `speaking_turn_id`.
- File audio/image chi luu URL, khong luu binary trong database.

## 2. Dependency goi y

Neu dung Spring Boot 3.x va Hibernate 6.x:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

Neu khong muon dung Lombok, co the tu generate getter/setter/constructor.

## 3. Quy uoc package

```text
com.example.ielts.domain.entity
com.example.ielts.domain.enums
com.example.ielts.repository
```

## 4. Base entity

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.Id;
import jakarta.persistence.MappedSuperclass;
import jakarta.persistence.PrePersist;
import jakarta.persistence.PreUpdate;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.UuidGenerator;

import java.time.Instant;
import java.util.UUID;

@Getter
@Setter
@MappedSuperclass
public abstract class BaseEntity {

    @Id
    @GeneratedValue
    @UuidGenerator
    @Column(name = "id", nullable = false, updatable = false)
    private UUID id;

    @Column(name = "created_at", nullable = false, updatable = false)
    private Instant createdAt;

    @Column(name = "updated_at", nullable = false)
    private Instant updatedAt;

    @PrePersist
    protected void onCreate() {
        Instant now = Instant.now();
        this.createdAt = now;
        this.updatedAt = now;
    }

    @PreUpdate
    protected void onUpdate() {
        this.updatedAt = Instant.now();
    }
}
```

Voi bang chi co `created_at`, co the van ke thua `BaseEntity` neu migration co `updated_at`, hoac tao `CreatedOnlyEntity` rieng. De don gian cho backend, nen them `updated_at` cho cac bang noi dung chinh.

## 5. Enums

```java
package com.example.ielts.domain.enums;

public enum UserRole {
    LEARNER,
    ADMIN
}
```

```java
package com.example.ielts.domain.enums;

public enum SkillType {
    READING,
    LISTENING,
    WRITING,
    SPEAKING,
    VOCABULARY,
    GRAMMAR
}
```

```java
package com.example.ielts.domain.enums;

public enum ContentStatus {
    DRAFT,
    REVIEWED,
    PUBLISHED,
    REJECTED,
    ARCHIVED
}
```

```java
package com.example.ielts.domain.enums;

public enum SourceType {
    SELF_CREATED,
    AI_CREATED,
    OPEN_DATASET,
    USER_GENERATED
}
```

```java
package com.example.ielts.domain.enums;

public enum GenerationStatus {
    PENDING,
    PROCESSING,
    COMPLETED,
    FAILED
}
```

```java
package com.example.ielts.domain.enums;

public enum WritingTaskType {
    TASK_1,
    TASK_2
}
```

```java
package com.example.ielts.domain.enums;

public enum SpeakingPart {
    PART_1,
    PART_2,
    PART_3
}
```

```java
package com.example.ielts.domain.enums;

public enum SubmissionType {
    WRITING,
    SPEAKING
}
```

```java
package com.example.ielts.domain.enums;

public enum MemoryStatus {
    NEW,
    LEARNING,
    REVIEWING,
    MASTERED
}
```

Luu y: PostgreSQL enum trong DBML dang lower-case. Khi dung `@Enumerated(EnumType.STRING)`, Hibernate mac dinh ghi `LEARNER`, `READING`,... Neu muon map dung PostgreSQL enum lower-case, nen can converter rieng hoac dung varchar thay vi PostgreSQL enum. Cach don gian cho MVP:

- Database dung `varchar` + check constraint, hoac
- PostgreSQL enum dung upper-case de khop Java enum.

## 6. JSONB mapping helper

Dung Hibernate 6:

```java
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.util.Map;

@JdbcTypeCode(SqlTypes.JSON)
@Column(name = "options", columnDefinition = "jsonb")
private Map<String, Object> options;
```

Co the dung:

- `Map<String, Object>` cho object JSON linh hoat.
- `List<String>` cho array don gian.
- DTO class rieng neu schema on dinh.

## 7. Core entities

### 7.1. User

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.UserRole;
import jakarta.persistence.CascadeType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.OneToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
@Table(name = "users")
public class User extends BaseEntity {

    @Column(name = "full_name", nullable = false, length = 255)
    private String fullName;

    @Column(name = "email", nullable = false, unique = true, length = 255)
    private String email;

    @Column(name = "password_hash", nullable = false)
    private String passwordHash;

    @Enumerated(EnumType.STRING)
    @Column(name = "role", nullable = false, length = 30)
    private UserRole role = UserRole.LEARNER;

    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private LearnerProfile learnerProfile;
}
```

### 7.2. LearnerProfile

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.OneToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.math.BigDecimal;
import java.util.List;

@Getter
@Setter
@Entity
@Table(name = "learner_profiles")
public class LearnerProfile extends BaseEntity {

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false, unique = true)
    private User user;

    @Column(name = "current_band", precision = 3, scale = 1)
    private BigDecimal currentBand;

    @Column(name = "target_band", precision = 3, scale = 1)
    private BigDecimal targetBand;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "weak_skills", columnDefinition = "jsonb")
    private List<String> weakSkills;

    @Column(name = "daily_study_minutes")
    private Integer dailyStudyMinutes;

    @Column(name = "placement_completed", nullable = false)
    private Boolean placementCompleted = false;
}
```

### 7.3. Topic

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
@Table(name = "topics")
public class Topic extends BaseEntity {

    @Column(name = "name", nullable = false, unique = true, length = 100)
    private String name;

    @Column(name = "description")
    private String description;
}
```

### 7.4. Level

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;

@Getter
@Setter
@Entity
@Table(name = "levels")
public class Level extends BaseEntity {

    @Column(name = "cefr_level", nullable = false, length = 10)
    private String cefrLevel;

    @Column(name = "ielts_band_min", nullable = false, precision = 3, scale = 1)
    private BigDecimal ieltsBandMin;

    @Column(name = "ielts_band_max", nullable = false, precision = 3, scale = 1)
    private BigDecimal ieltsBandMax;

    @Column(name = "description")
    private String description;
}
```

## 8. Source data entities

### 8.1. SourceMaterial

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import com.example.ielts.domain.enums.SkillType;
import com.example.ielts.domain.enums.SourceType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
@Table(name = "source_materials")
public class SourceMaterial extends BaseEntity {

    @Enumerated(EnumType.STRING)
    @Column(name = "skill", nullable = false, length = 30)
    private SkillType skill;

    @Column(name = "title", nullable = false, length = 255)
    private String title;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "topic_id")
    private Topic topic;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "level_id")
    private Level level;

    @Column(name = "content_text")
    private String contentText;

    @Column(name = "media_url")
    private String mediaUrl;

    @Column(name = "transcript")
    private String transcript;

    @Enumerated(EnumType.STRING)
    @Column(name = "source_type", nullable = false, length = 50)
    private SourceType sourceType = SourceType.SELF_CREATED;

    @Column(name = "license_info")
    private String licenseInfo;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "created_by")
    private User createdBy;
}
```

### 8.2. ReadingPassage

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.OneToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;

@Getter
@Setter
@Entity
@Table(name = "reading_passages")
public class ReadingPassage extends BaseEntity {

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "source_material_id", unique = true)
    private SourceMaterial sourceMaterial;

    @Column(name = "title", nullable = false, length = 255)
    private String title;

    @Column(name = "passage_text", nullable = false)
    private String passageText;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "topic_id")
    private Topic topic;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "level_id")
    private Level level;

    @Column(name = "estimated_band", precision = 3, scale = 1)
    private BigDecimal estimatedBand;

    @Column(name = "word_count")
    private Integer wordCount;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;
}
```

### 8.3. ListeningMaterial va TranscriptSegment

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import jakarta.persistence.CascadeType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.OneToMany;
import jakarta.persistence.OneToOne;
import jakarta.persistence.OrderBy;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.util.ArrayList;
import java.util.List;

@Getter
@Setter
@Entity
@Table(name = "listening_materials")
public class ListeningMaterial extends BaseEntity {

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "source_material_id", unique = true)
    private SourceMaterial sourceMaterial;

    @Column(name = "title", nullable = false, length = 255)
    private String title;

    @Column(name = "audio_url", nullable = false)
    private String audioUrl;

    @Column(name = "transcript", nullable = false)
    private String transcript;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "topic_id")
    private Topic topic;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "level_id")
    private Level level;

    @Column(name = "accent", length = 100)
    private String accent;

    @Column(name = "duration_seconds")
    private Integer durationSeconds;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;

    @OneToMany(mappedBy = "listeningMaterial", cascade = CascadeType.ALL, orphanRemoval = true)
    @OrderBy("orderIndex ASC")
    private List<TranscriptSegment> segments = new ArrayList<>();
}
```

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;

@Getter
@Setter
@Entity
@Table(name = "transcript_segments")
public class TranscriptSegment extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "listening_material_id", nullable = false)
    private ListeningMaterial listeningMaterial;

    @Column(name = "start_time", nullable = false, precision = 8, scale = 2)
    private BigDecimal startTime;

    @Column(name = "end_time", nullable = false, precision = 8, scale = 2)
    private BigDecimal endTime;

    @Column(name = "speaker", length = 100)
    private String speaker;

    @Column(name = "segment_text", nullable = false)
    private String segmentText;

    @Column(name = "order_index", nullable = false)
    private Integer orderIndex;
}
```

## 9. AI-generated exercise entities

### 9.1. PromptTemplate va AiGenerationRequest

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.util.Map;

@Getter
@Setter
@Entity
@Table(name = "prompt_templates")
public class PromptTemplate extends BaseEntity {

    @Column(name = "name", nullable = false, length = 255)
    private String name;

    @Column(name = "task_type", nullable = false, length = 100)
    private String taskType;

    @Column(name = "template_text", nullable = false)
    private String templateText;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "output_schema", columnDefinition = "jsonb")
    private Map<String, Object> outputSchema;

    @Column(name = "version", nullable = false, length = 50)
    private String version;

    @Column(name = "is_active", nullable = false)
    private Boolean active = true;
}
```

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.GenerationStatus;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.util.Map;

@Getter
@Setter
@Entity
@Table(name = "ai_generation_requests")
public class AiGenerationRequest extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "source_material_id")
    private SourceMaterial sourceMaterial;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "requested_by")
    private User requestedBy;

    @Column(name = "generation_type", nullable = false, length = 100)
    private String generationType;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "input_parameters", columnDefinition = "jsonb")
    private Map<String, Object> inputParameters;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "prompt_template_id")
    private PromptTemplate promptTemplate;

    @Column(name = "model_name", length = 100)
    private String modelName;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "raw_response", columnDefinition = "jsonb")
    private Map<String, Object> rawResponse;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private GenerationStatus status = GenerationStatus.PENDING;

    @Column(name = "error_message")
    private String errorMessage;
}
```

### 9.2. GeneratedExercise va GeneratedQuestion

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import com.example.ielts.domain.enums.SkillType;
import jakarta.persistence.CascadeType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.OneToMany;
import jakarta.persistence.OrderBy;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.util.ArrayList;
import java.util.List;

@Getter
@Setter
@Entity
@Table(name = "generated_exercises")
public class GeneratedExercise extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "generation_request_id")
    private AiGenerationRequest generationRequest;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "source_material_id")
    private SourceMaterial sourceMaterial;

    @Enumerated(EnumType.STRING)
    @Column(name = "skill", nullable = false, length = 30)
    private SkillType skill;

    @Column(name = "title", nullable = false, length = 255)
    private String title;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "topic_id")
    private Topic topic;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "level_id")
    private Level level;

    @Column(name = "question_type", length = 100)
    private String questionType;

    @Column(name = "estimated_time_minutes")
    private Integer estimatedTimeMinutes;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;

    @OneToMany(mappedBy = "exercise", cascade = CascadeType.ALL, orphanRemoval = true)
    @OrderBy("orderIndex ASC")
    private List<GeneratedQuestion> questions = new ArrayList<>();
}
```

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.math.BigDecimal;
import java.util.Map;

@Getter
@Setter
@Entity
@Table(name = "generated_questions")
public class GeneratedQuestion extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "exercise_id", nullable = false)
    private GeneratedExercise exercise;

    @Column(name = "question_text", nullable = false)
    private String questionText;

    @Column(name = "question_type", nullable = false, length = 100)
    private String questionType;

    @Column(name = "order_index", nullable = false)
    private Integer orderIndex;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "options", columnDefinition = "jsonb")
    private Map<String, Object> options;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "correct_answer", nullable = false, columnDefinition = "jsonb")
    private Map<String, Object> correctAnswer;

    @Column(name = "explanation")
    private String explanation;

    @Column(name = "evidence_text")
    private String evidenceText;

    @Column(name = "evidence_location", length = 255)
    private String evidenceLocation;

    @Column(name = "timestamp_start", precision = 8, scale = 2)
    private BigDecimal timestampStart;

    @Column(name = "timestamp_end", precision = 8, scale = 2)
    private BigDecimal timestampEnd;

    @Column(name = "difficulty", length = 50)
    private String difficulty;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;
}
```

## 10. Practice entities

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.SkillType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;
import java.time.Instant;

@Getter
@Setter
@Entity
@Table(name = "practice_attempts")
public class PracticeAttempt extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "exercise_id", nullable = false)
    private GeneratedExercise exercise;

    @Enumerated(EnumType.STRING)
    @Column(name = "skill", nullable = false, length = 30)
    private SkillType skill;

    @Column(name = "started_at", nullable = false)
    private Instant startedAt = Instant.now();

    @Column(name = "submitted_at")
    private Instant submittedAt;

    @Column(name = "score", precision = 6, scale = 2)
    private BigDecimal score;

    @Column(name = "max_score", precision = 6, scale = 2)
    private BigDecimal maxScore;

    @Column(name = "estimated_band", precision = 3, scale = 1)
    private BigDecimal estimatedBand;

    @Column(name = "time_spent_seconds")
    private Integer timeSpentSeconds;
}
```

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.math.BigDecimal;
import java.util.Map;

@Getter
@Setter
@Entity
@Table(name = "user_answers")
public class UserAnswer extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "attempt_id", nullable = false)
    private PracticeAttempt attempt;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "question_id", nullable = false)
    private GeneratedQuestion question;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "answer", nullable = false, columnDefinition = "jsonb")
    private Map<String, Object> answer;

    @Column(name = "is_correct")
    private Boolean correct;

    @Column(name = "score", precision = 6, scale = 2)
    private BigDecimal score;

    @Column(name = "feedback")
    private String feedback;
}
```

## 11. Writing entities

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import com.example.ielts.domain.enums.WritingTaskType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.OneToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
@Table(name = "writing_prompts")
public class WritingPrompt extends BaseEntity {

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "source_material_id", unique = true)
    private SourceMaterial sourceMaterial;

    @Enumerated(EnumType.STRING)
    @Column(name = "task_type", nullable = false, length = 30)
    private WritingTaskType taskType;

    @Column(name = "prompt_text", nullable = false)
    private String promptText;

    @Column(name = "chart_image_url")
    private String chartImageUrl;

    @Column(name = "essay_type", length = 100)
    private String essayType;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "topic_id")
    private Topic topic;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "level_id")
    private Level level;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;
}
```

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.time.Instant;

@Getter
@Setter
@Entity
@Table(name = "writing_submissions")
public class WritingSubmission extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "writing_prompt_id", nullable = false)
    private WritingPrompt writingPrompt;

    @Column(name = "essay_text", nullable = false)
    private String essayText;

    @Column(name = "word_count")
    private Integer wordCount;

    @Column(name = "submitted_at", nullable = false)
    private Instant submittedAt = Instant.now();
}
```

## 12. Speaking entities

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import com.example.ielts.domain.enums.SpeakingPart;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.OneToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.util.List;

@Getter
@Setter
@Entity
@Table(name = "speaking_prompts")
public class SpeakingPrompt extends BaseEntity {

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "source_material_id", unique = true)
    private SourceMaterial sourceMaterial;

    @Enumerated(EnumType.STRING)
    @Column(name = "part", nullable = false, length = 30)
    private SpeakingPart part;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "topic_id")
    private Topic topic;

    @Column(name = "prompt_text", nullable = false)
    private String promptText;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "cue_points", columnDefinition = "jsonb")
    private List<String> cuePoints;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "level_id")
    private Level level;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;
}
```

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.SpeakingPart;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;
import java.time.Instant;

@Getter
@Setter
@Entity
@Table(name = "speaking_sessions")
public class SpeakingSession extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "topic_id")
    private Topic topic;

    @Enumerated(EnumType.STRING)
    @Column(name = "part", nullable = false, length = 30)
    private SpeakingPart part;

    @Column(name = "started_at", nullable = false)
    private Instant startedAt = Instant.now();

    @Column(name = "ended_at")
    private Instant endedAt;

    @Column(name = "overall_band", precision = 3, scale = 1)
    private BigDecimal overallBand;
}
```

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
@Table(name = "speaking_turns")
public class SpeakingTurn extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "session_id", nullable = false)
    private SpeakingSession session;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "speaking_prompt_id")
    private SpeakingPrompt speakingPrompt;

    @Column(name = "question_text", nullable = false)
    private String questionText;

    @Column(name = "answer_text")
    private String answerText;

    @Column(name = "audio_url")
    private String audioUrl;

    @Column(name = "transcript")
    private String transcript;

    @Column(name = "duration_seconds")
    private Integer durationSeconds;

    @Column(name = "order_index", nullable = false)
    private Integer orderIndex;

    @Column(name = "is_ai_follow_up", nullable = false)
    private Boolean aiFollowUp = false;
}
```

## 13. Rubric va AI grading entities

### 13.1. Rubric

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.SkillType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;

@Getter
@Setter
@Entity
@Table(name = "rubrics")
public class Rubric extends BaseEntity {

    @Enumerated(EnumType.STRING)
    @Column(name = "skill", nullable = false, length = 30)
    private SkillType skill;

    @Column(name = "task_type", length = 100)
    private String taskType;

    @Column(name = "criterion", nullable = false, length = 100)
    private String criterion;

    @Column(name = "band_score", nullable = false, precision = 3, scale = 1)
    private BigDecimal bandScore;

    @Column(name = "descriptor_text", nullable = false)
    private String descriptorText;

    @Column(name = "version", length = 50)
    private String version;

    @Column(name = "source_reference")
    private String sourceReference;
}
```

### 13.2. AiGradingResult

Day la entity quan trong. Khong dung `submissionId` polymorphic. Dung 2 quan he ro rang:

- Writing: `writingSubmission` co gia tri, `speakingTurn` null.
- Speaking: `speakingTurn` co gia tri, `writingSubmission` null.

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.SubmissionType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.math.BigDecimal;
import java.util.Map;

@Getter
@Setter
@Entity
@Table(name = "ai_grading_results")
public class AiGradingResult extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @Enumerated(EnumType.STRING)
    @Column(name = "submission_type", nullable = false, length = 30)
    private SubmissionType submissionType;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "writing_submission_id")
    private WritingSubmission writingSubmission;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "speaking_turn_id")
    private SpeakingTurn speakingTurn;

    @Column(name = "overall_band", precision = 3, scale = 1)
    private BigDecimal overallBand;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "criterion_scores", columnDefinition = "jsonb")
    private Map<String, Object> criterionScores;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "strengths", columnDefinition = "jsonb")
    private Map<String, Object> strengths;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "weaknesses", columnDefinition = "jsonb")
    private Map<String, Object> weaknesses;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "grammar_errors", columnDefinition = "jsonb")
    private Map<String, Object> grammarErrors;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "vocabulary_suggestions", columnDefinition = "jsonb")
    private Map<String, Object> vocabularySuggestions;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "pronunciation_metrics", columnDefinition = "jsonb")
    private Map<String, Object> pronunciationMetrics;

    @Column(name = "feedback_text")
    private String feedbackText;

    @Column(name = "improved_answer_text")
    private String improvedAnswerText;

    @Column(name = "model_name", length = 100)
    private String modelName;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "prompt_template_id")
    private PromptTemplate promptTemplate;
}
```

Check constraint nen dat trong migration:

```sql
ALTER TABLE ai_grading_results
ADD CONSTRAINT ai_grading_one_submission_check
CHECK (
    (submission_type = 'WRITING' AND writing_submission_id IS NOT NULL AND speaking_turn_id IS NULL)
    OR
    (submission_type = 'SPEAKING' AND speaking_turn_id IS NOT NULL AND writing_submission_id IS NULL)
);
```

Neu database enum dung lower-case, thay `'WRITING'`, `'SPEAKING'` bang `'writing'`, `'speaking'`.

## 14. Vocabulary entities

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.util.List;

@Getter
@Setter
@Entity
@Table(name = "vocabulary_items")
public class VocabularyItem extends BaseEntity {

    @Column(name = "word", nullable = false, length = 100)
    private String word;

    @Column(name = "ipa", length = 100)
    private String ipa;

    @Column(name = "part_of_speech", length = 50)
    private String partOfSpeech;

    @Column(name = "meaning_vi")
    private String meaningVi;

    @Column(name = "definition_en")
    private String definitionEn;

    @Column(name = "example_sentence")
    private String exampleSentence;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "collocations", columnDefinition = "jsonb")
    private List<String> collocations;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "synonyms", columnDefinition = "jsonb")
    private List<String> synonyms;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "antonyms", columnDefinition = "jsonb")
    private List<String> antonyms;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "topic_id")
    private Topic topic;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "level_id")
    private Level level;

    @Column(name = "audio_url")
    private String audioUrl;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;
}
```

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.MemoryStatus;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;
import java.time.Instant;

@Getter
@Setter
@Entity
@Table(name = "user_vocabulary_progress")
public class UserVocabularyProgress extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "vocabulary_item_id", nullable = false)
    private VocabularyItem vocabularyItem;

    @Enumerated(EnumType.STRING)
    @Column(name = "memory_status", nullable = false, length = 30)
    private MemoryStatus memoryStatus = MemoryStatus.NEW;

    @Column(name = "ease_score", precision = 4, scale = 2)
    private BigDecimal easeScore;

    @Column(name = "next_review_at")
    private Instant nextReviewAt;

    @Column(name = "review_count", nullable = false)
    private Integer reviewCount = 0;

    @Column(name = "last_reviewed_at")
    private Instant lastReviewedAt;
}
```

## 15. Grammar entities

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.util.Map;

@Getter
@Setter
@Entity
@Table(name = "grammar_topics")
public class GrammarTopic extends BaseEntity {

    @Column(name = "title", nullable = false, length = 255)
    private String title;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "level_id")
    private Level level;

    @Column(name = "explanation")
    private String explanation;

    @Column(name = "formula")
    private String formula;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "examples", columnDefinition = "jsonb")
    private Map<String, Object> examples;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "common_mistakes", columnDefinition = "jsonb")
    private Map<String, Object> commonMistakes;

    @Column(name = "ielts_application")
    private String ieltsApplication;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;
}
```

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.util.Map;

@Getter
@Setter
@Entity
@Table(name = "grammar_exercises")
public class GrammarExercise extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "grammar_topic_id", nullable = false)
    private GrammarTopic grammarTopic;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "generated_exercise_id")
    private GeneratedExercise generatedExercise;

    @Column(name = "exercise_type", nullable = false, length = 100)
    private String exerciseType;

    @Column(name = "question_text", nullable = false)
    private String questionText;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "options", columnDefinition = "jsonb")
    private Map<String, Object> options;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "correct_answer", nullable = false, columnDefinition = "jsonb")
    private Map<String, Object> correctAnswer;

    @Column(name = "explanation")
    private String explanation;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;
}
```

```java
package com.example.ielts.domain.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;
import java.time.Instant;

@Getter
@Setter
@Entity
@Table(name = "user_grammar_progress")
public class UserGrammarProgress extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "grammar_topic_id", nullable = false)
    private GrammarTopic grammarTopic;

    @Column(name = "mastery_level", precision = 4, scale = 2)
    private BigDecimal masteryLevel;

    @Column(name = "correct_count", nullable = false)
    private Integer correctCount = 0;

    @Column(name = "incorrect_count", nullable = false)
    private Integer incorrectCount = 0;

    @Column(name = "last_practiced_at")
    private Instant lastPracticedAt;
}
```

## 16. Recommendation entity

```java
package com.example.ielts.domain.entity;

import com.example.ielts.domain.enums.ContentStatus;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.FetchType;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.util.Map;

@Getter
@Setter
@Entity
@Table(name = "learning_recommendations")
public class LearningRecommendation extends BaseEntity {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @Column(name = "recommendation_type", nullable = false, length = 100)
    private String recommendationType;

    @Column(name = "recommendation_text", nullable = false)
    private String recommendationText;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "recommended_items", columnDefinition = "jsonb")
    private Map<String, Object> recommendedItems;

    @Column(name = "reason")
    private String reason;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 30)
    private ContentStatus status = ContentStatus.DRAFT;
}
```

## 17. Repository examples

```java
package com.example.ielts.repository;

import com.example.ielts.domain.entity.GeneratedExercise;
import com.example.ielts.domain.enums.ContentStatus;
import com.example.ielts.domain.enums.SkillType;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;
import java.util.UUID;

public interface GeneratedExerciseRepository extends JpaRepository<GeneratedExercise, UUID> {
    List<GeneratedExercise> findBySkillAndStatus(SkillType skill, ContentStatus status);
}
```

```java
package com.example.ielts.repository;

import com.example.ielts.domain.entity.UserVocabularyProgress;
import org.springframework.data.jpa.repository.JpaRepository;

import java.time.Instant;
import java.util.List;
import java.util.UUID;

public interface UserVocabularyProgressRepository extends JpaRepository<UserVocabularyProgress, UUID> {
    List<UserVocabularyProgress> findByUserIdAndNextReviewAtLessThanEqual(UUID userId, Instant now);
}
```

```java
package com.example.ielts.repository;

import com.example.ielts.domain.entity.AiGradingResult;
import com.example.ielts.domain.enums.SubmissionType;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;
import java.util.UUID;

public interface AiGradingResultRepository extends JpaRepository<AiGradingResult, UUID> {
    List<AiGradingResult> findByUserIdAndSubmissionTypeOrderByCreatedAtDesc(UUID userId, SubmissionType submissionType);
}
```

## 18. Cac luu y khi implement

### 18.1. Fetch strategy

Nen dung `FetchType.LAZY` cho hau het quan he:

- `@ManyToOne(fetch = FetchType.LAZY)`
- `@OneToOne(fetch = FetchType.LAZY)`

DTO/API response nen duoc build o service layer, tranh tra entity truc tiep ra controller.

### 18.2. Cascade

Chi dung cascade khi entity con phu thuoc vong doi vao entity cha:

- `ListeningMaterial` -> `TranscriptSegment`
- `GeneratedExercise` -> `GeneratedQuestion`
- `User` -> `LearnerProfile`

Khong nen cascade tu `User` sang submissions/attempts trong code neu co nguy co xoa nham du lieu lich su.

### 18.3. JSONB

Nen dung JSONB cho:

- Options/correct answers.
- Rubric score details.
- Grammar errors.
- Vocabulary suggestions.
- Prompt output schema.
- Recommendation items.

Khong nen dung JSONB cho du lieu can query/filter thuong xuyen nhu:

- skill
- topic
- level
- status
- user id
- created_at

### 18.4. Enum mapping

Chon mot trong hai huong:

1. Database enum upper-case de khop Java enum.
2. Dung `varchar` + check constraint de de lam voi JPA.

Neu database enum lower-case, can viet `AttributeConverter`.

### 18.5. AiGradingResult relationship

Thiet ke da ra soat khong dung `submission_id` chung vi:

- Khong tao duoc FK that.
- Kho map JPA.
- De bi tham chieu nham bang.

Thay vao do:

- `writing_submission_id` FK -> `writing_submissions.id`
- `speaking_turn_id` FK -> `speaking_turns.id`
- Check constraint dam bao dung loai.

### 18.6. SourceMaterial relationship

`SourceMaterial` giup truy vet nguon noi dung. Cac bang sau co the lien ket 1-1 toi source:

- `ReadingPassage`
- `ListeningMaterial`
- `WritingPrompt`
- `SpeakingPrompt`

Neu mot prompt duoc admin nhap truc tiep va khong can source rieng, `source_material_id` co the null.

## 19. Ket luan

Bo entity tren phu hop cho phien ban MVP cua he thong:

- Reading/Listening dung chung `GeneratedExercise` va `GeneratedQuestion`.
- Writing/Speaking co submission/session rieng.
- AI grading co quan he FK ro rang.
- Vocabulary/Grammar co bang progress rieng cho ca nhan hoa.
- JSONB chi dung cho cac truong linh hoat.

Khi bat dau code that, nen tao migration Flyway/Liquibase truoc, sau do tao entity khop voi migration thay vi de Hibernate auto generate schema trong moi truong production.
