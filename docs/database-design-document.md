# Database Design Document

Project: AudioVerse English
Status: Approved
Owner: Jules
Reviewers: User
Date: 2026-06-28
Related documents: [Jules Master Development Handbook](../jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md), [Data Architecture and SQLite Strategy](../docs/data-architecture-sqlite-strategy.md), [Software Architecture Document](../docs/software-architecture-document.md)

## 2. Purpose and Scope

### Purpose

This document defines the physical SQLite database design for AudioVerse English. It details the table schemas, entities, relationships, indexes, data retention policies, and migration strategies.

### Scope

- **In Scope**:
  - Local SQLite schema design covering core entities: settings, content packages, lessons, progress, and practice attempts.
  - Sync metadata columns for optional cloud sync (Supabase).
  - Migration strategy and data retention policies.
- **Out of Scope**:
  - Remote cloud database (Supabase) schema, except how it relates to local sync metadata.
  - Detailed application repository implementations.

## 3. Requirements Summary

This design fulfills the following core requirements:

- **OFF-001 / OFF-002**: Local SQLite is the absolute source of truth; core flows must be fully functional offline.
- **DATA-001**: User progress, settings, and attempts must be decoupled from downloaded content data to ensure safe content updates without data loss.
- **SYNC-001**: Entities that can be synced must track their sync state locally (e.g., last synced timestamp).
- **SEC-001 / SEC-002**: Voice recordings are ephemeral and strictly out of scope for database storage. Only the metadata and pronunciation scores of practice attempts are persisted.

## 4. Technical Design

### Architecture Context

The SQLite database resides locally on the device. All application data flows through Repository classes that abstract SQL queries. When the optional Cloud Sync Engine is enabled, it reads and updates records based on sync metadata (`updated_at`, `sync_status`).

### Component Breakdown (Schema)

#### `user_settings`

Stores accessibility preferences and general user settings.

- `id` (TEXT, PK): UUID.
- `key` (TEXT, UNIQUE): Setting identifier (e.g., `theme`, `speech_rate`).
- `value` (TEXT): JSON or scalar representation of the value.
- `updated_at` (INTEGER): Epoch timestamp.
- `sync_status` (INTEGER): Enum (0=Pending, 1=Synced).

#### `content_packages`

Tracks downloaded learning materials. Content is immutable from the user's perspective.

- `id` (TEXT, PK): Package UUID.
- `version` (INTEGER): Package version to manage updates.
- `title` (TEXT): Readable name of the package.
- `manifest_path` (TEXT): Local file path to the content manifest JSON.
- `installed_at` (INTEGER): Epoch timestamp.
- `status` (INTEGER): Enum (0=Downloading, 1=Installed).

#### `lessons`

Metadata for individual lessons within content packages. This acts as a reference table for progress.

- `id` (TEXT, PK): Lesson UUID.
- `package_id` (TEXT, FK): Refers to `content_packages.id`.
- `title` (TEXT): Lesson title.
- `sequence_order` (INTEGER): Ordering within the package.

#### `user_progress`

Tracks the learner's completion status per lesson.

- `id` (TEXT, PK): Progress record UUID.
- `lesson_id` (TEXT, FK): Refers to `lessons.id`.
- `status` (INTEGER): Enum (0=Not Started, 1=In Progress, 2=Completed).
- `highest_score` (INTEGER): Best pronunciation score achieved (0-100).
- `updated_at` (INTEGER): Epoch timestamp.
- `sync_status` (INTEGER): Enum (0=Pending, 1=Synced).

#### `practice_attempts`

Records individual speaking attempts for analytical or sync purposes. No audio is stored here.

- `id` (TEXT, PK): Attempt UUID.
- `lesson_id` (TEXT, FK): Refers to `lessons.id`.
- `recognized_text` (TEXT): ASR transcript.
- `score` (INTEGER): Pronunciation score (0-100).
- `created_at` (INTEGER): Epoch timestamp.
- `sync_status` (INTEGER): Enum (0=Pending, 1=Synced).

### Indexes

- `idx_user_progress_lesson_id` on `user_progress(lesson_id)`
- `idx_practice_attempts_lesson_id` on `practice_attempts(lesson_id)`
- `idx_sync_status` on `user_progress(sync_status)` and `practice_attempts(sync_status)` to quickly find pending records for cloud sync.

### Data Flow

1. **Reads**: Repositories query SQLite directly, returning domain entities to the application layer.
2. **Writes**: Updates write locally first, immediately setting `sync_status = 0` (Pending) and updating `updated_at`.
3. **Sync**: A background job queries for `sync_status = 0`, attempts upload to Supabase, and on success sets `sync_status = 1`.

### Interfaces / APIs

No external HTTP APIs are exposed directly. Internal repositories provide standard CRUD interfaces:

- `ISettingsRepository`
- `IContentRepository`
- `IProgressRepository`
- `IAttemptsRepository`

### State Management

Database operations are asynchronous. The UI state will listen to repository streams or rely on Future-based fetches that react to local DB changes.

### Accessibility implementation

While a database does not have a UI, data models must support accessibility features:

- `user_settings` will persist settings like `blank_screen_mode_enabled` or `high_contrast_enabled`.
- Database schema ensures that all text necessary for screen reader announcements (e.g., lesson titles, status strings) is reliably available offline.

### Offline / Sync behavior

- **Local absolute truth**: Sync conflicts default to Last-Write-Wins based on the `updated_at` timestamp.
- **Offline degradation**: If sync fails or is disabled, local CRUD operations continue without interruption. The `sync_status` remains `0` indefinitely with no visible errors to the user.

## 5. Alternatives Considered

### Realm / ObjectBox

- **Why rejected**: Introduces heavy native binaries and vendor lock-in. SQLite is native, universally supported, and extremely lightweight, perfectly fitting the low-end device constraint.

### Shared Preferences / Secure Storage for all data

- **Why rejected**: Suitable for simple settings but insufficient for structured, relational data like lesson progress and practice attempts.

## 6. Testing Strategy

- **Unit Tests**: Test repository logic using an in-memory SQLite database instance (`sqflite_common_ffi`).
- **Integration Tests**: Verify schema creation, constraint validation (e.g., FK cascades), and correct update of `updated_at` timestamps.
- **Offline Tests**: Force network disconnection and ensure `sync_status` transitions and local reads function correctly.

## 7. Migration and Rollout

- **Versioning**: Schema versioning managed through `sqflite`'s `onUpgrade` callback.
- **Rollout**: Initial MVP uses version 1.
- **Migration Policy**: Schema updates must strictly preserve existing user progress. Structural changes to `user_progress` and `user_settings` must utilize non-destructive `ALTER TABLE` commands.

## 8. Known Risks and Limitations

- **Storage size**: SQLite databases can grow over time. We must enforce retention rules on `practice_attempts` (e.g., delete attempts older than 30 days) to prevent filling up low-end device storage.
- **Sync conflicts**: The Last-Write-Wins strategy is simple but could potentially overwrite data if a user uninstalls, works offline on another device, and then syncs.
