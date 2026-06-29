# Offline-First Requirements

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:

- [Software Requirements Specification](software-requirements-specification.md)
- [Jules Master Development Handbook](../jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md)

## Purpose

This document defines the offline-first behavior and optional sync expectations for AudioVerse English. The core mandate is that the application must work meaningfully without internet access. Cloud services (like Supabase) may improve synchronization, lesson updates, and content delivery, but they must remain optional and not block the core learning experience.

## Definitions

- **Local Content**: Lesson content, soundscapes, and audio files that are downloaded and stored on the device.
- **Local Progress**: The user's learning progress, attempt scores, and settings, stored in the local SQLite database.
- **Sync**: The optional process of backing up local progress to the cloud and retrieving progress from the cloud.

## Offline-First Requirements (OFF)

The following requirements dictate the mandatory offline behavior of the app.

- **OFF-001 (Core Functionality)**: The application's core learning flows, including lesson catalog browsing, audio playback, speaking practice recording, local speech recognition (if supported by the selected engine), and basic pronunciation scoring must function completely without an internet connection, relying only on locally downloaded content and models.
- **OFF-002 (Local Progress Persistence)**: All user progress, including completed lessons, attempt scores, and application settings, must be instantly saved to the local SQLite database.
- **OFF-003 (Content Availability)**: Downloaded content packages must remain accessible indefinitely without requiring server validation or periodic "check-ins".
- **OFF-004 (Graceful Degradation)**: If an optional cloud-dependent feature (e.g., cloud-based AI Tutor or cloud-based speech recognition) is invoked while offline, the app must gracefully degrade, notifying the user via accessible prompts and falling back to a local alternative or safely skipping the step without blocking the overall lesson flow.

## Sync Requirements (SYNC)

The following requirements outline the boundaries and behavior of the optional cloud sync functionality.

- **SYNC-001 (Optional Sync)**: Cloud synchronization must be strictly optional. A user who never creates an account or never enables sync must still be able to use the full core functionality of the app.
- **SYNC-002 (Local Source of Truth during Offline)**: When the device is offline, the local database remains the sole source of truth. The app must never block progress, lock the UI, or show "waiting for network" states during core learning flows.
- **SYNC-003 (Conflict Resolution)**: When reconnecting, the sync engine must resolve conflicts between local and remote data. The strategy should default to merging progress where possible, prioritizing the most recently updated record (last-write-wins based on timestamp) if a direct conflict occurs on a specific entity.
- **SYNC-004 (Sync State Visibility)**: The app must provide an accessible way (via screen reader announcements in a designated settings area) for the user to check if their data is currently fully synced, pending sync, or offline.
- **SYNC-005 (Failure Tolerance)**: If a sync operation fails (e.g., due to a dropped connection), the app must queue the operation to retry later in the background without interrupting the user's current session or displaying intrusive error modals.
