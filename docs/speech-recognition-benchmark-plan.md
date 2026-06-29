# Speech Recognition Benchmark Plan

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers:
Last updated: 2026-06-28
Related documents:

- [Software Architecture Document](software-architecture-document.md)
- [Modular Engine Architecture](modular-engine-architecture.md)
- [Performance and Low-End Device Design](performance-low-end-device-design.md) (Pending)

## 1. Purpose

This document defines the benchmark plan to evaluate and select the primary offline Speech Recognition (ASR) engine for AudioVerse English. The initial candidates are **Vosk** and **Whisper.cpp**.

The goal is to provide evidence-based criteria for selecting an ASR engine that meets the project's strict requirements for offline-first capability, low-end device performance, and high accuracy for English language learners, especially for users relying on screen readers.

## 2. Scope

This benchmark specifically evaluates the ASR component. It **does not** evaluate pronunciation assessment or feedback generation, which are separate pipeline steps. The output of the ASR engine (text transcription and confidence scores) will be the sole input for pronunciation assessment evaluation.

## 3. Candidates

1. **Vosk:** Lightweight, fast, known for good performance on mobile devices.
2. **Whisper.cpp:** High accuracy, but potentially heavier. Needs evaluation on low-end hardware.
3. **Future Candidates:** (e.g., specific ML Kit on-device models, if they support custom offline language packs without cloud fallback).

## 4. Target Device Matrix

The application must run smoothly on low-to-mid-range devices. The benchmark will target the following device classes:

- **Low-End Device (Minimum Specification):**
  - OS: Android 8.0+ / iOS 12+
  - RAM: 2GB - 3GB
  - Processor: Entry-level ARM (e.g., Snapdragon 400 series or equivalent MediaTek)
- **Mid-Range Device:**
  - OS: Android 10+ / iOS 14+
  - RAM: 4GB - 6GB
  - Processor: Mid-tier ARM (e.g., Snapdragon 600/700 series)

## 5. Metrics and Thresholds

The following metrics will be collected for each candidate on each target device class.

| Metric | Definition | Target Threshold | Criticality |
| :--- | :--- | :--- | :--- |
| **Accuracy (WER/CER)** | Word Error Rate and Character Error Rate on the test dataset. | WER < 15% on non-native accents. | High |
| **Latency (RTF)** | Real-Time Factor (processing time / audio duration). | RTF < 1.0 (ideally < 0.5 for near real-time). | High |
| **Memory Footprint** | Peak RAM usage during model loading and inference. | Peak RAM < 150MB overhead. | High (2GB RAM limit) |
| **Storage Size** | Size of the necessary acoustic and language models. | Model Size < 50MB per language. | Medium (100MB app size limit) |
| **Battery Impact** | Battery drain percentage over 1 hour of active recognition. | Drain < 15% per hour (total app limit). | High |

## 6. Datasets

The benchmark will utilize the following datasets to ensure robustness across different accents and audio conditions:

1. **Native English Speakers (Baseline):** Standard datasets (e.g., LibriSpeech subsets).
2. **Non-Native Accented English:** Crucial for language learners (e.g., L2-ARCTIC or internally collected samples matching the target learner demographics).
3. **Noisy Environments:** Audio with simulated background noise (e.g., coffee shop, transit) to test robustness.

## 7. Decision Criteria

The final decision will be based on a weighted evaluation of the metrics, prioritized for the constraints of AudioVerse English:

1. **Hard Constraints (Must Pass):**
    - Must operate 100% offline.
    - Must not exceed memory limits on the minimum specification device (2GB RAM).
    - Must not exceed the battery drain limit (15%/hour).
2. **Trade-offs:**
    - If Whisper.cpp offers significantly higher accuracy (WER < 10%) but exceeds latency targets (RTF > 1.0) on low-end devices, Vosk will be preferred.
    - If both candidates fail the minimum specification tests, the architecture will require a degraded mode (e.g., simpler grammar models) or deferral of advanced ASR features on low-end hardware.

## 8. Benchmark Execution Process

1. **Prepare Adapters:** Implement standard adapter interfaces for both Vosk and Whisper.cpp within a test harness app.
2. **Load Datasets:** Provision the test harness with the selected audio datasets.
3. **Run Automated Tests:** Execute the test suite on physical devices representing the target matrix.
4. **Collect Telemetry:** Monitor and log latency, memory usage, and accuracy scores.
5. **Analyze Battery Drain:** Conduct extended manual tests to measure battery impact.
6. **Publish Report:** Document findings in `reviews/phase-57-performance-asr-benchmark-report.md`.
7. **Record Decision:** Create an ADR documenting the selected engine and rationale.

## 9. Review Checklist

- [ ] Are low-to-mid-range devices accurately represented?
- [ ] Are the metrics clearly defined and measurable?
- [ ] Are the decision criteria objective and based on constraints?
- [ ] Is the benchmark plan isolated from pronunciation assessment logic?
