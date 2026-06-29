# ADR-0001: Use Flutter as Primary Frontend

Project: AudioVerse English
Status: Accepted
Date: 2026-06-28
Decision owners: Jules
Related documents: [Jules Master Development Handbook](../jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md), [Software Architecture Document](../docs/software-architecture-document.md)

## Context

AudioVerse English requires a front-end framework capable of running on low-to-mid-range Android and iOS devices. The application requires precise control over audio playback, recording, accessibility APIs (TalkBack/VoiceOver), and offline asset management.

## Decision

We will use **Flutter** as the primary frontend framework for AudioVerse English.

## Options Considered

| Option | Pros | Cons |
| --- | --- | --- |
| Flutter | Single codebase, rich UI ecosystem, strong accessibility support, good performance on low-end devices. | Large initial learning curve, potential bridge overhead for native audio/speech APIs. |
| React Native | Familiar to web developers, large ecosystem, over-the-air updates. | Performance can be inconsistent on low-end Android, accessibility bridges can be fragile. |
| Native (Kotlin/Swift) | Best possible performance and accessibility integration, direct access to audio APIs. | Requires two separate codebases, higher development and maintenance cost. |

## Consequences

- Development will be constrained to the Flutter ecosystem.
- We must rely on Flutter packages (e.g., `just_audio`, `flutter_sound`) for core audio features, requiring careful evaluation of their accessibility and offline capabilities.
- Native plugins will be required for advanced speech recognition integrations (e.g., Vosk, Whisper.cpp).

## Accessibility Impact

Positive. Flutter has strong built-in semantics that map well to TalkBack and VoiceOver, provided developers strictly adhere to accessibility guidelines.

## Offline Impact

Neutral to Positive. Flutter works completely offline by default.

## Performance Impact

Positive. Flutter compiles to native ARM code, offering predictable performance on low-end devices, though we must carefully monitor UI thread blocking.

## Security and Privacy Impact

Neutral. Flutter handles data securely, but we must ensure native plugins do not leak sensitive audio data.

## Follow-Up

- Select and evaluate state management libraries suitable for Flutter.
- Establish Flutter-specific accessibility coding standards.
