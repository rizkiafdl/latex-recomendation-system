<!-- Class Diagram -->
classDiagram
    direction TB

    class AppUser {
        +int id
        +String username
        +String full_name
        +String password_hash
        +DateTime created_at
    }

    class Supervisor {
        +int id
        +String code
        +String name
        +Text profile_keywords
        +Boolean is_active
        +List~Recommendation~ recommendations
    }

    class Student {
        +int id
        +String student_id
        +String binusian_id
        +String name
        +String email
        +String phone
        +String track
        +Float gpa
        +int total_sks
        +String partner_lecturer
        +Text position_topic
        +String duration
        +String job_start_date
        +String job_end_date
        +String work_schema
        +String enrollment_status
        +String current_supervisor_code
        +String current_supervisor_name
        +DateTime created_at
        +DateTime updated_at
        +List~Recommendation~ recommendations
    }

    class RecommendationRun {
        +int id
        +DateTime created_at
        +int created_by_id
        +String input_source
        +int total_students
        +int total_supervisors
        +int target_min_capacity
        +int target_max_capacity
        +Boolean capacity_relaxed
        +Text capacity_note
        +Text capacity_bounds_json
        +String solver_name
        +Text solver_note
        +String embedding_backend
        +String embedding_model
        +Text evaluation_json
        +Text pipeline_config_json
        +Float objective_score
        +Text rankings_json
        +List~Recommendation~ recommendations
    }

    class Recommendation {
        +int id
        +int run_id
        +int student_id
        +int supervisor_id
        +Float similarity_score
        +Float group_boost
        +Float final_score
        +Text rule_matches
        +String company_group_key
    }

    class SupervisorProfile {
        <<dataclass>>
        +String code
        +String name
        +Tuple~str~ keywords
        +Tuple~str~ labels
    }

    class RunOverrides {
        <<dataclass>>
        +String embedding_model
        +String embedding_task
        +Boolean enable_group_bonus
        +Boolean enable_extra_docs
        +List~str~ capacity_priority_codes
        +int target_min_capacity
        +int target_max_capacity
    }

    class CapacityPlan {
        <<dataclass>>
        +List~int~ min_caps
        +List~int~ max_caps
        +Boolean relaxed
        +String note
    }

    class RecommendationItem {
        <<dataclass>>
        +Dict student
        +SupervisorProfile supervisor
        +Float similarity_score
        +Float group_boost
        +Float final_score
        +List~str~ rule_matches
        +String company_group_key
    }

    class RecommendationOutput {
        <<dataclass>>
        +List~RecommendationItem~ items
        +Dict~str_int~ counts_by_supervisor
        +String solver_name
        +Float objective_score
        +CapacityPlan capacity_plan
        +String solver_note
        +String embedding_backend
        +String embedding_model
        +String embedding_note
        +List~str~ supervisor_codes
        +ndarray content_similarity_matrix
        +ndarray hybrid_score_matrix
    }

    RecommendationRun "1" --> "*" Recommendation : cascade delete
    AppUser "1" --> "*" RecommendationRun : created by
    Student "1" --> "*" Recommendation : has
    Supervisor "1" --> "*" Recommendation : has
    RecommendationOutput "1" --> "*" RecommendationItem : contains
    RecommendationOutput "1" --> "1" CapacityPlan : contains
    RecommendationItem "*" --> "1" SupervisorProfile : references



<!-- ERD -->
erDiagram
    app_users {
        INTEGER id PK
        VARCHAR_64 username UK "NOT NULL"
        VARCHAR_255 full_name
        VARCHAR_255 password_hash "NOT NULL"
        DATETIME created_at "NOT NULL, DEFAULT now"
    }

    supervisors {
        INTEGER id PK
        VARCHAR_16 code UK "NOT NULL"
        VARCHAR_255 name UK "NOT NULL"
        TEXT profile_keywords
        BOOLEAN is_active "NOT NULL, DEFAULT true"
    }

    students {
        INTEGER id PK
        VARCHAR_32 student_id UK "NOT NULL"
        VARCHAR_32 binusian_id
        VARCHAR_255 name "NOT NULL"
        VARCHAR_255 email
        VARCHAR_64 phone
        VARCHAR_255 track
        FLOAT gpa
        INTEGER total_sks
        VARCHAR_255 partner_lecturer
        TEXT position_topic
        VARCHAR_64 duration
        VARCHAR_64 job_start_date
        VARCHAR_64 job_end_date
        VARCHAR_64 work_schema
        VARCHAR_64 enrollment_status
        VARCHAR_16 current_supervisor_code
        VARCHAR_255 current_supervisor_name
        DATETIME created_at "NOT NULL, DEFAULT now"
        DATETIME updated_at "NOT NULL, DEFAULT now"
    }

    recommendation_runs {
        INTEGER id PK
        DATETIME created_at "NOT NULL, DEFAULT now"
        INTEGER created_by_id FK
        VARCHAR_255 input_source
        INTEGER total_students "NOT NULL"
        INTEGER total_supervisors "NOT NULL"
        INTEGER target_min_capacity "NOT NULL"
        INTEGER target_max_capacity "NOT NULL"
        BOOLEAN capacity_relaxed "NOT NULL, DEFAULT false"
        TEXT capacity_note
        TEXT capacity_bounds_json
        VARCHAR_32 solver_name
        TEXT solver_note
        VARCHAR_64 embedding_backend
        VARCHAR_255 embedding_model
        TEXT evaluation_json
        TEXT pipeline_config_json
        FLOAT objective_score
        TEXT rankings_json
    }

    recommendations {
        INTEGER id PK
        INTEGER run_id FK "NOT NULL"
        INTEGER student_id FK "NOT NULL"
        INTEGER supervisor_id FK "NOT NULL"
        FLOAT similarity_score "NOT NULL, DEFAULT 0.0"
        FLOAT group_boost "NOT NULL, DEFAULT 0.0"
        FLOAT final_score "NOT NULL"
        TEXT rule_matches
        VARCHAR_255 company_group_key
    }

    app_users ||--o{ recommendation_runs : "membuat"
    supervisors ||--o{ recommendations : "direkomendasikan untuk"
    students ||--o{ recommendations : "mendapat rekomendasi"
    recommendation_runs ||--o{ recommendations : "menghasilkan"
