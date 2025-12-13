---
publish: true
---

# Paul Supabase Schema Design
[[client-paul]]

## 1. Overview

**Purpose:** Migration from Flask/local PostgreSQL to Supabase for the Paul Faceless YouTube project.

**Scope:**
- 15 tables to create
- 10 PostgreSQL ENUM types
- RLS policies for multi-tenant access

**Key Decisions:**
- User references use `UUID` → `auth.users.id` (not integer bridge table)
- File path columns remain `TEXT` (Supabase Storage migration is separate issue #236)
- ENUMs as PostgreSQL types (not TEXT with CHECK constraints)

**Related Issues:**
- #122: Backend Rebuild - FastAPI + Supabase (parent)
- #235: Database Schema Migration (this work)
- #230: Supabase Auth + RBAC Setup (parallel)

---

## 2. ENUMs (PostgreSQL Types)

10 ENUMs to create as PostgreSQL ENUM types:

| ENUM Name | Values |
|-----------|--------|
| `footage_type_enum` | 'SECTIONED', 'GENERATIVE' |
| `step_enum` | '1', '2', '3', '4', '5', '6', '7' |
| `status_enum` | 'running', 'idle', 'finished', 'error' |
| `script_gen_type_enum` | 'YOUTUBE_URL', 'TITLE_GEN' |
| `channel_type_enum` | 'Queue', 'VidRush' |
| `video_data_source_enum` | 'youtube', 'manual' |
| `vidrush_status_enum` | 'pending', 'approved', 'rejected' |
| `thumbnail_processing_status_enum` | 'pending', 'completed', 'hidden' |
| `assignment_status_enum` | 'pending', 'in_progress', 'completed', 'video_processed' |
| `thumbnail_assignment_status_enum` | 'assigned', 'in_progress', 'completed', 'delivered' |

---

## 3. Core Tables (No Foreign Keys)

Tables with no foreign key dependencies - create first.

### 3.1 channels
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| youtube_channel_id | TEXT | UNIQUE NOT NULL |
| display_name | TEXT | |
| token_file_path | TEXT | NOT NULL |
| is_active | BOOLEAN | DEFAULT TRUE |
| type | channel_type_enum | |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |

### 3.2 prompt_configuration
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| role_prompt | TEXT | NOT NULL |
| script_structure | TEXT | |
| tone_style | TEXT | |
| retention_techniques | TEXT | |
| additional_information | TEXT | |
| title | TEXT | NOT NULL |
| type | TEXT | DEFAULT 'YOUTUBE' |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |

### 3.3 image_style
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| prompt | TEXT | |
| title | TEXT | NOT NULL |
| image_model | TEXT | NOT NULL |
| video_model | TEXT | NOT NULL |
| use_video_model | BOOLEAN | DEFAULT FALSE |
| rule_0_30 | INTEGER | DEFAULT 3 |
| rule_30_60 | INTEGER | DEFAULT 6 |
| rule_60_600 | INTEGER | DEFAULT 12 |
| rule_600_3600 | INTEGER | DEFAULT 24 |
| rule_600_plus | INTEGER | DEFAULT 24 |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |

### 3.4 voice
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| name | TEXT | NOT NULL |
| path_to_voice | TEXT | NOT NULL |
| language | TEXT | NOT NULL |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |

### 3.5 video_gen_template
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| name | TEXT | NOT NULL |
| json_data | JSONB | |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |

---

## 4. Dependent Tables (With Foreign Keys)

Tables with foreign key relationships to core tables.

### 4.1 templates
**Foreign Keys:** 5 total (prompt_configuration x2, voice, image_style, channel, video_gen_template)

| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| name | TEXT | NOT NULL |
| prompt_configuration | INTEGER | FK → prompt_configuration.id |
| title_prompt_configuration | INTEGER | FK → prompt_configuration.id |
| voice_id | INTEGER | FK → voice.id |
| voice_name | TEXT | (temporary compatibility) |
| footage_type | footage_type_enum | |
| voice_speed | FLOAT | |
| image_style_id | INTEGER | FK → image_style.id |
| skip_image_processing | BOOLEAN | DEFAULT FALSE |
| script_gen_type | script_gen_type_enum | |
| channelurl | TEXT | |
| channel_id | INTEGER | FK → channels.id ON DELETE SET NULL |
| statistics | JSONB | |
| video_gen_template_id | INTEGER | FK → video_gen_template.id ON DELETE SET NULL |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |

### 4.2 video_process
**Foreign Keys:** 5 total (template, video_gen_template, prompt_configuration x2, script_assignments)

| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| template_id | INTEGER | FK → templates.id ON DELETE CASCADE |
| video_url | TEXT | |
| step | step_enum | |
| status | status_enum | |
| path_to_folder | TEXT | |
| is_custom_script | BOOLEAN | DEFAULT FALSE |
| video_duration | INTEGER | |
| title | TEXT | |
| is_auto_pilot | BOOLEAN | DEFAULT FALSE |
| skip_llm | BOOLEAN | DEFAULT FALSE |
| autopilot | BOOLEAN | DEFAULT FALSE |
| word_count | FLOAT | DEFAULT 0 |
| download_checked | BOOLEAN | DEFAULT FALSE |
| skip_image_processing | BOOLEAN | DEFAULT FALSE |
| script_gen_type | script_gen_type_enum | |
| script_duration_seconds | INTEGER | DEFAULT 60 |
| error_message | TEXT | |
| video_gen_template_id | INTEGER | FK → video_gen_template.id ON DELETE SET NULL |
| is_video_generation_running | BOOLEAN | DEFAULT FALSE |
| youtube_prompt_configuration | INTEGER | FK → prompt_configuration.id |
| title_prompt_configuration | INTEGER | FK → prompt_configuration.id |
| description | TEXT | |
| language | TEXT | DEFAULT 'en' |
| autopreview | BOOLEAN | DEFAULT FALSE |
| require_thumbnail | BOOLEAN | DEFAULT TRUE |
| autovideo | BOOLEAN | DEFAULT FALSE |
| autoupload | BOOLEAN | DEFAULT FALSE |
| llm_model | TEXT | DEFAULT 'gpt-4o-mini' |
| preset_name | TEXT | DEFAULT 'default' |
| is_video_uploaded | BOOLEAN | DEFAULT FALSE |
| is_thumbnail_uploaded | BOOLEAN | DEFAULT FALSE |
| youtube_video_id | TEXT | |
| thumbnail_title | TEXT | |
| thumbnail_description | TEXT | |
| reference | TEXT | |
| add_cta_flag | BOOLEAN | DEFAULT FALSE |
| assigned_script_id | INTEGER | FK → script_assignments.id ON DELETE SET NULL |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| finished_at | TIMESTAMPTZ | DEFAULT NOW() |

### 4.3 multi_vid
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| name | TEXT | NOT NULL |
| script_path | TEXT | NOT NULL |
| video_process_id | INTEGER | FK → video_process.id ON DELETE CASCADE |
| multi_vid_languages | JSONB | NOT NULL |
| status | status_enum | DEFAULT 'running' |
| error_message | TEXT | |
| use_video_process_script | BOOLEAN | DEFAULT FALSE |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |

---

## 5. User-Referencing Tables

Tables with UUID columns referencing `auth.users.id` (Supabase Auth).

### 5.1 scripts
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| name | VARCHAR(500) | NOT NULL |
| description | TEXT | |
| created_by | UUID | FK → auth.users.id ON DELETE SET NULL |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |

### 5.2 script_assignments
**Unique Constraint:** (script_id, user_id)

| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| script_id | INTEGER | FK → scripts.id ON DELETE CASCADE NOT NULL |
| user_id | UUID | FK → auth.users.id ON DELETE CASCADE NOT NULL |
| status | assignment_status_enum | |
| assigned_at | TIMESTAMPTZ | DEFAULT NOW() |
| started_at | TIMESTAMPTZ | |
| completed_at | TIMESTAMPTZ | |
| notes | TEXT | |
| file_path | TEXT | |
| original_filename | TEXT | |
| file_extension | VARCHAR(10) | |
| file_size | INTEGER | |

### 5.3 thumbnail_assignments
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| makemythumb_request_id | VARCHAR(255) | NOT NULL |
| user_id | UUID | FK → auth.users.id ON DELETE CASCADE NOT NULL |
| status | thumbnail_assignment_status_enum | |
| assigned_by | UUID | FK → auth.users.id ON DELETE SET NULL |
| assigned_at | TIMESTAMPTZ | DEFAULT NOW() |
| started_at | TIMESTAMPTZ | |
| completed_at | TIMESTAMPTZ | |
| delivered_at | TIMESTAMPTZ | |
| notes | TEXT | |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |

---

## 6. Auxiliary Tables

Standalone tables with simpler relationships.

### 6.1 stored_thumbnails
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| unique_id | VARCHAR(50) | UNIQUE NOT NULL |
| filename | TEXT | NOT NULL |
| file_extension | VARCHAR(10) | NOT NULL |
| file_size | INTEGER | NOT NULL |
| file_path | TEXT | NOT NULL |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |

### 6.2 vidrush_assigned_videos
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| channel_id | INTEGER | FK → channels.id ON DELETE CASCADE NOT NULL |
| youtube_video_id | TEXT | NOT NULL |
| video_title | TEXT | |
| video_description | TEXT | |
| video_thumbnail_url | TEXT | |
| assigned_by_admin | BOOLEAN | DEFAULT TRUE |
| status | vidrush_status_enum | |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |

### 6.3 vidrush_drafts
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| assigned_video_id | INTEGER | FK → vidrush_assigned_videos.id ON DELETE CASCADE NOT NULL |
| draft_title | TEXT | |
| draft_description | TEXT | |
| draft_tags | JSONB | |
| draft_thumbnail_path | TEXT | |
| status | vidrush_status_enum | DEFAULT 'pending' |
| admin_notes | TEXT | |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |
| approved_at | TIMESTAMPTZ | |

### 6.4 thumbnail_processing
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| title | VARCHAR(500) | NOT NULL |
| description | TEXT | |
| reference_link | TEXT | |
| thumbnail_path | TEXT | |
| status | thumbnail_processing_status_enum | |
| created_by_admin | BOOLEAN | DEFAULT TRUE |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |
| completed_at | TIMESTAMPTZ | |

### 6.5 competition_channels
**Unique Constraint:** (template_id, youtube_channel_id)

| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| template_id | INTEGER | FK → templates.id ON DELETE CASCADE NOT NULL |
| youtube_channel_id | VARCHAR(255) | NOT NULL |
| channel_name | VARCHAR(500) | |
| channel_url | TEXT | |
| subscriber_count | BIGINT | |
| total_views | BIGINT | |
| video_count | INTEGER | |
| channel_thumbnail | TEXT | |
| last_scraped_at | TIMESTAMPTZ | |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |

### 6.6 competition_videos
| Column | Type | Constraints |
|--------|------|-------------|
| id | SERIAL | PRIMARY KEY |
| competition_channel_id | INTEGER | FK → competition_channels.id ON DELETE CASCADE NOT NULL |
| youtube_video_id | VARCHAR(255) | UNIQUE NOT NULL |
| title | TEXT | |
| description | TEXT | |
| thumbnail_url | TEXT | |
| view_count | BIGINT | |
| like_count | INTEGER | |
| comment_count | INTEGER | |
| published_at | TIMESTAMPTZ | |
| duration | INTEGER | |
| tags | JSONB | |
| views_per_day | FLOAT | |
| engagement_rate | FLOAT | |
| source | video_data_source_enum | |
| scraped_at | TIMESTAMPTZ | DEFAULT NOW() |
| created_at | TIMESTAMPTZ | DEFAULT NOW() |
| updated_at | TIMESTAMPTZ | DEFAULT NOW() |

---

## 7. RLS Policy Design

Row Level Security policies for multi-tenant access control.

### 7.1 Multi-Tenant Access (Channel-Based Isolation)
- **channels:** Filter by user's assigned channels via `user_roles` join
- **templates:** Filter by `channel_id` relationship
- **video_process:** Filter via `template → channel` relationship

### 7.2 Role-Based Access
Based on `user_roles` table from #230:

| Role | Access Pattern |
|------|----------------|
| admin | Full access to all tables |
| manager | Channel-specific access (assigned channels only) |
| scripter | `script_assignments` where `user_id = auth.uid()` |
| thumbnail | `thumbnail_assignments` where `user_id = auth.uid()` |
| queue_processor | `video_process` workflow access |

### 7.3 Ownership Patterns
- **vidrush_drafts:** Created by user (implicit ownership)
- **script_assignments:** `user_id = auth.uid()`
- **thumbnail_assignments:** `user_id = auth.uid()`

### 7.4 Policy Implementation Pattern
```sql
-- Example: scripter can only see their assignments
CREATE POLICY "scripter_own_assignments" ON script_assignments
  FOR ALL
  USING (user_id = auth.uid());

-- Example: admin full access
CREATE POLICY "admin_full_access" ON script_assignments
  FOR ALL
  USING (
    EXISTS (
      SELECT 1 FROM user_roles
      WHERE user_roles.user_id = auth.uid()
      AND 'admin' = ANY(user_roles.roles)
    )
  );
```

---

## 8. Migration Sequence

Recommended order for creating tables (dependency-aware).

### Phase 1: Foundation
1. Create all 10 ENUMs
2. Create core tables (no FK dependencies):
   - channels
   - prompt_configuration
   - image_style
   - voice
   - video_gen_template

### Phase 2: Primary Dependencies
3. Create templates (references Phase 1 tables)
4. Create competition_channels (references templates)
5. Create competition_videos (references competition_channels)

### Phase 3: User-Referencing Tables
6. Create scripts (references auth.users)
7. Create script_assignments (references scripts, auth.users)
8. Create thumbnail_assignments (references auth.users)

### Phase 4: Video Processing
9. Create video_process (references templates, prompt_configuration, video_gen_template, script_assignments)
10. Create multi_vid (references video_process)

### Phase 5: Auxiliary Tables
11. Create stored_thumbnails
12. Create vidrush_assigned_videos (references channels)
13. Create vidrush_drafts (references vidrush_assigned_videos)
14. Create thumbnail_processing

### Phase 6: RLS Policies
15. Implement role-based policies for all tables
16. Test with different user roles

---

## Excluded Tables

Per issue #235, these tables are NOT migrated:

| Table | Reason |
|-------|--------|
| accounts | Replaced by Supabase Auth + user_roles from #230 |
| script_entry | Unused in production (dead code) |
| MakeMyThumb tables | Separate system |

---

## Source Reference

- **Flask models:** `paul_queue/app/models.py`
- **Supabase project:** `xthxrdghwwwymmkfwyjc` (FACELESS-YOUTUBE)
- **Migrations directory:** `supabase/migrations/`
