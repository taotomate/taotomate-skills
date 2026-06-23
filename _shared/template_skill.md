---
name: {skill-name}
description: '{What it does. Trigger: keywords for invocation.}'
version: "1.0.0"
author: {your-name}
generator_model: {model-used}
inherited_from: {origin-path}
model_tier: {high|medium|fast}
---

## Context & Triggers
**When to use this skill:**
- {situation 1}
- {situation 2}

## Prerequisites
- [ ] {requirement 1}
- [ ] {requirement 2}

## Execution Phases

> **[UNIVERSAL DRY-RUN / SIMULATION RULE]**
> If the user requests execution in `--dry-run` mode or asks for a "simulation", the agent will **NOT** execute commands that alter system state or call destructive MCP tools in the Action Phase.
> Instead, the agent will print the exact payload (JSON, code block, or parameters) it planned to execute, and will stop to wait for explicit human approval.

### 1. Diagnosis Phase
{Understand the problem before acting}

### 2. Action Phase
{Execute the main work}

### 3. Verification Phase
{Confirm success and report results}

## Guardrails (Critical Rules)

- {rule 1}
- {rule 2}

## Data Structures / Examples & Commands

{Key formats, templates, tables, and CLI commands needed for this skill}

## Troubleshooting

- **{Problem}**: {solution}
- **{Problem}**: {solution}
