# Soundscape Asset Architecture

Project: AudioVerse English
Status: Draft
Owner: Jules
Reviewers: User
Last updated: 2026-06-28
Related documents: [Jules Master Development Handbook](../jules/JULES_MASTER_DEVELOPMENT_HANDBOOK.md), [Audio and Soundscape Requirements](audio-soundscape-requirements.md)

## Purpose

The purpose of this document is to define the architecture and format for structured soundscapes within the AudioVerse English platform. It explicitly establishes that soundscapes are asset-driven constructs, composed of a JSON manifest and pre-recorded audio files, rather than dynamically generated via AI.

## Scope

This architecture covers the structure of the JSON manifest, the responsibilities of the audio layers, timing logic, spatial metadata, and the required accessibility cues to ensure soundscapes are usable by blind and low-vision learners.

## Definitions

- **Soundscape**: A structured learning scene defined by a JSON manifest and an accompanying set of audio assets (e.g., `.wav` or `.mp3` files) that orchestrate background noise, dialogue, and interaction cues.
- **Manifest**: The JSON file (`soundscape.json`) that dictates how and when audio layers are played.
- **Layer**: A single audio track within the soundscape, such as background ambience, foreground dialogue, or interactive prompts.
- **Spatial Metadata**: Information within the manifest that directs panning or positional audio rendering, providing a realistic 3D sense of environment (e.g., placing traffic noise on the left, dialogue in the center).
- **Accessibility Cues**: Explicit audio markers (like chimes or spoken directions) that guide screen reader users on when to interact or expect a response.

## Assumptions

- Soundscapes are packaged into self-contained ZIP files downloaded to the local device.
- The playback engine will parse the JSON manifest to coordinate timing and volume adjustments locally.
- Generative AI is strictly excluded from creating soundscape elements on-the-fly to guarantee offline reliability, low-end device compatibility, and predictable content quality.

## Architecture Guidelines

### Asset-Driven Structure

Soundscapes must not use runtime AI generation. Each soundscape is an offline-capable, asset-based package.

Example directory structure:

```text
airport-scene/
  airport.json
  background_ambience.wav
  ticket_agent_dialogue.wav
  learner_prompt.wav
  success_chime.wav
```

### JSON Manifest Definition

The `soundscape.json` manifest orchestrates the scene. It controls:

- **Scene Metadata**: ID, title, and total duration.
- **Layers**: Definitions of each audio track, its relative volume, and loop behavior.
- **Timing Events**: Triggers indicating exactly when a track should play, pause, or duck (lower volume).
- **Spatial Data**: Stereo panning or 3D positioning metadata (e.g., `pan: -1.0` for full left).
- **Accessibility Nodes**: Timestamps indicating when specific screen reader actions or instructional cues should be announced.

### Example JSON Manifest

```json
{
  "soundscape_id": "scene_airport_checkin_01",
  "title": "Airport Check-in",
  "duration_seconds": 120,
  "layers": [
    {
      "id": "bg_ambience",
      "file": "background_ambience.wav",
      "type": "environment",
      "loop": true,
      "base_volume": 0.4,
      "spatial": {
        "pan": 0.0
      }
    },
    {
      "id": "agent_dialogue",
      "file": "ticket_agent_dialogue.wav",
      "type": "dialogue",
      "loop": false,
      "base_volume": 1.0,
      "spatial": {
        "pan": 0.2
      }
    },
    {
      "id": "prompt_cue",
      "file": "learner_prompt.wav",
      "type": "accessibility_cue",
      "loop": false,
      "base_volume": 0.8,
      "spatial": {
        "pan": 0.0
      }
    }
  ],
  "timeline": [
    {
      "time_seconds": 0,
      "action": "play",
      "layer_id": "bg_ambience"
    },
    {
      "time_seconds": 5,
      "action": "play",
      "layer_id": "agent_dialogue"
    },
    {
      "time_seconds": 15,
      "action": "duck",
      "layer_id": "bg_ambience",
      "target_volume": 0.1
    },
    {
      "time_seconds": 16,
      "action": "play",
      "layer_id": "prompt_cue",
      "description": "It is your turn to speak. Say: 'I have one checked bag.'"
    }
  ]
}
```

### Accessibility Integration

The manifest must include `accessibility_cue` types. These are essential for blind learners:

1. **Explicit Prompting**: A distinct sound (or spoken instruction) must notify the user when they are expected to speak.
2. **Ducking**: Environmental background noises must be ducked (lowered in volume) when instructions are spoken or when the microphone is recording.
3. **Screen Reader Compatibility**: The timeline events can emit state changes to the UI layer, ensuring TalkBack or VoiceOver can read contextual hints (like the `description` field in the timeline) if the user queries the screen.

## AI Exclusion Rule

Generative AI (e.g., dynamically generating ambient noise or dialogue via LLMs) is completely prohibited within the soundscape playback pipeline. This ensures deterministic testing, zero latency, offline capability, and avoids unpredictable or confusing audio overlaps for blind learners.

## Open Questions

- What specific audio file format (`.wav` vs `.ogg` vs `.m4a`) provides the optimal balance between asset size and playback performance on low-end Android devices?
- How complex should the spatial metadata become? Should we support basic stereo panning (`-1.0` to `1.0`) initially, or plan for 3D spatialized audio objects?

## Risks

- **Syncing Issues**: Delays in the audio playback engine might cause timeline events to misalign with the actual audio being heard, potentially confusing learners.
- **Asset Size**: High-quality audio layers could bloat the size of content packages, conflicting with the 100MB base install constraint if too many soundscapes are included initially.

## Review Checklist

- Is AI generation excluded from core soundscape design? Yes.
- Does the architecture support accessibility cues? Yes.
- Can content creators prepare soundscapes offline? Yes.

## Acceptance Criteria

- JSON manifest structure and audio asset responsibilities are described.
- Example manifest is included as documentation.
- The document explicitly forbids runtime AI generation for soundscapes.
