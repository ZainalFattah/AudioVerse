# Jules Master Development Handbook

Project: AudioVerse English  
Document type: Master development handbook  
Audience: Jules, AI agents, maintainers, reviewers, and future engineers  
Status: Draft v0.1  
Last updated: 2026-06-28  
Repository mode: Documentation-first  

---

## 1. Purpose

This handbook is the official operating system for building AudioVerse English from zero to production.

AudioVerse English is an audio-first English learning application designed specifically for blind and low-vision learners, including Total Blind and Low Vision users. The application must be offline-first, accessible by default, lightweight enough for low-to-mid-range phones, and engineered so future AI, speech recognition, pronunciation assessment, and sync engines can be replaced without rewriting the whole application.

This document is not a prompt collection. It is a professional software engineering handbook that defines:

- How Jules must work inside this repository.
- What documents must exist before coding begins.
- How requirements become architecture.
- How architecture becomes technical design.
- How technical design becomes prototype, implementation, tests, and production releases.
- What quality gates prevent premature development.
- What acceptance criteria define "done" at every phase.

The primary goal is not only to produce a working app. The primary goal is to create a durable project blueprint that allows an AI agent or human engineer to continue development without losing context, violating accessibility principles, or making undocumented decisions.

---

## 2. Non-Negotiable Principles

### 2.1 Documentation before code

The repository begins as a documentation repository. Jules must not initialize Flutter, generate application source code, or implement features until the documentation quality gates in this handbook explicitly allow it.

### 2.2 Accessibility before aesthetics

AudioVerse English exists for blind and low-vision learners. Accessibility is not an enhancement layer. It is a product requirement, an architecture requirement, and a test requirement.

Every major decision must be evaluated against:

- Total Blind usability.
- Low Vision usability.
- TalkBack and VoiceOver behavior.
- Gesture navigation.
- Audio-first interaction.
- High contrast mode.
- Blank screen mode.
- Cognitive load and error recovery.

### 2.3 Offline-first by default

The application must work meaningfully without internet access. Cloud services may improve synchronization, lesson updates, and content delivery, but must not be required for core learning.

### 2.4 Modular engines, not hard dependencies

The application must not be tightly coupled to a single speech recognition engine, pronunciation engine, AI tutor model, audio engine, or sync provider.

Use interfaces and adapters:

```text
SpeechRecognitionEngine
  -> VoskEngine
  -> WhisperEngine
  -> MLKitEngine

AITutorEngine
  -> LfmTutorEngine
  -> CloudTutorEngine
  -> RuleBasedTutorEngine
```

The same pattern applies to pronunciation assessment, sync, analytics, and content update mechanisms.

### 2.5 Benchmark before binding

Speech recognition, embedded AI, audio processing, sync behavior, storage performance, and battery impact must be benchmarked on target-class devices before final decisions are made.

Vosk and Whisper.cpp must remain candidates until benchmark evidence supports a decision.

### 2.6 Soundscape is content, not AI

Soundscape scenarios are structured audio assets, not generative AI.

Example:

```text
airport/
  airport.json
  plane.wav
  announcement.wav
  people.wav
  luggage.wav
```

Flutter reads the JSON manifest and plays audio layers according to scene rules, timing, and spatial metadata.

### 2.7 Speech recognition is not pronunciation assessment

Speech recognition converts audio to text. Pronunciation assessment evaluates how the learner pronounced the expected speech.

Correct pipeline:

```text
Microphone
  -> Speech Recognition
  -> Pronunciation Evaluation
  -> Scoring
  -> Feedback
```

### 2.8 Decisions must be recorded

Every meaningful technical or product decision must become an ADR in `decisions/`.

Examples:

- Flutter as frontend framework.
- SQLite as offline database.
- Supabase as optional sync candidate.
- Vosk vs Whisper.cpp benchmark outcome.
- Selected state management approach.
- Selected accessibility navigation model.

---

## 3. Repository Contract

### 3.1 Initial repository structure

```text
AudioVerse-English/
  README.md
  docs/
  prototype/
  assets/
  app/
  jules/
  tasks/
  reviews/
  decisions/
```

### 3.2 Folder responsibilities

| Folder | Responsibility |
| --- | --- |
| `README.md` | Public project overview and entry point |
| `docs/` | Formal software engineering documents |
| `prototype/` | Prototype artifacts, wireframes, research output, exported flows |
| `assets/` | Non-code planning assets, sample audio manifests, content design artifacts |
| `app/` | Future Flutter application, locked until the implementation gate |
| `jules/` | Jules handbook, prompt library, agent operating rules |
| `tasks/` | Phase task plans, execution logs, backlog slices |
| `reviews/` | Repository reviews, documentation reviews, quality reviews |
| `decisions/` | Architecture Decision Records |

### 3.3 Locked folders

Until Phase 47 is passed:

- `app/` must not contain Flutter source code.
- No `pubspec.yaml` may be created.
- No generated Flutter project may be committed.
- Prototype code may exist only when explicitly approved by prototype phases and must stay outside `app/`.

---

## 4. Jules Operating Model

Jules must behave like a newly onboarded software engineer working inside a professional engineering organization.

### 4.1 Standard workflow

For every phase, Jules must:

1. Read the repository.
2. Read relevant documentation.
3. Summarize current state.
4. Identify gaps.
5. Propose the phase work.
6. Create or update documentation.
7. Run a self-review.
8. Record decisions where needed.
9. Commit changes when requested or when the workflow requires it.
10. Re-read the repository before moving to the next phase.

### 4.2 Work rules

Jules must:

- Never skip the phase quality gate.
- Never implement application code before the implementation gate.
- Never hide assumptions.
- Never silently overwrite existing documentation.
- Keep changes small enough to review.
- Prefer explicit markdown documents over chat-only decisions.
- Link documents to each other.
- Maintain traceability from requirements to design, tests, and release.
- Record unresolved questions in a visible document.

### 4.3 Phase output format

At the end of each phase, Jules must produce:

```text
Phase:
Status:
Documents created or changed:
Key decisions:
Open questions:
Risks:
Quality gate result:
Recommended next phase:
```

### 4.4 Commit convention

Use clear, phase-based commit messages:

```text
docs(phase-04): add initial SRS
docs(adr): record offline-first storage decision
docs(accessibility): define TalkBack interaction model
prototype(phase-43): add low-fidelity flow plan
```

---

## 5. Documentation Standards

### 5.1 Required metadata

Every formal document must begin with:

```markdown
# Document Title

Project: AudioVerse English
Status: Draft | Review | Approved | Deprecated
Owner:
Reviewers:
Last updated: YYYY-MM-DD
Related documents:
```

### 5.2 Required sections for formal documents

Unless there is a strong reason, formal documents should include:

- Purpose
- Scope
- Audience
- Definitions
- Assumptions
- Requirements or design content
- Risks
- Open questions
- Acceptance criteria
- Review checklist
- Change log

### 5.3 Requirement IDs

Requirements must use stable IDs.

Recommended prefixes:

| Prefix | Meaning |
| --- | --- |
| `FR` | Functional Requirement |
| `NFR` | Non-Functional Requirement |
| `A11Y` | Accessibility Requirement |
| `OFF` | Offline Requirement |
| `AUD` | Audio Requirement |
| `SR` | Speech Recognition Requirement |
| `PRON` | Pronunciation Requirement |
| `AI` | AI Tutor Requirement |
| `DATA` | Data Requirement |
| `SEC` | Security Requirement |
| `SYNC` | Sync Requirement |
| `TEST` | Test Requirement |

Example:

```text
A11Y-001: The learner must be able to complete the core lesson flow using TalkBack without visual assistance.
```

### 5.4 Decision IDs

ADRs must use stable IDs:

```text
ADR-0001-select-flutter-as-primary-frontend.md
ADR-0002-use-sqlite-for-offline-storage.md
ADR-0003-keep-speech-engine-pluggable.md
```

### 5.5 Traceability

Every major feature must be traceable:

```text
Requirement -> Architecture component -> Technical design -> Test case -> Release criterion
```

No MVP feature should be implemented unless it is traceable to an approved requirement.

---

## 6. Required Document Set

The project should eventually contain these documents.

