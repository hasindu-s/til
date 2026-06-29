# Spec-Driven Development

**Date:** 2026-06-29

---

## What it is

A new approach to building software with agentic coding tools (e.g. Claude Code), where the spec document — not the prompt — is the primary artifact.

1. Before writing code, write a spec document for the feature. The agent can help draft it.
2. Review and validate the spec until it actually describes what you want.
3. The agent implements code that matches the validated spec.

The spec document gets committed to the repo alongside the code.

---

## Why commit the spec

Requirements change over time. Because the spec lives in the repo, it evolves with the code — giving a durable, versioned record of *why* the code looks the way it does, not just a one-time prompt that's lost after the session ends.

---

## How this differs from plan mode

Plan mode is well suited for one-off requirements: you describe what you want, the agent plans and executes, done.

Spec-driven development is better when requirements keep changing or new requirements arrive incrementally. Without a committed spec, both you and the agent lose track of intent as the codebase grows — there's no persistent source of truth to diff new requirements against.