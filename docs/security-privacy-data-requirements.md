# Security, Privacy, and Data Requirements

## 1. Introduction

This document outlines the security, privacy, and data requirements for AudioVerse English. Given the focus on offline-first capabilities and the target demographic of blind and low-vision learners, the application adheres to strict data minimization, local-first storage defaults, and explicit user consent for any data leaving the device.

## 2. Data Types and Classification

The application handles the following data types:

| Data Type | Description | Classification |
| :--- | :--- | :--- |
| **Voice Recordings** | Temporary audio captured during speaking practice and assessment. | Sensitive (PII potentially included in speech) |
| **Learner Profile** | User preferences, accessibility settings (e.g., TalkBack compatibility, text scaling), and basic demographic data if provided. | Personal |
| **Learning Progress** | Lesson completion status, pronunciation scores, and attempt history. | Personal |
| **Content Packages** | Downloaded educational materials (audio files, JSON manifests, transcripts). | Public/App Data |
| **Telemetry (Optional)** | Crash reports and basic usage analytics. | Anonymous |

## 3. Storage and Deletion Policies

### 3.1 Voice Recordings (Strict Policy)

- **Storage:** Voice recordings are stored in a temporary cache directory as 16kHz WAV files.
- **Duration:** Recordings are strictly capped at 30 seconds per attempt.
- **Deletion:** Recordings must be aggressively and automatically deleted immediately after the local pronunciation assessment or speech recognition process completes. They are never stored permanently and are never synced to the cloud.

### 3.2 Learner Profile and Progress

- **Storage:** Stored locally in a secure SQLite database. This database is the absolute source of truth when offline.
- **Deletion:** Users must have a clear, accessible option within the app settings to completely delete all profile and progress data, effectively resetting the application.

### 3.3 Content Packages

- **Storage:** Stored on the local file system. Treated as permanent until explicitly deleted by the user.
- **Deletion:** Users can manage storage by deleting specific content packages or all downloaded content via the application settings.

## 4. Permissions

The application will request only the minimum required permissions:

- **Microphone:** Required for core speaking practice. Must clearly explain why it is needed. If denied, the app should degrade gracefully (e.g., allowing listening-only mode if applicable, or explaining that speaking exercises are unavailable).
- **Network/Internet:** Required for downloading content packages and optional cloud synchronization.

## 5. Cloud Boundaries and Sync

### 5.1 Local-First Defaults

- The application operates completely offline by default. No network connection is required to complete core learning flows once content is downloaded.
- Cloud synchronization (e.g., to Supabase) is strictly opt-in. The app will never silently sync data in the background without explicit user configuration and consent.

### 5.2 Optional AI Tutor

- If the AI Tutor module relies on a cloud API, explicit user consent must be obtained before any data (text or audio) is sent.
- The AI Tutor must implement safety guardrails, restricting topics to English language learning and preventing the transmission of PII.
- If running locally, the AI Tutor must operate within the constraints of low-end devices (e.g., memory limits) without compromising security.

## 6. Security Requirements

- **Data in Transit:** All network communication (content downloads, optional sync, optional cloud AI) must use TLS 1.2 or higher.
- **Data at Rest:** While local SQLite data is not encrypted by default on all low-end devices due to performance overhead, sensitive PII should not be collected. The primary protection is device-level security (screen lock).
- **Vulnerability Management:** Third-party dependencies (Flutter packages, SQLite, ASR engine) must be regularly updated to address known vulnerabilities.

## 7. Privacy Disclosures

- The application must provide a clear, accessible privacy policy.
- The policy must explicitly state the ephemeral nature of voice recordings and the offline-first data storage model.
- Screen reader users must be able to easily navigate and comprehend all privacy disclosures and permission requests.
