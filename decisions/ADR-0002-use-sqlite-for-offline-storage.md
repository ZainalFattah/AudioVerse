# ADR-0002: Use SQLite for Offline Storage

Project: AudioVerse English
Status: Accepted
Date: 2026-06-28
Decision owners: Jules
Related documents: [Data Architecture and SQLite Strategy](../docs/data-architecture-sqlite-strategy.md), [Software Architecture Document](../docs/software-architecture-document.md)

## Context

AudioVerse English is an offline-first application. User progress, settings, lesson metadata, and potentially cached content must be stored locally on the device and remain accessible without an internet connection.

## Decision

We will use **SQLite** as the primary local database for offline storage.

## Options Considered

| Option | Pros | Cons |
| --- | --- | --- |
| SQLite | Ubiquitous, lightweight, transactional, excellent performance, built into Android/iOS. | Relational model can be rigid, requires schema migrations. |
| Hive / Isar | Fast, NoSQL, native to Dart/Flutter, easy to use. | Less mature ecosystem for complex migrations, potentially higher memory footprint for large datasets. |
| Shared Preferences | Very simple for key-value data. | Inadequate for complex relational data (e.g., progress tracking, lesson metadata). |

## Consequences

- We must design a robust relational schema.
- We must implement a strict migration strategy to prevent data loss during updates.
- We will likely use a Flutter package like `sqflite` or `drift` to interact with SQLite.

## Accessibility Impact

None directly.

## Offline Impact

Critical. SQLite provides the reliable local persistence required for the offline-first architecture.

## Performance Impact

Positive. SQLite is highly optimized for mobile devices and will perform well even on low-end hardware, provided queries and indexes are designed correctly.

## Security and Privacy Impact

Positive. SQLite databases are stored securely within the app's sandboxed file system.

## Follow-Up

- Finalize the database schema and migration strategy.
- Select the specific Flutter SQLite package (`sqflite` vs. `drift`).
