# ADR-0005: Defer Speech Recognition Engine Decision

Project: AudioVerse English
Status: Accepted
Date: 2026-06-28
Decision owners: Jules
Related documents: [Speech Recognition Benchmark Plan](../docs/speech-recognition-benchmark-plan.md)

## Context

AudioVerse English requires a 100% offline Speech Recognition (ASR) engine. The primary candidates are Vosk and Whisper.cpp. However, the application must run efficiently on low-end devices (minimum 2GB RAM) without exceeding battery drain limits or violating the 100MB base app install size constraint.

## Decision

We will **defer the final selection of the specific Speech Recognition engine** until empirical benchmark data is gathered on target-class devices. We will proceed with architectural and UI design using the Modular Engine Architecture (ADR-0004), employing mock adapters or a provisional lightweight implementation during initial development.

## Options Considered

| Option | Pros | Cons |
| --- | --- | --- |
| Defer (Benchmark First) | Ensures the chosen engine actually meets NFRs on real devices, reduces risk of costly rewrites. | Delays the final integration of the ASR feature. |
| Choose Vosk Now | Very lightweight, fast, known to work well on low-end devices. | Acoustic models might be less accurate than Whisper for certain accents. |
| Choose Whisper.cpp Now | Highly accurate, robust to noise. | High memory footprint, potentially slower inference on low-end CPUs, larger model size. |

## Consequences

- Development of the core lesson flow and pronunciation feedback logic can proceed using a mocked ASR adapter.
- We must execute Phase 57 (Performance Benchmark and ASR Decision) before the Beta release.
- The `ISpeechRecognitionEngine` interface must be robust enough to handle the idiosyncrasies of both Vosk and Whisper.

## Accessibility Impact

Neutral for now, but critical later. Accurate, low-latency ASR is essential for a smooth, accessible conversational experience.

## Offline Impact

Both candidates support offline use.

## Performance Impact

This decision mitigates performance risk by requiring proof before commitment.

## Security and Privacy Impact

Both candidates run locally, satisfying the privacy requirement that voice data does not leave the device.

## Follow-Up

- Execute the Benchmark Plan defined in Phase 25.
- Update this ADR or create a new one once the benchmark results are available.
