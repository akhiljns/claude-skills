---
name: kt
description: >
  Generates a Knowledge Transfer document by reading all repo context — CLAUDE.md, skills, source code, architecture, and critical flows.
  Use when onboarding someone new or creating a project handoff doc.
argument-hint: "[output format: 'markdown' (default) | 'interactive' — markdown writes a KT.md file, interactive walks through it conversationally]"
allowed-tools: Read, Glob, Grep, Bash, Write, Agent
---

# Knowledge Transfer Generator

Reads everything about the current project — config, skills, source code, docs, architecture, database schema, API surface, critical flows — and produces a comprehensive Knowledge Transfer document that gets a new developer productive fast.

This is not a README rewrite. It captures the things you'd explain verbally in a KT session: why decisions were made, where the gotchas are, what the mental model should be, and what to touch vs. leave alone.

Follow the instructions in `workflow.md`.