| Document | Target folder | Purpose |
| --- | --- | --- |
| Project Charter | `docs/` | Defines mission, scope, stakeholders, constraints |
| Vision Review | `docs/` | Reviews the original product vision against engineering needs |
| SRS | `docs/` | Defines product requirements |
| SAD | `docs/` | Defines software architecture |
| ADRs | `decisions/` | Records binding decisions |
| Technical Design Document | `docs/` | Defines detailed implementation design |
| Database Design Document | `docs/` | Defines SQLite schema, migrations, data rules |
| Accessibility Guideline | `docs/` | Defines accessibility requirements and patterns |
| UI/UX Guideline | `docs/` | Defines product interaction model |
| AI Design Document | `docs/` | Defines AI Tutor architecture and constraints |
| Speech Benchmark Plan | `docs/` | Defines Vosk vs Whisper.cpp benchmark method |
| Pronunciation Design Document | `docs/` | Defines scoring and feedback approach |
| Audio and Soundscape Design | `docs/` | Defines audio assets, manifests, playback rules |
| Testing Guideline | `docs/` | Defines test strategy and quality gates |
| Deployment Guideline | `docs/` | Defines release, store, signing, rollback |
| Project Management Plan | `docs/` | Defines phases, milestones, roles, risks |
| Jules Prompt Library | `jules/` | Provides controlled prompts for phase execution |

---

## 7. Product and Technical Baseline

### 7.1 Product baseline

AudioVerse English must support:

- Audio-first English learning.
- Blind and low-vision user journeys.
- Offline lesson access.
- Progress tracking.
- Audio playback.
- Recording and speaking practice.
- Pronunciation feedback.
- Optional AI tutor assistance.
- Optional cloud sync.
- Structured soundscape lessons.

### 7.2 Preliminary technology decisions

| Area | Current direction | Decision status |
| --- | --- | --- |
| Frontend | Flutter | Provisional, needs ADR |
| Local database | SQLite | Provisional, needs ADR |
| Cloud sync | Supabase | Optional, needs ADR |
| Audio playback | `just_audio` | Candidate |
| Recording | `flutter_sound` | Candidate |
| Haptic | `flutter_vibration` | Candidate |
| Speech recognition | Vosk or Whisper.cpp | Benchmark required |
| Embedded AI tutor | LFM-style small model or alternative | Research required |
| Accessibility | TalkBack, VoiceOver, WCAG | Mandatory |

### 7.3 Recommended high-level pipeline

```text
User
  -> Flutter App
  -> Audio Engine
  -> Speech Recognition Engine
  -> Pronunciation Engine
  -> Feedback Generator
  -> SQLite
  -> Optional Sync

AI Tutor Engine runs as a separate module.
```

### 7.4 Conceptual module boundaries

```text
Presentation Layer
  -> Accessibility Interaction Layer
  -> Lesson Flow Layer
  -> Audio Orchestration Layer
  -> Speech Adapter Layer
  -> Pronunciation Evaluation Layer
  -> AI Tutor Adapter Layer
  -> Local Data Layer
  -> Optional Sync Layer
```

---

## 8. Quality Gates

### 8.1 Gate A: Foundation complete

Required before detailed requirements:

- Repository structure exists.
- Handbook exists.
- Project Charter exists.
- Vision Review exists.
- Initial risk register exists.
- Documentation standards are accepted.

### 8.2 Gate B: Requirements complete

Required before architecture:

- SRS approved.
- Accessibility requirements approved.
- Offline-first requirements approved.
- Audio and speech requirements approved.
- AI tutor requirements scoped.
- Requirements Traceability Matrix created.

### 8.3 Gate C: Architecture complete

Required before technical design:

- SAD approved.
- Core module boundaries defined.
- Engine interfaces defined conceptually.
- Storage and sync strategy documented.
- Key ADRs created.
- Known technical risks documented.

### 8.4 Gate D: Technical design complete

Required before UI prototype and implementation planning:

- Technical Design Document approved.
- Database design approved.
- State management strategy selected.
- Permission and privacy flows designed.
- Audio, speech, pronunciation, and AI boundaries defined.

### 8.5 Gate E: UX and prototype complete

Required before Flutter initialization:

- Accessibility interaction model approved.
- Core flows documented.
- Prototype reviewed with accessibility criteria.
- MVP scope frozen.
- Usability test plan exists.

### 8.6 Gate F: Engineering readiness

Required before creating Flutter project:

- Development guideline approved.
- Coding standard approved.
- Testing guideline approved.
- CI plan approved.
- Release strategy drafted.
- Backlog sliced into implementable MVP tasks.

### 8.7 Gate G: MVP release readiness

Required before beta:

- MVP features implemented.
- Accessibility tests passed.
- Offline behavior verified.
- Performance baseline measured.
- Speech engine benchmark completed or deferred with explicit reason.
- Privacy review completed.

### 8.8 Gate H: Production readiness

Required before production:

- Beta feedback reviewed.
- Critical defects resolved.
- Store assets prepared.
- Rollback plan documented.
- Content update process documented.
- Maintenance plan approved.

---

## 9. Roadmaps

### 9.1 Documentation roadmap

1. Foundation documents.
2. Requirements documents.
3. Architecture documents.
4. Technical design documents.
5. Accessibility and UX documents.
6. Prototype documents.
7. Development standards.
8. Testing and deployment documents.

### 9.2 Prototype roadmap

1. Audio-only lesson journey map.
2. TalkBack and VoiceOver interaction script.
3. Low-fidelity flow diagrams.
4. High contrast visual wireframes for Low Vision users.
5. Blank screen mode behavior definition.
6. Soundscape manifest prototype.
7. Recording and feedback flow prototype.
8. Usability test protocol.

### 9.3 MVP roadmap

MVP should include:

- Onboarding with accessibility preferences.
- Offline lesson catalog.
- Audio lesson playback.
- Basic progress tracking in SQLite.
- Speaking practice recording.
- Pluggable speech recognition adapter with one selected implementation or mock.
- Basic pronunciation scoring prototype.
- Basic feedback generation.
- High contrast mode.
- Blank screen mode.
- TalkBack and VoiceOver compatible navigation.

MVP should exclude unless explicitly approved:

- Complex social features.
- Full cloud dependency.
- Generative soundscape creation.
- Heavy embedded AI without benchmark evidence.
- Advanced gamification that distracts from accessibility.

### 9.4 Production roadmap

Production requires:

- Stable offline content packaging.
- Store-ready privacy disclosures.
- Accessibility conformance evidence.
- Crash and error reporting strategy.
- Content update strategy.
- Device compatibility matrix.
- Performance benchmark on low-end devices.
- Security review.
- Support and maintenance process.

---

## 10. Jules Prompt Template

Use this template for all phase prompts:

```text
You are Jules, acting as a software engineer for AudioVerse English.

Before doing any work:
1. Read README.md.
2. Read jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md.
3. Read all documents relevant to this phase.
4. Inspect the repository structure.

Current phase:
[PHASE NAME]

Objective:
[OBJECTIVE]

Rules:
- Do not create Flutter app code unless this phase explicitly allows it.
- Preserve documentation-first workflow.
- Record assumptions.
- Add ADRs for binding decisions.
- Update review notes and open questions.

Deliverables:
[DELIVERABLES]

Acceptance criteria:
[ACCEPTANCE CRITERIA]

At the end, provide:
- Summary
- Files changed
- Decisions made
- Risks
- Open questions
- Quality gate status
- Recommended next phase
```

---

## 11. Review Template

Use this template for reviews:

```markdown
# Review: [Document or Phase]

Project: AudioVerse English
Reviewer:
Date:
Scope:

## Summary

## Findings

| Severity | Finding | Evidence | Recommendation |
| --- | --- | --- | --- |

## Missing Information

## Accessibility Concerns

## Technical Risks

## Requirement Traceability Issues

## Decision Gaps

## Approval Status

- [ ] Approved
- [ ] Approved with changes
- [ ] Changes required

## Required Follow-Up
```

---

## 12. ADR Template

```markdown
# ADR-0000: Decision Title

Project: AudioVerse English
Status: Proposed | Accepted | Superseded | Rejected
Date:
Decision owners:
Related documents:

## Context

## Decision

## Options Considered

| Option | Pros | Cons |
| --- | --- | --- |

## Consequences

## Accessibility Impact

## Offline Impact

## Performance Impact

## Security and Privacy Impact

## Follow-Up
```

---

## 13. Engineering Standards for Future Code

These standards become active only when the implementation gate permits code.

### 13.1 Flutter standards

- Use clear feature modules.
- Keep UI, domain, data, and infrastructure concerns separated.
- Treat accessibility semantics as required code, not optional attributes.
- Avoid business logic inside widgets.
- Use dependency injection for replaceable engines.
- Keep offline storage behind repository interfaces.
- Write tests for core logic before broad UI expansion.

### 13.2 Accessibility coding standards

Future code must:

- Provide semantic labels for interactive controls.
- Support TalkBack and VoiceOver focus order.
- Avoid visual-only instructions.
- Provide audio and haptic confirmation for critical actions where appropriate.
- Support scalable text.
- Support high contrast mode.
- Support blank screen mode.
- Avoid gesture conflicts with screen readers.

