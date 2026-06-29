# Optional Sync Architecture

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents:

- [Data Architecture and SQLite Strategy](data-architecture-sqlite-strategy.md)
- [Offline-First Requirements](offline-first-requirements.md)
- [Software Architecture Document](software-architecture-document.md)

## 1. Purpose

This document outlines the optional Supabase sync architecture for AudioVerse English. The core requirement is that cloud synchronization must strictly remain optional and failure-tolerant to ensure offline-first behavior is never compromised.

## 2. Sync Principles and User Consent

- **Optionality (SYNC-001):** The user is not required to create an account or enable sync to use any core functionality.
- **Explicit Consent:** Sync is an opt-in feature. The application will request informed consent via accessible UI components before starting any background synchronization or storing user data on Supabase.
- **Local Source of Truth (SYNC-002):** The local SQLite database is the only database the core application interacts with. The sync engine operates as a separate background process.

## 3. Syncable Entities

Only specific portions of the data model are eligible for synchronization.

- **Synced Data:**
  - `LearnerProfile`: Accessibility settings and user preferences.
  - `ProgressRecord`: Status and scores for Courses, Units, Lessons, and Exercises.
  - `Attempt` Metadata: Scores and timestamps.
- **Local-Only Data (Never Synced):**
  - Downloaded `ContentPackage`s (assets, transcripts).
  - Ephemeral Voice Recordings (16kHz WAV files).

## 4. Conflict Resolution Strategy (SYNC-003)

- **Default Strategy:** Last-Write-Wins based on the entity's `updated_at` timestamp.
- **Merging:** When reconnecting, local progress records and cloud records are compared. Newer records overwrite older records. If a conflict occurs on the same timestamp, the local record takes precedence to ensure the user's immediate experience is not rolled back.

## 5. Offline Failure Behavior and Retries (SYNC-005)

- **Graceful Failure:** If a sync operation fails due to network loss or server error, the sync engine will catch the exception silently.
- **Background Queue:** Failed sync operations are added to a local background queue (e.g., using `workmanager` or a similar background task library). The queue is processed automatically upon the next successful network connection.
- **Non-blocking UI:** No intrusive error modals or "waiting for network" spinners will be displayed to the user during core learning flows.

## 6. Sync State Visibility (SYNC-004)

- The application will provide a dedicated "Sync Status" section in the settings menu.
- The UI will display and the screen reader will announce states such as: "Fully synced", "Pending sync (Offline)", or "Sync disabled".

## 7. Supabase Implementation Considerations

- **Authentication:** Anonymous or email/password authentication via Supabase Auth.
- **Realtime / Polling:** Sync will primarily rely on periodic background tasks rather than persistent WebSocket connections (to preserve battery on low-end devices), with an option to manually trigger a sync from the settings.
- **RLS (Row Level Security):** Supabase RLS policies will be strictly applied to ensure users can only read and write their own data.
