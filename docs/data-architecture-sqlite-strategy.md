# Data Architecture and SQLite Strategy

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents:

- [Domain Model](domain-model.md)
- [Software Architecture Document](software-architecture-document.md)
- [Architecture Principles](architecture-principles.md)
- [Offline-First Requirements](offline-first-requirements.md)

## 1. Purpose

This document defines the local data architecture and SQLite responsibilities for AudioVerse English. It outlines the core data domains, entities, persistence rules, migration policies, and backup considerations, enforcing the principle that the local SQLite database is the absolute source of truth.

## 2. Data Domains and Entities

The system's data is categorized into three primary domains based on the [Domain Model](domain-model.md):

### 2.1 Curriculum & Content (Read-Only/Downloaded)

These entities represent the educational material downloaded to the device. They are structured hierarchically and managed via `ContentPackage`s.

- **Course**: The top-level educational curriculum.
- **Unit**: A subdivision of a Course.
- **Lesson**: The core learning module.
- **Exercise**: Specific interactive tasks within a Lesson.
- **Soundscape**: Asset-driven JSON definitions for audio scenarios.
- **AudioLayer**: Specific audio tracks within a Soundscape.
- **Transcript**: Text representations of audio prompts.
- **ContentPackage**: The versioned bundle containing all the above entities and associated assets.

### 2.2 Learner & Progress (Read/Write)

These entities represent user state, preferences, and performance. This is the primary data manipulated and persisted by the core application flows.

- **LearnerProfile**: User preferences, including critical accessibility settings (e.g., TalkBack enabled, High Contrast Mode).
- **ProgressRecord**: Completion status and scores for Courses, Units, Lessons, and Exercises.
- **Attempt**: Records of a user's interaction with an Exercise, including transcribed text (`Transcript`) and `PronunciationScore`.

### 2.3 Ephemeral Data (Temporary)

- **Voice Recordings**: Ephemeral 16kHz WAV files captured during an `Attempt` for pronunciation assessment. These are strictly temporary and must not be persisted in SQLite or the filesystem beyond the immediate evaluation cycle.

## 3. Persistence Rules

### 3.1 Local SQLite as the Absolute Source of Truth

- The local SQLite database is the authoritative source for all user-generated data (`LearnerProfile`, `ProgressRecord`, `Attempt`).
- Core application flows must read from and write to the SQLite database exclusively, without blocking on or waiting for network requests (enforcing `OFF-001` and `OFF-002`).

### 3.2 Content Data vs. User Data

- **Content Data** (`Courses`, `Lessons`, etc.) is primarily read-only once a `ContentPackage` is installed. The database tracks the installed packages and indexes content for fast offline retrieval.
- **User Data** (`ProgressRecord`, `LearnerProfile`) is actively written to the database during lessons. This data must be kept logically separate from content data to allow content updates without risking progress loss.

### 3.3 Ephemeral Data Deletion

- Voice recordings must be deleted immediately after the `PronunciationEngine` generates a score. This is a strict privacy constraint; this data must never be permanently saved in SQLite or synced.

## 4. Synced vs. Local-Only Data

- **Synced Data**: `LearnerProfile`, `ProgressRecord`, and non-audio `Attempt` metadata (scores, timestamps). This data is eligible for background cloud synchronization if the user explicitly opts in (per `SYNC-001`).
- **Local-Only Data**: Downloaded `ContentPackage` files (assets), cached data, and ephemeral voice recordings. These are never uploaded to the cloud sync backend.

## 5. Schema Migration Policy

To protect local user progress and ensure offline reliability during application updates:

- **Additive Migrations**: Schema changes should be additive whenever possible (e.g., adding new columns with default values).
- **Destructive Changes**: Dropping tables or columns is heavily discouraged. If required, a rigorous migration path must be provided to retain existing `ProgressRecord` and `LearnerProfile` data.
- **Versioning**: The SQLite database must use PRAGMA user_version to track the schema version. Every schema change must be accompanied by a deterministic migration script executed during application startup.
- **Content Upgrades**: Updates to a `ContentPackage` must not break existing `ProgressRecord` links. Entities must use stable UUIDs to maintain referential integrity across versions.

## 6. Backup Considerations

- The application must support exporting local progress and settings as a portable backup (e.g., a serialized JSON file or raw SQLite dump) to prevent data loss if a user uninstalls the app without cloud sync enabled.
- **Privacy Constraint**: Backups must strictly exclude any cached audio or temporary voice recordings. They must contain only structured text and metadata (`ProgressRecord`, `LearnerProfile`).