### 13.3 Data standards

- SQLite schema changes require migrations.
- Local progress must be durable offline.
- Sync must be conflict-aware.
- Content packages must be versioned.
- User data must be separated from downloadable lesson content.

### 13.4 AI and speech standards

- AI tutor and speech engines must be replaceable.
- Models must be benchmarked before production use.
- AI output must not be treated as authoritative without guardrails.
- Pronunciation feedback must distinguish confidence, recognition errors, and actual pronunciation issues.

---

## 14. Testing Standards

Testing must cover:

- Unit tests for domain logic.
- Repository tests for SQLite behavior.
- Adapter tests for speech and AI interfaces.
- Integration tests for lesson flows.
- Accessibility tests with screen readers.
- Offline tests with network disabled.
- Performance tests on low-to-mid-range devices.
- Battery and memory checks for audio and speech features.
- Regression tests for content package updates.

No release should proceed if the core learning flow cannot be completed without sight.

---

## 15. Security, Privacy, and Data Protection

AudioVerse English may process sensitive voice recordings. The project must document:

- Whether recordings are stored.
- Where recordings are stored.
- When recordings are deleted.
- Whether recordings ever leave the device.
- Whether cloud sync includes voice data.
- How learners can reset or delete local progress.
- What data Supabase may store if sync is enabled.
- What happens when the user is offline.

Default policy: keep voice processing local unless a documented and approved reason exists.

---

## 16. Phase Master Plan

This project uses 60 phases. Each phase is intentionally small enough for Jules to complete, review, and commit without losing context.

### Part I: Foundation

#### Phase 01 - Repository Review

- Objective: Inspect the repository and establish the current baseline.
- Deliverables: `reviews/phase-01-repository-review.md`.
- Acceptance Criteria: The review lists existing files, missing folders, current risks, and whether app code exists.
- Checklist: Read root files; inspect folders; check git status; identify documentation gaps.
- Jules Prompt: "Review the repository baseline for AudioVerse English. Do not create application code. Produce a repository review and list missing documentation."
- Review Checklist: Is the repository state accurate? Are gaps concrete? Are no code changes made?
- Quality Gate: Pass when baseline is documented.
- Definition of Done: Review file exists and next phase recommendation is clear.
- Next Phase: Phase 02 - Project Charter.

#### Phase 02 - Project Charter

- Objective: Define mission, scope, stakeholders, success criteria, constraints, and non-goals.
- Deliverables: `docs/project-charter.md`.
- Acceptance Criteria: Charter clearly explains why the project exists and what it will not attempt in early versions.
- Checklist: Define users; define problem; define goals; define non-goals; define constraints.
- Jules Prompt: "Create the Project Charter for AudioVerse English using the handbook and repository context."
- Review Checklist: Are blind and low-vision users central? Is offline-first explicit? Are constraints realistic?
- Quality Gate: Pass when charter can guide requirement writing.
- Definition of Done: Charter is review-ready and linked from docs index.
- Next Phase: Phase 03 - Vision Review.

#### Phase 03 - Vision Review

- Objective: Convert the existing product vision into engineering implications.
- Deliverables: `docs/vision-review.md`.
- Acceptance Criteria: Vision assumptions, gaps, risks, and engineering needs are identified.
- Checklist: Summarize vision; separate product intent from requirements; identify missing technical details.
- Jules Prompt: "Review the AudioVerse English vision as an engineer. Identify what is usable, missing, risky, and required before SRS."
- Review Checklist: Does it avoid inventing unapproved features? Does it expose ambiguity?
- Quality Gate: Pass when vision gaps are actionable.
- Definition of Done: Review includes recommendations for SRS.
- Next Phase: Phase 04 - Stakeholder and User Model.

#### Phase 04 - Stakeholder and User Model

- Objective: Define users, stakeholders, personas, contexts, and accessibility needs.
- Deliverables: `docs/stakeholder-user-model.md`.
- Acceptance Criteria: Total Blind and Low Vision users are represented with realistic contexts.
- Checklist: Define learner personas; define caregiver or teacher roles if relevant; define device constraints; define accessibility contexts.
- Jules Prompt: "Create a stakeholder and user model for an audio-first English learning app for blind and low-vision learners."
- Review Checklist: Are personas respectful and useful? Are assumptions marked?
- Quality Gate: Pass when requirements can reference user groups.
- Definition of Done: User model is linked from SRS preparation notes.
- Next Phase: Phase 05 - Glossary and Domain Model.

#### Phase 05 - Glossary and Domain Model

- Objective: Establish shared vocabulary for lessons, units, soundscapes, speech, pronunciation, AI tutor, and sync.
- Deliverables: `docs/glossary.md`, `docs/domain-model.md`.
- Acceptance Criteria: Core terms are defined unambiguously.
- Checklist: Define lesson; course; exercise; attempt; transcript; pronunciation score; soundscape; engine; content package.
- Jules Prompt: "Create a glossary and initial domain model for AudioVerse English. Focus on terms needed by requirements and architecture."
- Review Checklist: Are speech recognition and pronunciation assessment separated? Is soundscape correctly defined as asset-based?
- Quality Gate: Pass when future documents can use stable terminology.
- Definition of Done: Glossary contains term, definition, notes, and related terms.
- Next Phase: Phase 06 - Documentation Inventory and Gap Analysis.

#### Phase 06 - Documentation Inventory and Gap Analysis

- Objective: Identify all documents needed and current completion status.
- Deliverables: `docs/documentation-inventory.md`.
- Acceptance Criteria: Inventory maps required documents to status, owner, location, and dependency.
- Checklist: List required docs; mark missing docs; rank by dependency; identify blockers.
- Jules Prompt: "Create a documentation inventory and gap analysis for AudioVerse English based on the handbook."
- Review Checklist: Is the inventory complete? Are dependencies logical?
- Quality Gate: Pass when the documentation backlog is visible.
- Definition of Done: Missing documents are prioritized.
- Next Phase: Phase 07 - Risk Register v1.

#### Phase 07 - Risk Register v1

- Objective: Create the initial project risk register.
- Deliverables: `docs/risk-register.md`.
- Acceptance Criteria: Risks include accessibility, offline, speech accuracy, performance, privacy, content, and AI uncertainty.
- Checklist: Identify risks; rate probability and impact; define mitigation; assign owner.
- Jules Prompt: "Create the initial risk register for AudioVerse English."
- Review Checklist: Are risks specific? Are mitigations actionable?
- Quality Gate: Pass when major unknowns are visible.
- Definition of Done: Risk register is ready for regular updates.
- Next Phase: Phase 08 - SRS Framework.

### Part II: Requirements Engineering

#### Phase 08 - SRS Framework

- Objective: Create the Software Requirements Specification structure.
- Deliverables: `docs/software-requirements-specification.md`.
- Acceptance Criteria: SRS has sections, ID conventions, requirement categories, and traceability placeholders.
- Checklist: Add metadata; define scope; define requirement taxonomy; prepare tables.
- Jules Prompt: "Create the SRS framework for AudioVerse English. Do not fill unsupported requirements without marking assumptions."
- Review Checklist: Is the SRS ready to receive detailed requirements?
- Quality Gate: Pass when SRS structure is stable.
- Definition of Done: SRS skeleton exists with requirement ID standards.
- Next Phase: Phase 09 - Functional Requirements.

#### Phase 09 - Functional Requirements

- Objective: Define functional requirements for core product behavior.
- Deliverables: Updated SRS.
- Acceptance Criteria: Core flows include onboarding, lesson catalog, playback, practice, feedback, progress, settings, and offline access.
- Checklist: Add FR IDs; define priority; define rationale; define acceptance criteria.
- Jules Prompt: "Add functional requirements for AudioVerse English based on the project charter, user model, and glossary."
- Review Checklist: Are requirements testable? Are features not over-scoped?
- Quality Gate: Pass when functional scope is coherent.
- Definition of Done: Each FR has ID, description, priority, and acceptance criteria.
- Next Phase: Phase 10 - Non-Functional Requirements.

#### Phase 10 - Non-Functional Requirements

- Objective: Define performance, reliability, compatibility, maintainability, privacy, and usability requirements.
- Deliverables: Updated SRS.
- Acceptance Criteria: NFRs are measurable or reviewable.
- Checklist: Add device targets; define startup expectations; define offline reliability; define maintainability.
- Jules Prompt: "Add non-functional requirements for AudioVerse English with special attention to low-end devices and offline reliability."
- Review Checklist: Are NFRs measurable? Are performance assumptions marked for benchmark?
- Quality Gate: Pass when NFRs can guide architecture.
- Definition of Done: NFRs have IDs, metrics where possible, and validation method.
- Next Phase: Phase 11 - Accessibility Requirements.

