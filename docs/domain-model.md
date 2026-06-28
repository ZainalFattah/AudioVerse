# Domain Model

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents: docs/glossary.md

## Purpose
To outline the conceptual entities and their relationships within the AudioVerse English platform, serving as a foundation for database design and software architecture.

## Core Entities

### 1. Curriculum & Content
- **Course**: Contains multiple `Units`.
- **Unit**: Contains multiple `Lessons`.
- **Lesson**: The core learning module. Contains one or more `Exercises` or `Soundscapes`. Has associated metadata (title, description, accessibility tags).
- **Exercise**: A specific interactive task within a lesson (e.g., Listen and Repeat).
- **Soundscape**: A structured collection of audio assets defined by a JSON manifest. Belongs to a `Lesson`. Contains `AudioLayers`.
- **AudioLayer**: A single audio track (e.g., background noise, dialogue) within a `Soundscape`, with defined timing and spatial metadata.
- **Transcript**: The text representation of audio within a `Lesson` or `Soundscape`.
- **ContentPackage**: A versioned bundle containing `Courses`, `Units`, `Lessons`, `Soundscapes`, and associated audio files. Downloaded or pre-installed.

### 2. Learner & Progress
- **LearnerProfile**: Represents the user on the device. Contains `Preferences` (e.g., TalkBack enabled, High Contrast Mode).
- **ProgressRecord**: Tracks completion status and scores for `Courses`, `Units`, `Lessons`, and `Exercises`. Stored locally in SQLite.
- **Attempt**: A single instance of a Learner interacting with an `Exercise`. Contains a `Recording` (audio file path), a `Transcript` (from ASR), and a `PronunciationScore`.

### 3. Engines & Processing
- **AudioEngine**: Responsible for orchestrating the playback of `Lessons` and `Soundscapes` (mixing `AudioLayers`).
- **SpeechRecognitionEngine (ASR)**: Takes a `Recording` (audio) and outputs a `Transcript` (text).
- **PronunciationEngine**: Takes a `Recording` (audio) and an *expected* `Transcript`, and outputs a `PronunciationScore`.
- **AITutorEngine**: (Optional) Takes a Learner prompt (audio or text) and `ProgressRecord` context, and outputs guidance or feedback.

## Key Relationships

- A `LearnerProfile` generates many `ProgressRecords` and `Attempts`.
- A `ContentPackage` provides many `Courses`, which contain `Units`, which contain `Lessons`.
- A `Lesson` uses the `AudioEngine` to play `Soundscapes` and standard audio.
- An `Attempt` requires the `SpeechRecognitionEngine` to transcribe what was said.
- An `Attempt` requires the `PronunciationEngine` to evaluate the speech against the expected `Transcript`.
- `ProgressRecords` and `LearnerProfiles` may be uploaded via the `SyncEngine` to the cloud, but are primarily mastered locally.

## Review Checklist
- [x] Are speech recognition and pronunciation assessment separated?
- [x] Is soundscape correctly defined as asset-based?
