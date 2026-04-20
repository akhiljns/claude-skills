# Knowledge Transfer Generator — Workflow

## Step 0 — Determine output format

Check the user's argument:
- `interactive` → Walk through each section conversationally, pausing for questions. Do NOT write a file.
- `markdown` (or no argument) → Generate a `KT.md` file at the project root.

## Step 1 — Gather all project context

Read everything available. Not every project will have all of these — skip what doesn't exist. Use the Agent tool to parallelize reads where possible.

### 1a. Project identity and config
- `CLAUDE.md`, `AGENTS.md`, `README.md` — project instructions, conventions, agent guidance
- `package.json` / `pyproject.toml` / `Cargo.toml` / `go.mod` / `Makefile` — manifest, scripts, dependencies
- `.env.example` / `.env.local.example` — required environment variables
- `tsconfig.json` / `eslint.config.*` / `prettier.config.*` — tooling config
- `docker-compose.yml` / `Dockerfile` — containerization setup
- `vercel.json` / `fly.toml` / `railway.json` — deployment config

### 1b. Directory structure
- Run `ls -la` on root and the 2-3 most important subdirectories
- Identify the architectural pattern (monorepo? app router? src/ convention? flat?)

### 1c. Skills and automation
- Read all files under `.claude/skills/` — every `SKILL.md` and `workflow.md`
- These represent the team's codified workflows and should be included in the KT

### 1d. Documentation
- Read all files under `docs/` (PRD, architecture, UX spec, epics, ADRs, etc.)
- Read any `CONTRIBUTING.md`, `CHANGELOG.md`, `ARCHITECTURE.md` at root

### 1e. Source code — entry points and patterns
- Read the main entry point (`app/layout.tsx`, `app/page.tsx`, `src/index.ts`, `main.go`, etc.)
- Read 2-3 representative API routes or handlers to understand the request/response pattern
- Read the database layer (connection setup, query helpers, migrations)
- Read the auth layer (session handling, middleware, guards)
- Read any real-time infrastructure (SSE, WebSocket, pub/sub)

### 1f. Types and data model
- Read the shared types file(s) (`types/`, `types/index.ts`, models, schemas)
- Read DB migrations or schema files to understand the data model

### 1g. Tests
- Identify test framework and test file locations
- Read 1-2 representative test files to understand testing patterns and conventions

### 1h. CI/CD and deployment
- Read `.github/workflows/`, `Jenkinsfile`, `buildspec.yml`, or equivalent
- Read deployment scripts or infrastructure-as-code files

## Step 2 — Synthesize the KT document

Organize the knowledge into these sections. The goal is to explain things the way a senior developer would in a 1-hour KT session — not to list facts, but to build a mental model.

### Section structure:

#### 1. One-Paragraph Summary
What is this project, who uses it, and what does it do? Write it so someone with zero context gets it in 30 seconds.

#### 2. The Mental Model
The single most important section. Explain the core architecture in plain language:
- How does data flow through the system? (request → auth → handler → DB → response)
- What are the 3-5 main concepts/entities and how do they relate?
- What's the "shape" of the codebase? (e.g., "it's a standard Next.js App Router project with API routes that talk to Postgres")

Include a simple ASCII diagram if the architecture has multiple interacting parts.

#### 3. Tech Stack & Why
List each major technology and — critically — WHY it was chosen. Not just "PostgreSQL" but "PostgreSQL with pgvector extension for semantic search over skill embeddings." Include version numbers where they matter (framework version, language version).

#### 4. Getting Started
Exact steps to go from `git clone` to a running local dev environment. Include:
- Prerequisites (Node version, Docker, DB setup, extensions)
- Environment variable setup (what to copy, what to fill in)
- Database setup (migrations, seed data)
- How to start the dev server
- How to verify it's working (what URL to hit, what you should see)

#### 5. Directory Map
A guided tour of the directory structure. Not just the tree — explain what each directory's responsibility is and when you'd touch it. Highlight:
- Where new features go
- Where shared utilities live
- What NOT to modify (vendored code, generated files, framework internals)

#### 6. Key Patterns (Copy These)
The 3-5 patterns that repeat across the codebase. For each, include:
- What the pattern is (e.g., "Auth-gated API route")
- A concrete code snippet showing the canonical example
- Which file to copy from when creating a new one

This is the most actionable section — it tells a new dev "when you need to do X, copy this."

#### 7. Data Model
Entity-relationship overview:
- What tables/collections exist and what they represent
- Key relationships (foreign keys, join tables)
- Important constraints or business rules encoded in the schema
- Which fields are nullable, which have defaults, which are computed

#### 8. API Surface
List all API endpoints with:
- Method + path
- What it does (one line)
- Auth requirements
- Key request/response shapes (only for non-obvious ones)

Group by resource (e.g., all `/skills/*` routes together).

#### 9. Critical Flows
For each major user journey, trace the request through the system:
- What the user does (clicks button, submits form)
- What the client component does
- What API route handles it
- What DB queries run
- What side effects fire (SSE broadcast, notification creation, etc.)

Pick the 3-5 most important flows. Name exact files and functions.

#### 10. Available Skills & Automation
List every Claude Code skill available in the project:
- Skill name and one-line description
- When to use it
- What it does under the hood

This is unique to repos with Claude Code skills — it's part of the team's workflow.

#### 11. Gotchas & Tribal Knowledge
The things that aren't in the README but bite you on day 2:
- Framework quirks (version-specific behavior, beta APIs)
- Database gotchas (type casting, extension requirements)
- Build/deploy gotchas
- "Don't touch this" warnings
- Common error messages and their actual cause

Pull these from CLAUDE.md's "Known Gotchas" section, from code comments, and from patterns you observed while reading the source.

#### 12. Testing Strategy
- What test framework is used
- How to run tests (commands)
- What's tested and what isn't
- Testing patterns to follow for new code

#### 13. Deployment & Infrastructure
- Where does this run?
- How does deployment work? (push to main? manual? CI/CD?)
- Environment differences (dev vs staging vs prod)
- How to check if a deploy succeeded

#### 14. Open Questions & Known Gaps
Things you noticed while reading the code that a new developer should be aware of:
- TODOs in the code
- Missing test coverage
- Temporary workarounds
- Features described in docs but not yet implemented
- Known technical debt

## Step 3 — Write or present

### If markdown mode:
1. Write the full KT document to `KT.md` at the project root.
2. Read it back to verify all file paths referenced actually exist (glob check).
3. Fix any broken references.

### If interactive mode:
1. Present each section one at a time.
2. After each section, ask: "Questions about this? Or should I continue?"
3. Incorporate any clarifications into the explanation before moving on.
4. At the end, offer to write the whole thing to `KT.md` if they want a permanent artifact.

## Step 4 — Report

Tell the user:
- What was generated and where
- A 3-line summary of the project based on what you learned
- How many sections were populated vs. skipped (due to missing context)
- Suggest they review sections 6 (Key Patterns) and 11 (Gotchas) first — those are highest-value for onboarding
- Remind them that the KT doc is a snapshot — it should be regenerated periodically or when major changes ship