#### Phase 11 - Accessibility Requirements

- Objective: Define accessibility requirements as first-class requirements.
- Deliverables: `docs/accessibility-requirements.md`, updated SRS.
- Acceptance Criteria: TalkBack, VoiceOver, WCAG, gesture navigation, high contrast, blank screen mode, and audio feedback are covered.
- Checklist: Define Total Blind flow; define Low Vision flow; define screen reader behavior; define error recovery.
- Jules Prompt: "Create accessibility requirements for AudioVerse English. Treat accessibility as mandatory product behavior."
- Review Checklist: Can the core lesson flow be completed without sight? Are low-vision needs included?
- Quality Gate: Pass when accessibility requirements can drive UX and tests.
- Definition of Done: A11Y requirements are linked into SRS.
- Next Phase: Phase 12 - Offline-First Requirements.

#### Phase 12 - Offline-First Requirements

- Objective: Define offline behavior and optional sync expectations.
- Deliverables: `docs/offline-first-requirements.md`, updated SRS.
- Acceptance Criteria: Core app behavior works without internet; sync is optional and failure-tolerant.
- Checklist: Define local content; define local progress; define sync boundaries; define conflict scenarios.
- Jules Prompt: "Define offline-first requirements for AudioVerse English and separate mandatory offline behavior from optional cloud sync."
- Review Checklist: Are cloud assumptions avoided? Is offline mode complete?
- Quality Gate: Pass when architecture can preserve offline-first behavior.
- Definition of Done: OFF and SYNC requirements are traceable.
- Next Phase: Phase 13 - Content and Curriculum Requirements.

#### Phase 13 - Content and Curriculum Requirements

- Objective: Define lesson content, curriculum units, metadata, and content packaging requirements.
- Deliverables: `docs/content-curriculum-requirements.md`, updated SRS.
- Acceptance Criteria: Content package structure and metadata needs are clear.
- Checklist: Define lesson structure; define levels; define transcripts; define downloadable packages; define versioning.
- Jules Prompt: "Create content and curriculum requirements for an audio-first English learning app."
- Review Checklist: Are content requirements compatible with offline packages?
- Quality Gate: Pass when content can be designed independently from app code.
- Definition of Done: Content requirements are version-aware and accessible.
- Next Phase: Phase 14 - Audio and Soundscape Requirements.

#### Phase 14 - Audio and Soundscape Requirements

- Objective: Define playback, recording, soundscape, audio assets, and spatial audio requirements.
- Deliverables: `docs/audio-soundscape-requirements.md`, updated SRS.
- Acceptance Criteria: Soundscape is specified as asset-driven JSON plus audio files.
- Checklist: Define audio formats; define manifest needs; define playback controls; define recording constraints.
- Jules Prompt: "Create audio and soundscape requirements. Make clear that soundscape is structured audio content, not AI-generated output."
- Review Checklist: Are audio behaviors testable? Are accessibility cues included?
- Quality Gate: Pass when audio architecture can be designed.
- Definition of Done: AUD requirements are linked to SRS.
- Next Phase: Phase 15 - Speech and Pronunciation Requirements.

#### Phase 15 - Speech and Pronunciation Requirements

- Objective: Define speech recognition and pronunciation assessment requirements separately.
- Deliverables: `docs/speech-pronunciation-requirements.md`, updated SRS.
- Acceptance Criteria: Speech-to-text and pronunciation scoring are clearly separated.
- Checklist: Define expected input; define recognition output; define scoring; define feedback; define benchmark needs.
- Jules Prompt: "Create speech recognition and pronunciation requirements. Keep ASR separate from pronunciation assessment."
- Review Checklist: Does the document avoid treating ASR as pronunciation scoring?
- Quality Gate: Pass when speech benchmark and pronunciation design can proceed.
- Definition of Done: SR and PRON requirements are traceable.
- Next Phase: Phase 16 - AI Tutor Requirements.

#### Phase 16 - AI Tutor Requirements

- Objective: Define AI Tutor scope, constraints, safety, offline feasibility, and interaction boundaries.
- Deliverables: `docs/ai-tutor-requirements.md`, updated SRS.
- Acceptance Criteria: AI Tutor is optional, modular, and not confused with ASR.
- Checklist: Define use cases; define non-goals; define safety; define fallback; define benchmark needs.
- Jules Prompt: "Create AI Tutor requirements for AudioVerse English. Treat embedded AI as a separate module from speech recognition."
- Review Checklist: Are AI claims evidence-based? Are low-end device constraints included?
- Quality Gate: Pass when AI architecture can be modular.
- Definition of Done: AI requirements are scoped and risk-tagged.
- Next Phase: Phase 17 - Security, Privacy, and Data Requirements.

#### Phase 17 - Security, Privacy, and Data Requirements

- Objective: Define data protection requirements for voice, progress, profiles, content, and sync.
- Deliverables: `docs/security-privacy-data-requirements.md`, updated SRS.
- Acceptance Criteria: Voice recording policy and offline data handling are explicit.
- Checklist: Define data types; define storage; define deletion; define permissions; define cloud boundaries.
- Jules Prompt: "Create security, privacy, and data requirements for AudioVerse English, especially for learner voice recordings."
- Review Checklist: Are sensitive data flows visible? Are local-first defaults clear?
- Quality Gate: Pass when architecture can protect data.
- Definition of Done: SEC and DATA requirements are traceable.
- Next Phase: Phase 18 - Requirements Traceability Matrix.

#### Phase 18 - Requirements Traceability Matrix

- Objective: Create the initial traceability matrix.
- Deliverables: `docs/requirements-traceability-matrix.md`.
- Acceptance Criteria: Requirements map to planned architecture, tests, priority, and status.
- Checklist: Include all requirement IDs; add source; add planned validation; add owner.
- Jules Prompt: "Create a requirements traceability matrix for AudioVerse English."
- Review Checklist: Are all requirement categories represented? Are gaps visible?
- Quality Gate: Gate B passes only when this matrix exists and is usable.
- Definition of Done: Requirements phase is ready for architecture.
- Next Phase: Phase 19 - Architecture Principles and Constraints.

### Part III: Software Architecture

#### Phase 19 - Architecture Principles and Constraints

- Objective: Define architecture principles, constraints, and tradeoff rules.
- Deliverables: `docs/architecture-principles.md`.
- Acceptance Criteria: Principles cover accessibility, offline-first, modular engines, performance, privacy, and testability.
- Checklist: Define constraints; define tradeoffs; define unacceptable designs.
- Jules Prompt: "Create architecture principles and constraints for AudioVerse English."
- Review Checklist: Do principles prevent premature coupling to engines?
- Quality Gate: Pass when SAD can be written from these principles.
- Definition of Done: Principles are approved for use in architecture.
- Next Phase: Phase 20 - Software Architecture Document.

#### Phase 20 - Software Architecture Document

- Objective: Create the main Software Architecture Document.
- Deliverables: `docs/software-architecture-document.md`.
- Acceptance Criteria: SAD defines system context, layers, modules, data flow, deployment assumptions, and quality attributes.
- Checklist: Add diagrams; define module responsibilities; define boundaries; link requirements.
- Jules Prompt: "Create the Software Architecture Document for AudioVerse English using the approved requirements and principles."
- Review Checklist: Is architecture understandable without source code? Are requirements traceable?
- Quality Gate: Pass when architecture can drive technical design.
- Definition of Done: SAD is review-ready with diagrams and open questions.
- Next Phase: Phase 21 - Modular Engine Interface Architecture.

#### Phase 21 - Modular Engine Interface Architecture

- Objective: Define conceptual interfaces for speech, pronunciation, AI tutor, audio, and sync engines.
- Deliverables: `docs/modular-engine-architecture.md`.
- Acceptance Criteria: Engines are replaceable through clear contracts.
- Checklist: Define interfaces; define adapter responsibilities; define failure behavior; define testing strategy.
- Jules Prompt: "Design the modular engine architecture for replaceable ASR, pronunciation, AI Tutor, audio, and sync components."
- Review Checklist: Can Vosk, Whisper.cpp, ML Kit, or future engines be swapped?
- Quality Gate: Pass when no engine is hard-bound in architecture.
- Definition of Done: Interface contracts are documented at conceptual level.
- Next Phase: Phase 22 - Data Architecture and SQLite Strategy.

#### Phase 22 - Data Architecture and SQLite Strategy

