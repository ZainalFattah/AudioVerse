# Stakeholder and User Model

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:

- docs/project-charter.md
- jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md

## Purpose

The purpose of this document is to define the users, stakeholders, personas, contexts, and accessibility needs for the AudioVerse English platform. This model will guide subsequent requirements engineering and UX design by explicitly detailing the constraints and environmental factors of our target users.

## Stakeholders

- **Primary Users (Learners):** Total Blind and Low Vision individuals learning English.
- **Secondary Users (Caregivers/Teachers):** Individuals who may assist the primary users with device setup, initial installation, or progress monitoring.
- **Engineering Team:** Developers and AI agents (like Jules) responsible for building and maintaining the platform.
- **Content Creators:** Educators and audio engineers designing the structured soundscape lessons and audio assets.

## Learner Personas

### Persona 1: Elena (Total Blind Learner)

- **Profile:** 24 years old, university student, completely blind since birth.
- **Goal:** Improve conversational English for future career opportunities.
- **Tech Setup:** Uses an Android mid-range smartphone (e.g., Samsung Galaxy A-series). Heavy reliance on TalkBack. Uses screen gestures and audio feedback for all navigation.
- **Environment:** Often studies during her commute on public transit or at home.
- **Key Needs:**
  - 100% audio-driven and screen reader-compatible interface.
  - No reliance on visual cues for progression.
  - Ability to pause and resume audio seamlessly when interrupted.
  - Predictable screen focus order and clear audio confirmation of actions.

### Persona 2: Malik (Low Vision Learner)

- **Profile:** 45 years old, professional, has progressive vision loss (e.g., macular degeneration). Can perceive large text and high-contrast shapes.
- **Goal:** Practice English pronunciation for international business calls.
- **Tech Setup:** Uses an older iPhone. Relies on High Contrast Mode, bold text, and scalable UI elements. Occasionally uses VoiceOver when eyes are fatigued.
- **Environment:** Learns mostly at home during evenings.
- **Key Needs:**
  - High contrast interface (WCAG AAA compliance).
  - Support for maximum text scaling without breaking UI layouts.
  - "Blank Screen Mode" or an audio-only mode to conserve battery and reduce eye strain.
  - Large touch targets for interactive elements.

## Secondary Personas

### Persona 3: Sarah (Caregiver/Teacher)

- **Profile:** 35 years old, English teacher at a community center for the visually impaired.
- **Goal:** Help students install the app and track their overall progress if requested.
- **Tech Setup:** Uses standard visual interface on various devices.
- **Key Needs:**
  - Straightforward initial setup and permission granting (microphone, storage) that she can guide students through.
  - [Assumption] While the app is designed for independent use, standard visual UI should remain logically structured so sighted helpers can easily orient themselves if asked to look at the screen.

## Accessibility Contexts and Device Constraints

### Device Baseline

- **Hardware:** Low-to-mid-range smartphones (e.g., 2GB-4GB RAM, limited CPU).
- **Storage:** Limited available storage space, meaning audio content packages must be reasonably sized and manageable.
- **Network:** Intermittent or entirely unavailable internet connectivity, making an offline-first architecture critical.

### Usage Contexts

1. **Audio-First Navigation:** Users will often navigate without looking at the screen. The app must provide immediate, descriptive audio and haptic feedback for all state changes (e.g., "Recording started," "Answer submitted").
2. **Gesture Independence:** The application must not introduce custom multi-finger gestures that override or conflict with standard TalkBack (Android) or VoiceOver (iOS) system gestures.
3. **Environmental Noise:** Users might be in noisy environments (e.g., public transit). Pronunciation assessment and speech recognition must account for background noise, and learners must be able to adjust audio playback volume independently of screen reader volume if possible.

## Review Checklist

- [x] Are personas respectful and useful?
- [x] Are assumptions marked?
- [x] Are Total Blind and Low Vision users represented with realistic contexts?
