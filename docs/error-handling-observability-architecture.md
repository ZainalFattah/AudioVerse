# Error Handling and Observability Architecture

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents:

- [Software Architecture Document](software-architecture-document.md)
- [Accessibility Requirements](accessibility-requirements.md)
- [Security, Privacy, and Data Requirements](security-privacy-data-requirements.md)

## Purpose

This document defines the error handling, logging, diagnostics, and user-facing recovery strategy for AudioVerse English.

## Scope

The scope encompasses all failure states within the application, including speech recognition errors, audio playback interruptions, database failures, sync conflicts, and unexpected application crashes. It defines how errors are logged locally, how telemetry works, and how errors are communicated to users—especially Total Blind and Low Vision learners.

## Audience

This document is intended for engineers, UX designers, and QA testers building and verifying AudioVerse English.

## Assumptions

- The application will run primarily offline, meaning error resolution cannot rely on web search or online documentation.
- Screen readers (TalkBack, VoiceOver) are the primary way many users will interact with error states.
- Voice data and user progress are sensitive and must not be inadvertently included in diagnostic logs.

## Error Taxonomy

Errors in AudioVerse English are categorized to determine the correct handling strategy:

1. **Transient Errors (Recoverable automatically or via simple retry):**
   - *Examples:* Audio buffer underrun, temporary file lock, brief ASR initialization delay.
   - **Handling:** Silent retry with exponential backoff if appropriate. Do not surface to the user unless the retry threshold is exceeded.

2. **User-Actionable Errors (Recoverable via user intervention):**
   - *Examples:* Microphone permission denied, storage full, no lesson downloaded for offline playback.
   - **Handling:** Clear, accessible audio prompt explaining the issue and the exact step required to resolve it (e.g., "Microphone access is needed to practice speaking. Please go to settings to enable it.").

3. **Domain/Engine Errors (Graceful degradation):**
   - *Examples:* AI Tutor module fails to load, speech recognition confidence is persistently low.
   - **Handling:** Fallback to standard behavior. E.g., if AI Tutor fails, disable the tutor button with an accessible announcement ("Tutor currently unavailable"), but allow the core lesson to continue. ASR low confidence should trigger a "try again" prompt rather than a failure state.

4. **Fatal System Errors (Unrecoverable):**
   - *Examples:* SQLite database corruption, missing core asset manifests, persistent native audio engine crash.
   - **Handling:** Log the stack trace locally, announce a critical failure message via the screen reader ("A critical error occurred. AudioVerse needs to restart."), and safely terminate the session.

## Accessible Messages and User Recovery

All user-facing errors must adhere to the [Accessibility Requirements](accessibility-requirements.md).

- **Audio-First Communication:** Errors must not rely purely on visual cues (like red text or modal dialogs). Critical errors must proactively announce themselves using screen reader semantics (e.g., `Semantics(liveRegion: true)` in Flutter) or direct audio synthesis/playback if appropriate.
- **Predictable Focus:** When an error modal or actionable state occurs, TalkBack/VoiceOver focus must move immediately to the error message, and the next logical swipe must land on the recovery action (e.g., "Retry" or "Go to Settings").
- **Clarity over Technical Details:** Never read raw technical errors to the user (e.g., "SQLiteException: no such table"). Always map technical errors to user-friendly domain messages.

## Logging Boundaries and Privacy

To protect learner privacy and comply with security requirements:

- **PII and Voice Isolation:** Logs **MUST NEVER** contain transcripts of what the user said, raw audio buffers, or any PII (names, exact locations).
- **Local-First Logging:** Given the offline-first nature, all standard application logs are written to an internal rolling text file or diagnostic SQLite table with a strict size limit (e.g., 5MB max before rotation).
- **Redaction:** Any dynamic values inserted into logs must be scrubbed of sensitive context. Use structured logging where parameters are explicitly marked as safe or unsafe.

## Crash Reporting and Observability Options

### Crash Reporting

Since cloud sync is strictly optional, telemetry and crash reporting must follow a strict opt-in model.

1. **Offline Mode (Default):** Crashes are written to a local `.crash` log. On the next successful startup, the app detects the crash log and prompts the user (with an accessible dialog): "The app recently stopped unexpectedly. Would you like to share the anonymous error report to help us improve?"
2. **Opt-In Telemetry:** If the user explicitly opts in, crash reports (e.g., via Sentry or Firebase Crashlytics) can be transmitted. These tools must be configured to discard IPs and completely strip user-identifying payload data.

### Diagnostics

- **Settings Area:** A designated, screen-reader-accessible settings menu will allow users to "Export Diagnostic Data". This exports a ZIP containing the local logs and database schema state (without user progress/audio data), which can be emailed to support.

## Risks

- **Silent Failures in Audio Engine:** If the native audio engine crashes without throwing a Flutter exception, the UI may appear to play, but the blind user hears nothing and assumes the app is broken. We need explicit native layer watchdogs.
- **Log Bloat on Low-End Devices:** Excessive logging could fill up the limited storage of 2GB RAM / 16GB storage phones. Log rotation must be strictly enforced.

## Open Questions

- Should we bundle a local Text-To-Speech (TTS) engine fallback specifically for reading fatal error messages if the screen reader service crashes or is unexpectedly suspended?
- What specific crash reporting tool is best suited for an offline-first app without introducing heavy SDK dependencies?

## Acceptance Criteria

- Error taxonomy is defined and maps to actionable recovery paths.
- Accessible error announcements and focus management rules are documented.
- Logging boundaries specifically forbid PII and voice data.
- Crash reporting requires explicit opt-in and degrades gracefully offline.

## Review Checklist

- Can blind users recover from errors without visual cues? Yes, via explicit screen-reader focus and audio prompts.
- Are logging practices privacy-conscious? Yes, strict redaction and no voice transcripts.

## Change Log

- 2026-06-28: Initial draft created by Jules.