- Objective: Define local data architecture and SQLite responsibilities.
- Deliverables: `docs/data-architecture-sqlite-strategy.md`.
- Acceptance Criteria: Data domains, persistence rules, migrations, and backup considerations are clear.
- Checklist: Define entities; define local-only data; define synced data; define migration policy.
- Jules Prompt: "Create the data architecture and SQLite strategy for AudioVerse English."
- Review Checklist: Does the design support offline-first and future sync?
- Quality Gate: Pass when database design can begin.
- Definition of Done: Data architecture is linked to requirements.
- Next Phase: Phase 23 - Optional Sync Architecture.

#### Phase 23 - Optional Sync Architecture

- Objective: Define optional Supabase sync architecture without making cloud mandatory.
- Deliverables: `docs/optional-sync-architecture.md`.
- Acceptance Criteria: Sync boundaries, conflicts, retries, and offline failure behavior are documented.
- Checklist: Define syncable entities; define conflict strategy; define user consent; define fallback.
- Jules Prompt: "Design optional sync architecture for AudioVerse English using Supabase as a candidate while preserving offline-first behavior."
- Review Checklist: Can the app still work when sync is disabled?
- Quality Gate: Pass when sync cannot break offline use.
- Definition of Done: Sync remains optional and documented.
- Next Phase: Phase 24 - Audio Pipeline Architecture.

#### Phase 24 - Audio Pipeline Architecture

- Objective: Define playback, recording, soundscape, and audio session architecture.
- Deliverables: `docs/audio-pipeline-architecture.md`.
- Acceptance Criteria: Audio responsibilities and lifecycle are clear.
- Checklist: Define playback; define recording; define interruptions; define background behavior; define soundscape mixing.
- Jules Prompt: "Create the audio pipeline architecture for playback, recording, and structured soundscapes."
- Review Checklist: Are screen reader audio conflicts considered?
- Quality Gate: Pass when audio design can be implemented later.
- Definition of Done: Audio architecture links to AUD requirements.
- Next Phase: Phase 25 - Speech Recognition Benchmark Plan.

#### Phase 25 - Speech Recognition Benchmark Plan

- Objective: Define benchmark method for Vosk vs Whisper.cpp and other candidates.
- Deliverables: `docs/speech-recognition-benchmark-plan.md`.
- Acceptance Criteria: Metrics, datasets, target devices, and decision criteria are documented.
- Checklist: Define accuracy metrics; define latency; define memory; define battery; define device matrix.
- Jules Prompt: "Create a benchmark plan for speech recognition engines for AudioVerse English."
- Review Checklist: Are low-to-mid-range devices represented? Is decision evidence-based?
- Quality Gate: Pass when engine choice can be deferred responsibly.
- Definition of Done: Benchmark plan defines how to choose ASR later.
- Next Phase: Phase 26 - Pronunciation Evaluation Architecture.

#### Phase 26 - Pronunciation Evaluation Architecture

- Objective: Define pronunciation assessment architecture and feedback pipeline.
- Deliverables: `docs/pronunciation-evaluation-architecture.md`.
- Acceptance Criteria: The design separates recognition, scoring, confidence, and feedback.
- Checklist: Define expected text; define audio features; define scoring; define feedback; define uncertainty handling.
- Jules Prompt: "Create pronunciation evaluation architecture for AudioVerse English, separate from speech recognition."
- Review Checklist: Are false positives and recognition uncertainty considered?
- Quality Gate: Pass when pronunciation prototype can be planned.
- Definition of Done: PRON requirements are mapped to architecture.
- Next Phase: Phase 27 - AI Tutor Architecture.

#### Phase 27 - AI Tutor Architecture

- Objective: Define AI Tutor module architecture and safety constraints.
- Deliverables: `docs/ai-tutor-architecture.md`.
- Acceptance Criteria: AI tutor is modular, optional, benchmarked, and bounded.
- Checklist: Define tutor interface; define local and cloud candidates; define guardrails; define fallback mode.
- Jules Prompt: "Create AI Tutor architecture for AudioVerse English with replaceable model implementations."
- Review Checklist: Is AI Tutor separated from ASR and core lesson flow?
- Quality Gate: Pass when AI can be added without blocking MVP.
- Definition of Done: AI architecture includes risks and non-goals.
- Next Phase: Phase 28 - Soundscape Asset Architecture.

#### Phase 28 - Soundscape Asset Architecture

- Objective: Define structured soundscape asset format and playback rules.
- Deliverables: `docs/soundscape-asset-architecture.md`.
- Acceptance Criteria: JSON manifest structure and audio asset responsibilities are described.
- Checklist: Define scene; define layers; define timing; define spatial metadata; define accessibility cues.
- Jules Prompt: "Design the soundscape asset architecture using JSON manifests and audio files."
- Review Checklist: Is AI generation excluded from core soundscape design?
- Quality Gate: Pass when content creators can prepare soundscapes.
- Definition of Done: Example manifest is included as documentation, not app code.
- Next Phase: Phase 29 - Error Handling and Observability Architecture.

#### Phase 29 - Error Handling and Observability Architecture

- Objective: Define error handling, logging, diagnostics, and user-facing recovery.
- Deliverables: `docs/error-handling-observability-architecture.md`.
- Acceptance Criteria: Failures are accessible, recoverable, and privacy-conscious.
- Checklist: Define error taxonomy; define accessible messages; define logging boundaries; define crash reporting options.
- Jules Prompt: "Create error handling and observability architecture for an offline-first accessible app."
- Review Checklist: Can blind users recover from errors without visual cues?
- Quality Gate: Pass when failure behavior is predictable.
- Definition of Done: Error strategy links to UX and testing.
- Next Phase: Phase 30 - ADR Backlog and Decision Register.

#### Phase 30 - ADR Backlog and Decision Register

- Objective: Create ADRs for accepted decisions and backlog for unresolved decisions.
- Deliverables: `decisions/ADR-0001-...`, `docs/decision-register.md`.
- Acceptance Criteria: Flutter, SQLite, offline-first, modular engines, and benchmark deferrals are recorded.
- Checklist: Create ADRs; mark provisional decisions; identify pending decisions.
- Jules Prompt: "Create the initial ADR set and decision register for AudioVerse English."
- Review Checklist: Are decisions justified and consequences documented?
- Quality Gate: Gate C passes when core architecture decisions are visible.
- Definition of Done: Architecture phase is ready for technical design.
- Next Phase: Phase 31 - Technical Design Document Standard.

### Part IV: Technical Design and System Design

#### Phase 31 - Technical Design Document Standard

- Objective: Define how all future TDDs must be written.
- Deliverables: `docs/technical-design-document-standard.md`.
- Acceptance Criteria: TDD template includes requirements, design, alternatives, tests, migration, rollout, and risks.
- Checklist: Create template; define review process; define approval rules.
- Jules Prompt: "Create the Technical Design Document standard for AudioVerse English."
- Review Checklist: Will future engineers know how to write implementation designs?
- Quality Gate: Pass when detailed designs have a standard.
- Definition of Done: TDD template is reusable.
- Next Phase: Phase 32 - App Module Breakdown.

#### Phase 32 - App Module Breakdown

- Objective: Define future app modules without creating source code.
- Deliverables: `docs/app-module-breakdown.md`.
- Acceptance Criteria: Modules map to architecture and requirements.
- Checklist: Define presentation; domain; data; audio; speech; pronunciation; AI; settings; sync.
- Jules Prompt: "Create the app module breakdown for the future Flutter app without writing code."
- Review Checklist: Are boundaries clear? Are modules too broad or too fragmented?
- Quality Gate: Pass when implementation structure can be planned.
- Definition of Done: Module ownership and dependencies are documented.
- Next Phase: Phase 33 - Database Design.

#### Phase 33 - Database Design

- Objective: Define SQLite schema, entities, indexes, migrations, and retention rules.
- Deliverables: `docs/database-design-document.md`.
- Acceptance Criteria: Schema supports lessons, progress, attempts, settings, content packages, and sync metadata.
- Checklist: Define tables; define relationships; define indexes; define migrations; define retention.
- Jules Prompt: "Create the Database Design Document for AudioVerse English using the data architecture."
- Review Checklist: Does schema support offline-first and future sync?
- Quality Gate: Pass when database implementation can be tested later.
- Definition of Done: Database design includes migration strategy.
- Next Phase: Phase 34 - API and Repository Contracts.

#### Phase 34 - API and Repository Contracts

- Objective: Define internal contracts between domain, repositories, engines, and adapters.
- Deliverables: `docs/api-repository-contracts.md`.
- Acceptance Criteria: Contracts describe inputs, outputs, errors, and ownership.
- Checklist: Define lesson repository; progress repository; content repository; engine contracts; sync contracts.
- Jules Prompt: "Create internal API and repository contracts for AudioVerse English without writing source code."
- Review Checklist: Are contracts implementation-neutral?
- Quality Gate: Pass when code can later follow documented boundaries.
- Definition of Done: Contracts are traceable to modules and requirements.
- Next Phase: Phase 35 - State Management and Navigation Design.

