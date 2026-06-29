# Glossary

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents: docs/domain-model.md, docs/project-charter.md

## Purpose

To establish a shared, unambiguous vocabulary for the AudioVerse English project, ensuring consistency across all documentation, requirements, and future architecture.

## Terms

### AI Tutor

An optional, embedded or cloud-based modular software engine that can provide contextual, natural language assistance or feedback to the learner.
*Note:* Must be explicitly separate from core lesson progression and Speech Recognition.

### Blank Screen Mode

An accessibility and power-saving feature that allows the application to continue running, playing audio, and recording input while the physical device screen is turned off or rendered entirely black.

### Content Package

A downloadable or pre-installed bundle containing all necessary assets (JSON manifests, audio files, transcripts) for one or more Units or Courses, designed to be used entirely offline.

### Course

The highest level of curriculum organization, consisting of multiple sequenced Units.

### Engine

A modular, replaceable software component responsible for a specific complex task (e.g., Speech Recognition Engine, Pronunciation Engine, Audio Engine).

### High Contrast Mode

A visual mode designed for Low Vision learners that adheres to strict WCAG AAA contrast ratios and supports significant text scaling without layout breakage.

### Lesson

A structured learning session within a Unit, typically focused on a specific vocabulary set, grammar concept, or conversational scenario.

### Pronunciation Assessment / Pronunciation Engine

The process of evaluating *how* a learner pronounced a specific, expected phrase, assigning a score or providing feedback.
*Note:* This is strictly separated from Speech Recognition, which only determines *what* words were spoken.

### Soundscape

A structured, audio-first learning scenario defined by a JSON manifest and accompanying audio files (e.g., `plane.wav`, `announcement.wav`). It is NOT dynamically generated AI audio; it is an assembled asset designed to provide immersive context.

### Speech Recognition (ASR) / Speech Recognition Engine

The process of converting spoken audio into text (Speech-to-Text).
*Note:* It only determines *what* was said, it does not assess the *quality* of pronunciation.

### Sync

The optional process of uploading local progress data (e.g., SQLite database records) to a cloud provider (e.g., Supabase) and downloading updates.
*Note:* Sync is never required for core app functionality.

### Transcript

The written text corresponding to spoken audio in a Lesson or Soundscape, used for accessibility, display (for Low Vision learners), and as the expected input for Pronunciation Assessment.

### Unit

A grouping of related Lessons within a Course, usually centered around a broader theme (e.g., "At the Airport", "Introductions").

## Review Checklist

- [x] Are speech recognition and pronunciation assessment separated?
- [x] Is soundscape correctly defined as asset-based?
