# Jarvis — Real-Time Streaming Voice Assistant

> A low-latency, hands-free voice assistant: streaming speech-to-text → LLM → text-to-speech,
> with multi-persona routing on a shared pipeline.

## Problem

Build a voice assistant that feels conversational — latency low enough for natural back-and-forth
— and that exposes the platform's tools by voice, with resilience when a component degrades.

## Architecture

- **Streaming pipeline (Pipecat):** real-time **STT → LLM → TTS**, processing audio in a stream
  rather than turn-by-turn batch, to minimize perceived latency.
- **Fallback voice:** a secondary TTS voice keeps the assistant talking when the primary voice
  service is unavailable.
- **WebRTC transport:** real-time, low-latency browser audio.
- **Profile-based prompts & tools:** one engine serves distinct personas (e.g. operational vs.
  executive) with different system prompts and tool subsets, selected at the transport/routing
  layer so the same pipeline backs multiple front-ends.

## My role

Pipeline integration, persona/profile routing, and **latency / time-to-first-byte (TTFB)
reduction** across the STT → LLM → TTS path.

## Stack

`Pipecat` · `WebRTC` · `streaming STT/TTS` · `LLM` · `Python`

## Results

- A working **real-time voice assistant** with measurable latency/TTFB improvements.
- **Multi-persona routing** on a single shared pipeline, with per-persona prompts and tools.

## Engineering highlights

- **Latency focus:** profiling and reducing time-to-first-byte across a multi-stage streaming
  pipeline — the metric that most affects how "live" a voice assistant feels.
- **Graceful degradation:** a fallback voice so a single failing dependency doesn't take the
  assistant offline.