#### Phase 35 - State Management and Navigation Design

- Objective: Define future state management and accessible navigation approach.
- Deliverables: `docs/state-management-navigation-design.md`.
- Acceptance Criteria: Navigation supports screen reader flow, deep links where relevant, and offline state.
- Checklist: Evaluate Flutter state options; define navigation model; define focus recovery; define state persistence.
- Jules Prompt: "Design state management and navigation for AudioVerse English with accessibility as a primary constraint."
- Review Checklist: Are gesture conflicts and focus order considered?
- Quality Gate: Pass when UX and implementation can align.
- Definition of Done: Chosen approach is recorded or deferred with ADR.
- Next Phase: Phase 36 - Performance and Low-End Device Design.

#### Phase 36 - Performance and Low-End Device Design

- Objective: Define performance budgets and low-end device strategy.
- Deliverables: `docs/performance-low-end-device-design.md`.
- Acceptance Criteria: Budgets cover startup, memory, audio latency, ASR latency, storage, battery, and content size.
- Checklist: Define target devices; define metrics; define profiling plan; define degradation behavior.
- Jules Prompt: "Create the performance and low-end device design for AudioVerse English."
- Review Checklist: Are budgets realistic and benchmarkable?
- Quality Gate: Pass when MVP performance can be evaluated.
- Definition of Done: Performance requirements link to tests.
- Next Phase: Phase 37 - Security and Permission Design.

#### Phase 37 - Security and Permission Design

- Objective: Define permission flows, local data protection, recording handling, and privacy UX.
- Deliverables: `docs/security-permission-design.md`.
- Acceptance Criteria: Microphone, storage, sync, deletion, and consent behavior are defined.
- Checklist: Define permissions; define denied states; define deletion; define privacy settings; define cloud consent.
- Jules Prompt: "Create the security and permission design for AudioVerse English."
- Review Checklist: Can users understand and control voice data?
- Quality Gate: Gate D passes when technical design documents are sufficient.
- Definition of Done: Technical design phase is ready for UX and prototype.
- Next Phase: Phase 38 - UX Research Synthesis.

### Part V: UI, UX, and Prototype

#### Phase 38 - UX Research Synthesis

- Objective: Synthesize user contexts and accessibility research into design requirements.
- Deliverables: `docs/ux-research-synthesis.md`.
- Acceptance Criteria: UX priorities are grounded in user needs, not generic app patterns.
- Checklist: Summarize personas; define contexts; identify pain points; define design implications.
- Jules Prompt: "Create a UX research synthesis for AudioVerse English from existing project documents."
- Review Checklist: Is the synthesis specific to blind and low-vision learners?
- Quality Gate: Pass when interaction design can proceed.
- Definition of Done: UX synthesis links to accessibility requirements.
- Next Phase: Phase 39 - Accessibility Interaction Model.

#### Phase 39 - Accessibility Interaction Model

- Objective: Define interaction patterns for audio-first navigation, gestures, haptics, and screen readers.
- Deliverables: `docs/accessibility-interaction-model.md`.
- Acceptance Criteria: Core flows are usable without visual reliance.
- Checklist: Define focus order; define audio prompts; define gesture rules; define haptic rules; define blank screen mode.
- Jules Prompt: "Create the accessibility interaction model for AudioVerse English."
- Review Checklist: Can a Total Blind learner complete onboarding and a lesson?
- Quality Gate: Pass when UI wireframes can follow the model.
- Definition of Done: Interaction model is testable.
- Next Phase: Phase 40 - UI Wireframe Specification.

#### Phase 40 - UI Wireframe Specification

- Objective: Define wireframes and screen inventory for MVP.
- Deliverables: `docs/ui-wireframe-specification.md`, prototype artifacts as needed.
- Acceptance Criteria: Screens are mapped to flows, states, accessibility labels, and error states.
- Checklist: Define onboarding; home; lesson player; practice; feedback; settings; downloads.
- Jules Prompt: "Create the UI wireframe specification for AudioVerse English MVP."
- Review Checklist: Are visuals supportive without being required? Are screen reader labels planned?
- Quality Gate: Pass when prototype can be built.
- Definition of Done: Wireframe spec covers core MVP screens.
- Next Phase: Phase 41 - Design System and High Contrast.

#### Phase 41 - Design System and High Contrast

- Objective: Define visual and interaction design system for Low Vision users.
- Deliverables: `docs/design-system-guideline.md`.
- Acceptance Criteria: Typography, contrast, spacing, focus states, and touch targets are defined.
- Checklist: Define color tokens; define typography; define button states; define high contrast; define scalable text behavior.
- Jules Prompt: "Create the design system guideline for AudioVerse English with Low Vision accessibility."
- Review Checklist: Are WCAG contrast needs met? Are touch targets large enough?
- Quality Gate: Pass when UI can be prototyped consistently.
- Definition of Done: Design system supports high contrast and screen reader use.
- Next Phase: Phase 42 - Prototype Plan.

#### Phase 42 - Prototype Plan

- Objective: Plan prototype scope, tools, flows, and review method.
- Deliverables: `prototype/prototype-plan.md`.
- Acceptance Criteria: Prototype goals and non-goals are explicit.
- Checklist: Define prototype type; define flows; define accessibility review; define artifacts.
- Jules Prompt: "Create the prototype plan for AudioVerse English. Focus on validating interaction and accessibility."
- Review Checklist: Does the prototype test the riskiest UX assumptions?
- Quality Gate: Pass when prototype work is scoped.
- Definition of Done: Prototype plan includes review criteria.
- Next Phase: Phase 43 - Usability Test Plan.

#### Phase 43 - Usability Test Plan

- Objective: Define how prototype usability and accessibility will be tested.
- Deliverables: `docs/usability-test-plan.md`.
- Acceptance Criteria: Test plan covers Total Blind and Low Vision scenarios.
- Checklist: Define participants; define tasks; define success metrics; define observation notes; define consent needs.
- Jules Prompt: "Create the usability test plan for AudioVerse English prototype."
- Review Checklist: Are tasks realistic and measurable?
- Quality Gate: Pass when prototype can be evaluated.
- Definition of Done: Test plan is ready for execution.
- Next Phase: Phase 44 - Low-Fidelity Prototype Review.

#### Phase 44 - Low-Fidelity Prototype Review

- Objective: Review low-fidelity prototype artifacts against requirements.
- Deliverables: `reviews/phase-44-low-fidelity-prototype-review.md`.
- Acceptance Criteria: Review identifies UX gaps, accessibility risks, and required changes.
- Checklist: Review flows; compare to requirements; test screen reader logic conceptually; record issues.
- Jules Prompt: "Review the low-fidelity prototype artifacts for AudioVerse English."
- Review Checklist: Are findings specific and tied to requirements?
- Quality Gate: Pass when prototype issues are actionable.
- Definition of Done: Review recommends changes or approval for interactive prototype.
- Next Phase: Phase 45 - Interactive Prototype Review.

#### Phase 45 - Interactive Prototype Review

- Objective: Review interactive prototype behavior before MVP scope freeze.
- Deliverables: `reviews/phase-45-interactive-prototype-review.md`.
- Acceptance Criteria: Core flows are validated or issues are documented.
- Checklist: Review onboarding; review lesson flow; review practice flow; review feedback; review settings.
- Jules Prompt: "Review the interactive prototype for AudioVerse English against accessibility, UX, and MVP requirements."
- Review Checklist: Can the core learning journey be understood without visual cues?
- Quality Gate: Pass when MVP flow is stable enough to freeze.
- Definition of Done: Review includes approve, approve with changes, or reject.
- Next Phase: Phase 46 - MVP Scope Freeze.

#### Phase 46 - MVP Scope Freeze

- Objective: Freeze MVP scope for implementation planning.
- Deliverables: `docs/mvp-scope.md`.
- Acceptance Criteria: MVP includes only necessary features and clearly lists deferred work.
- Checklist: List included features; list excluded features; map to requirements; define release criteria.
- Jules Prompt: "Create the MVP scope freeze document for AudioVerse English."
- Review Checklist: Is MVP realistic? Are accessibility essentials included?
- Quality Gate: Gate E passes when MVP scope is approved.
- Definition of Done: MVP scope can drive backlog and implementation.
- Next Phase: Phase 47 - Flutter Project Initialization Gate.

### Part VI: Development Readiness and Implementation

#### Phase 47 - Flutter Project Initialization Gate

