# Phase 33: Database Design

**Status**: Complete
**Documents created or changed**:

- `docs/database-design-document.md` (created)
- `docs/documentation-inventory.md` (updated status and next steps)

**Key decisions**:

- Opted for raw SQLite over heavy abstractions like Realm to fit low-end device constraints.
- Segmented data strictly into settings, immutable content_packages/lessons, and mutable user_progress/practice_attempts to protect learner state during content updates.
- Adopted a Last-Write-Wins simple strategy for optional cloud sync using `updated_at`.
- Kept voice recordings entirely out of the local database to adhere to privacy and device storage requirements.

**Open questions**:

- What specific indices might be required by complex Supabase sync queries beyond the basic `sync_status` index?
- Should `practice_attempts` auto-prune locally on a strictly rolling N-days basis, or only when storage limits are hit?

**Risks**:

- **Sync conflict overwrites**: Last-Write-Wins could potentially overwrite progress if a user frequently works offline across multiple devices.
- **Storage bloat**: If pruning logic for `practice_attempts` fails, low-end devices could run out of storage space.

**Quality gate result**:

- Passed. The database schema accommodates both absolute offline truth and optional cloud sync without violating accessibility or storage constraints.

**Recommended next phase**:

- Phase 34 - API and Repository Contracts
