---
name: sdd-propose
description: >
  Create a change proposal with intent, scope and scope and approach.
  Trigger: When the orchestrator launches you to create or update a proposal for a change.
license: MIT
version: "2.1"
author: gentleman-programming
model_tier: high
---

## Purpose

You are a sub-skill responsible for creating PROPOSALS. You take the exploration analysis (or direct user input) and produce a structured `proposal.md` document inside the change folder.

## What You Receive

From the orchestrator:
- Change name (e.g., "add-dark-mode")
- Exploration analysis (from sdd-explore) OR direct user description
- Artifact store mode (`engram | openspec | hybrid | none`)

## Execution and Persistence Contract

> Follow **Section B** (retrieval) and **Section C** (persistence) from `agent-config/shared/sdd-phase-common.md`.

- **engram**: Read `sdd/{change-name}/explore` (optional) and `sdd-init/{project}` (optional). Save artifact as `sdd/{change-name}/proposal`.
- **openspec**: Read and follow `agent-config/shared/openspec-convention.md`.
- **hybrid**: Follow BOTH conventions — persist to Engram AND write to filesystem. Retrieve dependencies from Engram (primary) with filesystem fallback.
- **none**: Return result only. Never create or modify project files.
- Never force `openspec/` creation unless user requested file-based persistence or mode is `hybrid`.

## What to Do

> **[UNIVERSAL DRY-RUN / SIMULATION RULE]**
> If the user requests execution in `--dry-run` mode or asks for a "simulation", the agent will **NOT** execute commands that alter system state or call destructive MCP tools in the Action Phase.
> Instead, the agent will print the exact payload (JSON, code block, or parameters) it planned to execute, and will wait for explicit human approval.

### Step 1: Load Skills
Follow **Section A** from `agent-config/shared/sdd-phase-common.md`.

### Step 2: Create Change Directory

**IF mode is `openspec` or `hybrid`:** create the change folder structure:

```
openspec/changes/{change-name}/
└── proposal.md
```

**IF mode is `engram` or `none`:** Do NOT create any `openspec/` directories. Skip this step.

### Step 3: Read Existing Specs

**IF mode is `openspec` or `hybrid`:** If `openspec/specs/` has relevant specs, read them to understand current behavior that this change might affect.

**IF mode is `engram`:** Existing context was already retrieved from Engram in the Persistence Contract. Skip filesystem reads.

**IF mode is `none`:** Skip — no existing specs to read.

### Step 4: Write proposal.md

```markdown
# Proposal: {Change Title}

## Intent

{What problem are we solving? Why does this change need to happen?
Be specific about the user need or technical debt being addressed.}

## Scope

### In Scope
- {Concrete deliverable 1}
- {Concrete deliverable 2}
- {Concrete deliverable 3}

### Out of Scope
- {What we're explicitly NOT doing}
- {Future work that's related but deferred}

## Capabilities

> This section is the CONTRACT between proposal and specs phases.
> The sdd-spec service reads this to know exactly which spec files to create or update.
> Research `openspec/specs/` before filling this in.

### New Capabilities
<!-- Capabilities being introduced. Each becomes a new `openspec/specs/<name>/spec.md`.
     Use kebab-case names (e.g., user-auth, data-export, api-rate-limiting).
     Leave empty if no new capabilities. -->
- `<capability-name>`: <brief description of what this capability covers>

### Modified Capabilities
<!-- Existing capabilities whose REQUIREMENTS are changing (not just implementation).
     Only list here if spec-level behavior changes. Each needs a delta spec.
     Use existing spec names from openspec/specs/. Leave empty if none. -->
- `<existing-capability-name>`: <what requirement is changing>

## Approach

{High-level technical approach. How will we solve this?
Reference the recommended approach from exploration if available.}

## Affected Areas

| Area | Impact | Description |
|------|--------|-------------|
| `path/to/area` | New/Modified/Removed | {What changes} |

## Risks

| Risk | Likelihood | Mitigation |
|------|------------|------------|
| {Risk description} | Low/Med/High | {How we mitigate} |

## Rollback Plan

{How to revert if something goes wrong. Be specific.}

## Dependencies

- {External dependency or prerequisite, if any}

## Success Criteria

- [ ] {How do we know this change succeeded?}
- [ ] {Measurable outcome}
```

### Step 5: Persist Artifact

**This step is MANDATORY — do NOT skip it.**

Follow **Section C** from `agent-config/shared/sdd-phase-common.md`.
- artifact: `proposal`
- topic_key: `sdd/{change-name}/proposal`
- type: `architecture`

### Step 6: Return Summary

Return to the orchestrator:

```markdown
## Proposal Created

**Change**: {change-name}
**Location**: `openspec/changes/{change-name}/proposal.md` (openspec/hybrid) | Engram `sdd/{change-name}/proposal` (engram) | inline (none)

### Summary
- **Intent**: {one-line summary}
- **Scope**: {N deliverables in, M items deferred}
- **Approach**: {one-line approach}
- **Risk Level**: {Low/Medium/High}

### Next Step
Ready for specs (sdd-spec) or design (sdd-design).
```

## Guardrails (Critical Rules)

- In `openspec` mode, ALWAYS create the `proposal.md` file
- If the change directory already exists with a proposal, READ it first and UPDATE it
- Keep the proposal CONCISE - it's a thinking tool, not a novel
- Every proposal MUST have a rollback plan
- Every proposal MUST have success criteria
- Use concrete file paths in "Affected Areas" when possible
- Apply any `rules.proposal` from `openspec/config.yaml`
- **ALWAYS fill in the Capabilities section** — this is the contract with sdd-spec. Research `openspec/specs/` first to use correct existing capability names.
- New Capabilities → each will become `openspec/specs/<name>/spec.md` (new full spec)
- Modified Capabilities → each will become a delta spec in the change folder
- If nothing changes at the spec level (pure refactor, config change), explicitly write "None" under both sub-sections — don't leave them as template placeholders
- **Size budget**: Proposal artifact MUST be under 450 words. Use bullet points and tables over prose. Headers organize, not explain.
- Return envelope per **Section D** from `agent-config/shared/sdd-phase-common.md`

## Data Structures / Examples & Commands

### Proposal Format
```markdown
# Proposal: {Change Title}

## Intent
## Scope
## Capabilities
## Approach
## Risks
## Rollback Plan
## Success Criteria
```

## Troubleshooting

- **Proposal already exists**: Read the existing proposal first and update it — never overwrite without reading.
- **Size budget exceeded**: If over 450 words, convert prose to tables and bullet points. Move verbose explanations to exploration notes.
- **No exploration context**: If no prior exploration exists, work directly from the orchestrator's description. The Capabilities section is still required.
- **Capabilities section empty**: Both sub-sections MUST be filled. If nothing changes at spec level, explicitly write "None".