- Objective: Decide whether the repository is ready to create the Flutter project.
- Deliverables: `reviews/phase-47-engineering-readiness-review.md`.
- Acceptance Criteria: Gates A through F are passed or explicitly waived with documented risk.
- Checklist: Verify required docs; verify ADRs; verify MVP scope; verify testing plan; verify app folder status.
- Jules Prompt: "Perform the engineering readiness review for Flutter initialization. Do not initialize Flutter unless the gate passes."
- Review Checklist: Are all required documents present and reviewable?
- Quality Gate: Pass when Flutter project initialization is allowed.
- Definition of Done: Decision is recorded; if approved, next phase may create Flutter scaffold.
- Next Phase: Phase 48 - Engineering Standards and CI Plan.

#### Phase 48 - Engineering Standards and CI Plan

- Objective: Define coding standards, branch strategy, CI checks, formatting, linting, and review rules.
- Deliverables: `docs/development-guideline.md`, `docs/ci-plan.md`.
- Acceptance Criteria: Future code quality expectations are explicit.
- Checklist: Define linting; define tests; define branch policy; define PR review; define commit rules.
- Jules Prompt: "Create the development guideline and CI plan for AudioVerse English."
- Review Checklist: Are standards practical for Flutter and accessible app development?
- Quality Gate: Pass when implementation has engineering guardrails.
- Definition of Done: CI plan is ready to implement when code exists.
- Next Phase: Phase 49 - Sprint 0 Implementation Skeleton.

#### Phase 49 - Sprint 0 Implementation Skeleton

- Objective: Initialize Flutter only if Phase 47 approved it and create minimal project skeleton.
- Deliverables: Future Flutter project in `app/`, initial README, lint configuration, empty module structure.
- Acceptance Criteria: No feature logic yet; structure follows approved module breakdown.
- Checklist: Confirm approval; initialize project; add linting; add test scaffold; document setup.
- Jules Prompt: "If and only if engineering readiness is approved, initialize the Flutter project skeleton in app/."
- Review Checklist: Does skeleton match documented architecture?
- Quality Gate: Pass when project builds with no features.
- Definition of Done: Skeleton builds and tests run.
- Next Phase: Phase 50 - Core Offline Learning Flow.

#### Phase 50 - Core Offline Learning Flow

- Objective: Implement the simplest offline lesson browsing and playback flow.
- Deliverables: Lesson catalog, local sample content, player shell, tests.
- Acceptance Criteria: User can access a sample lesson offline.
- Checklist: Implement content loading; implement accessible navigation; implement playback; add tests.
- Jules Prompt: "Implement the core offline learning flow according to approved requirements and architecture."
- Review Checklist: Is the flow accessible with screen readers? Does it work offline?
- Quality Gate: Pass when core lesson flow works with local content.
- Definition of Done: Feature is tested and documented.
- Next Phase: Phase 51 - Audio Playback and Soundscape MVP.

#### Phase 51 - Audio Playback and Soundscape MVP

- Objective: Implement audio playback controls and structured soundscape prototype.
- Deliverables: Audio player behavior, sample soundscape manifest, tests.
- Acceptance Criteria: App can play lesson audio and a structured soundscape from local assets.
- Checklist: Implement playback; implement pause/resume; parse manifest; handle interruptions; add accessibility labels.
- Jules Prompt: "Implement audio playback and soundscape MVP using structured assets, not AI generation."
- Review Checklist: Are playback controls accessible and predictable?
- Quality Gate: Pass when audio behavior meets MVP requirements.
- Definition of Done: Audio tests and manual accessibility checks pass.
- Next Phase: Phase 52 - Recording, ASR Adapter, and Pronunciation Prototype.

#### Phase 52 - Recording, ASR Adapter, and Pronunciation Prototype

- Objective: Implement recording flow and adapter boundary for ASR and pronunciation.
- Deliverables: Recording UI, ASR interface, mock or selected engine adapter, pronunciation prototype.
- Acceptance Criteria: User can record a practice attempt and receive basic feedback.
- Checklist: Implement permission flow; implement recording; implement adapter; implement mock scoring; add tests.
- Jules Prompt: "Implement recording, ASR adapter boundary, and pronunciation prototype according to documented architecture."
- Review Checklist: Are ASR and pronunciation kept separate?
- Quality Gate: Pass when speaking practice flow is functional and modular.
- Definition of Done: Recording and feedback flow works locally.
- Next Phase: Phase 53 - Progress Tracking and SQLite MVP.

#### Phase 53 - Progress Tracking and SQLite MVP

- Objective: Implement local progress tracking using SQLite.
- Deliverables: Database schema, migrations, repositories, progress UI states, tests.
- Acceptance Criteria: Lesson progress persists offline across app restarts.
- Checklist: Implement schema; implement repositories; implement migrations; add tests; verify offline behavior.
- Jules Prompt: "Implement SQLite-backed progress tracking according to the approved database design."
- Review Checklist: Are migrations tested? Is user data separated from content?
- Quality Gate: Pass when progress is durable offline.
- Definition of Done: Progress tracking tests pass.
- Next Phase: Phase 54 - AI Tutor Adapter Prototype.

#### Phase 54 - AI Tutor Adapter Prototype

- Objective: Implement AI Tutor adapter boundary with safe fallback behavior.
- Deliverables: AI tutor interface, local mock or selected candidate adapter, tutor UI entry points, tests.
- Acceptance Criteria: AI Tutor can be enabled, disabled, or replaced without changing lesson flow.
- Checklist: Implement interface; implement fallback; implement guardrails; add tests; document limitations.
- Jules Prompt: "Implement AI Tutor adapter prototype as an optional module."
- Review Checklist: Does AI remain separate from core offline learning?
- Quality Gate: Pass when AI can fail gracefully.
- Definition of Done: Tutor adapter is modular and documented.
- Next Phase: Phase 55 - Test Strategy Execution.

### Part VII: Testing, Hardening, and Release

#### Phase 55 - Test Strategy Execution

- Objective: Execute MVP test strategy across unit, integration, accessibility, offline, and regression tests.
- Deliverables: `reviews/phase-55-test-execution-report.md`.
- Acceptance Criteria: Test results are documented with pass/fail and defects.
- Checklist: Run unit tests; run integration tests; run offline tests; run accessibility checks; file defects.
- Jules Prompt: "Execute the MVP test strategy and produce a test execution report."
- Review Checklist: Are failures documented honestly?
- Quality Gate: Pass when critical tests pass or blockers are triaged.
- Definition of Done: Test report exists and defects are prioritized.
- Next Phase: Phase 56 - Accessibility Testing.

#### Phase 56 - Accessibility Testing

- Objective: Validate core flows with TalkBack, VoiceOver assumptions, high contrast, scalable text, and blank screen mode.
- Deliverables: `reviews/phase-56-accessibility-test-report.md`.
- Acceptance Criteria: Core learning flow can be completed without sight.
- Checklist: Test onboarding; test lesson playback; test recording; test feedback; test settings; test errors.
- Jules Prompt: "Perform accessibility testing for AudioVerse English MVP and report defects."
- Review Checklist: Are issues tied to user impact?
- Quality Gate: Pass when no critical accessibility blockers remain.
- Definition of Done: Accessibility report is reviewed and blockers are resolved or deferred with approval.
- Next Phase: Phase 57 - Performance Benchmark and ASR Decision.

#### Phase 57 - Performance Benchmark and ASR Decision

- Objective: Run or prepare final benchmark evidence for ASR engine decision.
- Deliverables: `reviews/phase-57-performance-asr-benchmark-report.md`, ADR update.
- Acceptance Criteria: Vosk, Whisper.cpp, or alternatives are evaluated against target metrics.
- Checklist: Measure latency; measure memory; measure battery; measure accuracy; record device class.
- Jules Prompt: "Run or document the ASR and performance benchmark and recommend an engine decision."
- Review Checklist: Is the recommendation based on evidence?
- Quality Gate: Pass when ASR decision is accepted or explicitly deferred.
- Definition of Done: ADR records the decision or deferral.
- Next Phase: Phase 58 - Security and Privacy Review.

#### Phase 58 - Security and Privacy Review

- Objective: Review permissions, voice data handling, storage, sync, and deletion.
- Deliverables: `reviews/phase-58-security-privacy-review.md`.
- Acceptance Criteria: No unresolved critical privacy risks remain.
- Checklist: Review microphone data; review local storage; review sync; review deletion; review disclosures.
- Jules Prompt: "Perform security and privacy review for AudioVerse English MVP."
- Review Checklist: Are voice recordings protected and controllable?
- Quality Gate: Pass when privacy risks are acceptable for beta.
- Definition of Done: Review is complete and action items are tracked.
- Next Phase: Phase 59 - Beta Release Readiness.

