# Accessibility Requirements

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:

- [Software Requirements Specification](software-requirements-specification.md)
- [Stakeholder & User Model](stakeholder-user-model.md)

---

## 1. Purpose

This document defines the accessibility requirements for AudioVerse English. Accessibility is treated as a mandatory product behavior rather than an enhancement, reflecting the core user base of blind and low-vision learners.

## 2. Scope

These requirements cover TalkBack, VoiceOver, WCAG compliance, gesture navigation, high contrast modes, blank screen mode, and audio feedback for both Total Blind and Low Vision user flows.

## 3. Requirements (A11Y)

| ID | Description | Priority | Rationale | Acceptance Criteria |
| --- | --- | --- | --- | --- |
| A11Y-001 | **Screen Reader Compatibility:** All interactive elements, states, and navigation flows must be fully compatible and logically structured for TalkBack (Android) and VoiceOver (iOS). | High | Essential for Total Blind users to operate the app. | The core learning flow (onboarding, lesson selection, playback, recording) can be completed with a screen reader without relying on visual cues. Focus order is logical. |
| A11Y-002 | **Blank Screen Mode:** The app must be fully operable with the screen completely dimmed or turned off (if supported by OS), relying entirely on screen reader and audio cues. | High | Many Total Blind users navigate with their screens off for privacy and battery saving. | Core learning flow is completable with screen off/dimmed while using a screen reader. |
| A11Y-003 | **High Contrast Mode:** The app must support a high contrast visual theme that complies with WCAG 2.1 AAA standards for text and interactive elements. | High | Critical for Low Vision learners who rely on visual interfaces. | High contrast mode can be toggled; contrast ratios for all critical text/UI elements are >= 7:1. |
| A11Y-004 | **Scalable Text:** The application UI must gracefully adapt to OS-level text scaling (up to 200%) without truncating critical information or breaking functionality. | High | Low Vision users often increase system font sizes. | All screens are usable and text is readable at 200% system font size; no overlapping or hidden text. |
| A11Y-005 | **Gesture Navigation Support:** The app must support standard accessibility gesture navigation without conflicting with OS-level screen reader gestures. | High | Users rely on swipe gestures to navigate; custom app gestures must not interfere. | Swiping left/right logically moves focus; no custom gestures block TalkBack/VoiceOver defaults. |
| A11Y-006 | **Audio and Haptic Feedback:** Critical actions (e.g., recording start/stop, error states, success states) must be accompanied by distinct non-intrusive audio and/or haptic cues. | Medium | Provides immediate confirmation without waiting for screen reader speech, reducing cognitive load. | Haptic/audio cues trigger correctly on key interactions; cues can be toggled in settings. |
| A11Y-007 | **Accessible Error Recovery:** Error messages must be announced immediately by the screen reader, clear, non-visual dependent, and provide a straightforward way to recover. | High | Blind users cannot visually scan the screen for error icons or red text. | Screen reader automatically announces errors (e.g., "Microphone permission denied") and focus shifts to the recovery action (e.g., "Open Settings" button). |
| A11Y-008 | **Audio-First Prompts:** Instructions for exercises and navigation should be available via audio, minimizing the need to read long blocks of text with a screen reader. | Medium | Reduces cognitive load and speeds up learning. | Exercise instructions are spoken naturally by the app's audio engine before or alongside screen reader focus. |
