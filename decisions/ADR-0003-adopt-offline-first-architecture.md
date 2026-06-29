# ADR-0003: Adopt Offline-First Architecture

Project: AudioVerse English
Status: Accepted
Date: 2026-06-28
Decision owners: Jules
Related documents: [Offline-First Requirements](../docs/offline-first-requirements.md), [Software Architecture Document](../docs/software-architecture-document.md)

## Context

AudioVerse English targets learners who may have limited, intermittent, or expensive internet connectivity. The core learning experience must not be blocked by network availability.

## Decision

We will adopt a strict **offline-first architecture**. The local device (SQLite database and local file system) is the absolute source of truth for the core learning experience. Cloud connectivity is treated as an optional enhancement (e.g., for syncing progress or downloading new content packages) rather than a requirement.

## Options Considered

| Option | Pros | Cons |
| --- | --- | --- |
| Offline-First | Guarantees access to learning, resilient to network drops, fast interaction times. | Complex to build, requires local storage management, sync conflicts must be handled. |
| Cloud-First (Online Only) | Simple to build, single source of truth (cloud). | Fails completely without internet, unacceptable for target users. |
| Cache-First | Easier than full offline, provides some resilience. | Unreliable; cache eviction can cause unexpected data loss, poor user experience offline. |

## Consequences

- All core application logic must read from and write to local storage synchronously.
- Content must be packaged in self-contained, downloadable units (e.g., ZIP files) that do not require online check-ins.
- Any future cloud synchronization must fail gracefully in the background without blocking the user.

## Accessibility Impact

Positive. Fast, reliable interactions reduce cognitive load and prevent unexpected errors when connectivity drops.

## Offline Impact

Critical. This decision defines the fundamental operational mode of the application.

## Performance Impact

Positive. Reading from local SQLite and file system is significantly faster than network requests, ensuring interactive startup times under 3-5 seconds.

## Security and Privacy Impact

Positive. By default, sensitive data (like voice recordings and progress) remains on the device unless explicitly synced.

## Follow-Up

- Define the conflict resolution strategy for the optional sync module.
- Design the content packaging format.