#### Phase 59 - Beta Release Readiness

- Objective: Decide whether MVP is ready for beta testing.
- Deliverables: `reviews/phase-59-beta-release-readiness.md`.
- Acceptance Criteria: Critical defects, accessibility blockers, and privacy blockers are resolved.
- Checklist: Review test reports; review known issues; review release notes; review rollback plan.
- Jules Prompt: "Assess beta release readiness for AudioVerse English."
- Review Checklist: Is beta scope honest and safe for target users?
- Quality Gate: Pass when beta can be released to controlled testers.
- Definition of Done: Beta readiness decision is recorded.
- Next Phase: Phase 60 - Production Launch and Maintenance Plan.

#### Phase 60 - Production Launch and Maintenance Plan

- Objective: Prepare production launch, support, maintenance, and iteration process.
- Deliverables: `docs/deployment-guideline.md`, `docs/maintenance-plan.md`, `reviews/phase-60-production-readiness.md`.
- Acceptance Criteria: Launch checklist, support flow, monitoring, content update process, and post-launch review are documented.
- Checklist: Define store release; define rollback; define support; define analytics policy; define maintenance cadence.
- Jules Prompt: "Create production launch and maintenance planning documents for AudioVerse English."
- Review Checklist: Is production support realistic and privacy-conscious?
- Quality Gate: Gate H passes when production readiness is approved.
- Definition of Done: Project has a documented path from launch to maintenance.
- Next Phase: Continue with post-launch iteration phases as needed.

---

## 17. Master Phase Gates Summary

| Gate | Passed after | Meaning |
| --- | --- | --- |
| Gate A | Phase 07 | Foundation is ready |
| Gate B | Phase 18 | Requirements are ready |
| Gate C | Phase 30 | Architecture is ready |
| Gate D | Phase 37 | Technical design is ready |
| Gate E | Phase 46 | UX, prototype, and MVP scope are ready |
| Gate F | Phase 48 | Engineering standards are ready |
| Gate G | Phase 59 | MVP beta is ready |
| Gate H | Phase 60 | Production process is ready |

---

## 18. Definition of Ready for Coding

Coding may begin only when:

- Gate A through Gate F are passed.
- SRS is approved.
- SAD is approved.
- MVP scope is frozen.
- Accessibility interaction model is approved.
- Database design is approved.
- Development guideline exists.
- Testing guideline exists.
- At least initial ADRs exist.
- The user explicitly allows Flutter project initialization.

If any item is missing, Jules must stop and create or request the missing document.

---

## 19. Definition of Done for MVP

MVP is done when:

- A learner can onboard with accessibility preferences.
- A learner can browse local lessons offline.
- A learner can play lesson audio.
- A learner can complete a speaking practice attempt.
- A learner can receive basic feedback.
- Progress persists in SQLite.
- Core flows work without internet.
- Core flows are navigable with screen reader assumptions.
- High contrast mode exists.
- Blank screen mode exists or has an approved deferral.
- Critical privacy risks are resolved.
- Critical accessibility blockers are resolved.
- Test reports are complete.
- Known limitations are documented.

---

## 20. Long-Term Maintenance Rules

After production, Jules must continue to:

- Keep ADRs updated.
- Maintain the requirements traceability matrix.
- Document content package changes.
- Re-run accessibility checks for navigation changes.
- Re-run benchmarks when changing speech or AI engines.
- Keep privacy documentation current.
- Preserve offline-first behavior.
- Avoid adding features that weaken accessibility.

---

## 21. Jules Prompt Library

This prompt library is used when Jules needs a controlled, repeatable instruction pattern. Phase-specific prompts remain authoritative, but these prompts may be used inside a phase when the task requires deeper review or document generation.

### 21.1 Repository review prompt

```text
You are Jules, reviewing the AudioVerse English repository.

Read README.md, the Jules handbook, and all index files.
Inspect the repository structure.
Do not create app code.

Produce:
- Repository structure summary
- Current documentation status
- Missing required documents
- Risks
- Recommended next phase
- Quality gate status
```

### 21.2 Documentation gap analysis prompt

```text
You are Jules, performing a documentation gap analysis.

Compare the repository against the required document set in the handbook.
For each missing or incomplete document, provide:
- Document name
- Purpose
- Dependency
- Priority
- Suggested owner
- Recommended phase

Do not write application code.
```

### 21.3 SRS generation prompt

```text
You are Jules, creating or updating the Software Requirements Specification for AudioVerse English.

Use the project charter, user model, glossary, accessibility requirements, offline-first requirements, and existing reviews.

Rules:
- Requirements must be testable.
- Use stable IDs.
- Mark assumptions.
- Separate functional, non-functional, accessibility, offline, audio, speech, pronunciation, AI, data, security, and sync requirements.
- Do not make architecture decisions inside the SRS unless they are constraints already approved by ADR.

Produce an SRS update and a list of open questions.
```

### 21.4 Architecture document prompt

```text
You are Jules, creating or updating the Software Architecture Document for AudioVerse English.

Use the approved SRS and architecture principles.

Rules:
- Preserve offline-first behavior.
- Treat accessibility as a primary quality attribute.
- Use modular interfaces for speech recognition, pronunciation, AI tutor, audio, and sync.
- Do not bind the app to Vosk, Whisper.cpp, or any model until benchmark evidence exists.
- Include diagrams where useful.
- Link architecture choices to requirements.

Produce the architecture document, risks, ADR candidates, and quality gate result.
```

### 21.5 ADR creation prompt

```text
You are Jules, writing an Architecture Decision Record for AudioVerse English.

Decision topic:
[TOPIC]

Use the ADR template from the handbook.

Include:
- Context
- Decision
- Options considered
- Consequences
- Accessibility impact
- Offline impact
- Performance impact
- Security and privacy impact
- Follow-up tasks

Do not treat provisional ideas as accepted decisions.
```

### 21.6 Accessibility review prompt

```text
You are Jules, performing an accessibility review for AudioVerse English.

Review the target document, prototype, or implementation against:
- Total Blind usability
- Low Vision usability
- TalkBack
- VoiceOver
- WCAG
- Gesture navigation
- High contrast mode
- Blank screen mode
- Audio and haptic feedback

Lead with blockers.
Tie every finding to user impact.
Recommend concrete fixes.
```

### 21.7 Speech and pronunciation review prompt

```text
You are Jules, reviewing speech and pronunciation design.

Check that:
- Speech recognition and pronunciation assessment are separated.
- ASR output is not treated as pronunciation truth.
- Confidence and uncertainty are handled.
- Benchmark requirements are defined.
- Low-end device performance is considered.
- Engine adapters remain replaceable.

Produce findings, risks, and required document updates.
```

### 21.8 Prototype review prompt

```text
You are Jules, reviewing an AudioVerse English prototype.

Evaluate:
- Core learner journey
- Screen reader flow
- Audio prompt clarity
- Gesture model
- Error recovery
- Low Vision visual support
- Alignment with MVP scope

Do not approve the prototype if blind users cannot complete the core flow conceptually.
```

### 21.9 Test planning prompt

```text
You are Jules, creating a test plan for AudioVerse English.

Cover:
- Unit tests
- Integration tests
- Accessibility tests
- Offline tests
- Audio tests
- Speech and pronunciation tests
- SQLite persistence tests
- Performance tests
- Privacy tests

Map tests to requirement IDs and release gates.
```

### 21.10 Release readiness prompt

```text
You are Jules, performing release readiness review.

Check:
- Requirements traceability
- Test results
- Accessibility blockers
- Offline behavior
- Performance baseline
- Privacy and permissions
- Known issues
- Rollback plan
- Support plan

Return one of:
- Ready
- Ready with approved risks
- Not ready
```

### 21.11 Readback prompt

```text
You are Jules, preparing to continue the next phase.

Re-read:
- README.md
- Jules handbook
- Documentation inventory
- Decision register
- Latest review file
- Current phase deliverables

Summarize current state before making changes.
```

### 21.12 Commit summary prompt

```text
You are Jules, preparing a commit summary.

Provide:
- Phase number
- Files changed
- Purpose of changes
- Decisions recorded
- Tests or reviews performed
- Open questions
- Suggested commit message
```

---

## 22. Final Instruction to Jules

Jules must treat this handbook as the first document to read and the last document to contradict.

If a task conflicts with this handbook, Jules must:

1. Pause.
2. Explain the conflict.
3. Propose a handbook update, ADR, or explicit waiver.
4. Continue only after the conflict is resolved.

AudioVerse English is not a quick prototype. It is a serious accessibility-first learning platform. The work must proceed with the discipline, traceability, review habits, and care expected from a professional software organization.
