# Audio and Soundscape Requirements

Project: AudioVerse English
Status: Final
Version: 1.0

## 1. Introduction

This document defines the requirements for audio playback, audio recording, and the soundscape architecture for AudioVerse English.
Since the application is designed for blind and low-vision users, audio is the primary interface for learning. A "soundscape" in this context refers to a structured, reproducible sequence of audio assets and cues defined by a manifest, explicitly excluding dynamically generated AI audio during core playback to ensure offline reliability and low-end device compatibility.

## 2. Audio Formats and Assets

- **Supported Playback Formats:** All audio assets distributed in content packages must be encoded in `MP3` or `Ogg Vorbis`.
- **Bitrate Constraint:** To balance audio clarity (crucial for pronunciation nuances) with strict storage limits (NFR-004, max 100MB base app + efficient content), speech assets should target 64kbps to 96kbps mono or stereo. High-fidelity ambient sounds may target 128kbps if strictly necessary.
- **Transcripts:** Every speech audio asset MUST have a corresponding textual transcript linkable in the soundscape manifest. This supports Low Vision users who rely on high-contrast text and provides a fallback.

## 3. Soundscape Manifest Structure

A Soundscape is defined by a JSON manifest. This manifest orchestrates the learning experience.

- **Deterministic Sequencing:** The manifest must define an exact, deterministic sequence of audio events (e.g., play intro, wait 2 seconds, play target phrase, trigger recording).
- **Spatial Audio Cues:** The manifest can define basic stereo panning (left/right channels) to create a sense of space or separate different "speakers" in a dialogue, relying on standard stereo audio rather than complex dynamic HRTF (Head-Related Transfer Function) processing to maintain performance on low-end hardware.
- **Timing and Triggers:** The manifest must support explicit timing cues for when to pause for user input and when to automatically resume.
- **Not AI-Generated:** The core soundscapes rely strictly on pre-recorded assets referenced in the JSON manifest. AI generation is explicitly excluded from the standard offline soundscape flow.

## 4. Playback Controls

Audio playback is the core interaction model and must be highly accessible.

- **Granular Control:** The user must be able to Play, Pause, Skip Forward/Backward (e.g., by 10-second intervals), and Skip to Previous/Next logical segment (as defined by the manifest).
- **Speed Adjustment:** The user must be able to adjust playback speed (e.g., 0.5x, 0.75x, 1.0x, 1.25x, 1.5x) without altering the pitch of the speech.
- **Screen Reader Harmony:** Playback controls and state changes must not conflict with TalkBack/VoiceOver. If an audio prompt is playing, the app must manage audio focus properly so that screen reader announcements (if triggered by the user) duck or pause the learning audio.

## 5. Audio Recording Constraints

Recording user speech is required for pronunciation assessment.

- **Temporary Storage:** Audio recorded from the user's microphone must be stored in a temporary, non-persistent directory.
- **Strict Length Limits:** Recordings must be strictly bounded (e.g., maximum 30 seconds per prompt) to prevent accidental storage exhaustion or excessive processing load.
- **Format:** Recorded audio should be captured in a format optimal for the local ASR/Pronunciation engine (e.g., 16kHz, 16-bit PCM WAV) to avoid unnecessary real-time transcoding on low-end devices.
- **Cleanup Rules:** Temporary recording files MUST be aggressively deleted immediately after the pronunciation assessment engine has returned a score, or when the user navigates away from the exercise.

## 6. Tracability

These requirements map to the Software Requirements Specification (SRS) under the `AUD` prefix.
