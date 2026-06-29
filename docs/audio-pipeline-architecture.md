# Audio Pipeline Architecture

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents:

- [Audio and Soundscape Requirements](audio-soundscape-requirements.md)
- [Software Architecture Document](software-architecture-document.md)
- [Modular Engine Architecture](modular-engine-architecture.md)

## 1. Purpose

This document defines the audio pipeline architecture for AudioVerse English. It covers playback, recording, soundscape orchestration, audio session management, interruptions, and background behavior.

## 2. Audio Pipeline Layers

The audio pipeline is divided into distinct layers to enforce the separation of concerns, ensuring that the presentation, orchestration, and native audio implementations are independent.

```text
Presentation Layer (Flutter UI, Accessibility Interaction)
  -> Soundscape Orchestration Layer (Manifest parsing, event timing)
  -> Audio Interface Layer (IAudioPipelineEngine)
  -> Native Audio Adapter Layer (just_audio, flutter_sound)
```

## 3. Playback Architecture

### 3.1 Orchestration

Playback is not just about playing a single file. It is controlled by the Soundscape Orchestration Layer, which reads a deterministic JSON manifest.

- **Manifest Parser:** Reads the JSON file and queues audio assets.
- **Timing Controller:** Handles wait times, overlaps, and sequential playback as defined in the manifest.
- **Speed Adjustment:** Passes speed modifications (0.5x to 1.5x) to the native adapter without altering pitch.

### 3.2 Accessibility and Audio Focus

- **Ducking and Pausing:** When TalkBack or VoiceOver announces a system event or user interaction, the OS requests audio focus. The Native Audio Adapter MUST respect this request. By default, learning audio should pause or duck (lower volume) when screen reader focus is active to prevent conflicts.
- **Audio Prompts:** System prompts embedded in the application (e.g., "Recording started") will be mixed through a separate, non-duckable channel if possible, or managed sequentially.

## 4. Recording Architecture

### 4.1 Temporary Capture Pipeline

- **Trigger:** The Soundscape Orchestration Layer triggers a recording session based on manifest cues.
- **Capture:** The Native Audio Adapter captures microphone input at 16kHz, 16-bit PCM WAV.
- **Storage:** The audio stream is written directly to a temporary, non-persistent local directory.
- **Constraints:** A strict 30-second maximum duration is enforced by the `IAudioPipelineEngine`.

### 4.2 Handoff to ASR

- Once recording completes, the file path is passed to the `ISpeechRecognitionEngine` adapter.
- The Audio Pipeline Layer does not perform speech recognition; it only manages the audio capture and lifecycle.

## 5. Soundscape Architecture

Soundscapes are strictly asset-based, not dynamically generated AI.

### 5.1 JSON Manifest Structure

The manifest defines:

- A sequence of scenes.
- Audio file paths relative to the content package.
- Timing instructions (e.g., `delay: 2000ms`).
- Basic spatial panning constraints (left/right channels).

### 5.2 Mixing Strategy

The Native Audio Adapter will use a mixer capable of handling at least two concurrent audio streams (e.g., a background ambient track and a foreground speech track). To preserve low-end device performance, no more than three simultaneous tracks will be allowed.

## 6. Interruptions and Background Behavior

- **Phone Calls/Alarms:** The Native Audio Adapter will listen for OS audio session interruptions. Upon an interruption (e.g., incoming call), playback will automatically pause.
- **Background Playback:** Core lesson playback must support running in the background. The user can lock the screen and continue listening to the lesson, supporting the audio-first nature of the application.
- **Recording Interruptions:** If a recording session is interrupted by a system event, the recording is discarded, and the user must retry.

## 7. Data Lifecycle and Cleanup

- **Voice Data:** The temporary WAV file created during the recording phase must be deleted as soon as the `IPronunciationEvaluationEngine` has returned a score, or immediately if the user cancels or navigates away. The `IAudioPipelineEngine` adapter is responsible for this cleanup.
