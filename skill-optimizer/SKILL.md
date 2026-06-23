---
name: skill-optimizer
description: Evaluates purpose compliance, detects script candidates, optimal tier, duplicates, external dependencies, and performs legacy migration. Generates dual report (file + engram) and optional auto-fix. All phases run inline (grep, glob, read, edit, bash).
version: 2.0.2
author: TaoTomate
generator_model: deepseek-v4-flash-free
model_tier: high
inherited_from: merged:skill-optimizer@1.0.0 + skill-migrator@1.2.0
---

## Context & Triggers
**When to use this skill:**
- Full skill ecosystem audit: "optimize skills", "audit skills", "skill health check"
- Individual analysis: "optimize skill <name>", "review skill <name>"
- Script candidate detection: "skill to script", "extract logic to execution/"
- Legacy migration: "migrate skill", "refactor old skill", "update skill architecture"
- Triggers: "optimize skills", "audit skills", "skill health check", "refactor skills", "migrate skill"

## Prerequisites
- [ ] Updated `skill-registry` (`.atl/skill-registry.md` or engram)
- [ ] Read access to target skill directory, write access for auto-fix
- [ ] `template_skill.md` available as structural reference (from _shared/)

## Execution Phases

> **[UNIVERSAL DRY-RUN / SIMULATION RULE]**
> If the user requests execution in `--dry-run` mode or asks for a "simulation", the agent will **NOT** execute commands that alter system state or call destructive MCP tools in the Action Phase. 
> Instead, the agent will print the exact payload (JSON, code block, or parameters) it planned to execute, and will wait for explicit human approval.

### 0. Preparation Phase (Inline)
- Load references: `skill-registry`, `template_skill.md`
- Resolve target skill list: batch (glob all skills) or individual (specified name)
- Cache the compact rules section from the registry for the session

### 1. Structural Analysis Phase (Fast - grep, glob, read)
- For each skill: read SKILL.md, validate frontmatter fields
- Verify required sections: Context & Triggers, Prerequisites, Execution Phases, Guardrails, Data Structures / Examples
- Detect legacy format (v1.0: missing DRY-RUN rule, missing Guardrails, no YAML frontmatter)
- Confirm PRESENCE of universal DRY-RUN rule under "## Execution Phases"
- Verify `model_tier` is in {high, medium, fast}
- Output: `structural_report.json` per skill (inline dict, saved to engram or printed)

### 2. Functional Analysis Phase (High - LLM Delegation)
- For each skill: deep semantic analysis:
  - **Intent vs Reality**: Do triggers + description predict actual invocation?
  - **Minimal Path**: Do phases have redundant steps? Can they be collapsed?
  - **Decision Points**: Where does the LLM decide vs just format? Does it justify the tier?
  - **Failure Modes**: Do guardrails cover real failures?
  - **External Coupling**: CLI dependencies without version pinning/fallback?
  - **Testability**: Is output objectively verifiable? Associated test?
  - **Token Efficiency**: Are compact rules concise? Noise in prompt?
- Output: `functional_report.json` per skill

### 3. Skill vs Script Phase (Inline - grep + judgment)
- Apply simple heuristics:
  - Pure deterministic steps (no LLM decisions) → candidate
  - Heavy external CLI calls with no LLM judgment → candidate
  - `sdd-*` skills → never candidate (pipeline cohesion)
  - LLM-heavy decision flow → not candidate
- Output: `script_candidate_report.json` per skill

### 4. Duplicate/Overlap Detection Phase (Inline - grep + read)
- Compare (triggers + description + key phase names) across skill pairs
- High keyword overlap → flag as potential duplicate
- Validate: real duplicate, complementary, or false positive
- Output: `duplicates_report.json`

### 5. External Dependencies Phase (Fast - grep)
- Scan skills for calls to: yt-dlp, ffmpeg, gh, node, python, curl, jq, etc.
- Generate skill × dependency matrix
- Output: `dependencies_report.json`

### 6. Consolidated Scorecard Phase (Inline)
- Combine reports 1-5 into per-skill scorecard:
  ```json
  {
    "skill": "name",
    "structural_score": 0.0-1.0,
    "functional_score": 0.0-1.0,
    "script_candidate": true/false,
    "script_score": 0.0-1.0,
    "tier_actual": "high|medium|fast",
    "tier_optimal": "high|medium|fast",
    "legacy_format": true/false,
    "duplicates": ["skill2", "skill3"],
    "external_deps": [{"tool": "yt-dlp", "pinned": false, "fallback": false}],
    "recommendations": ["..."]
  }
  ```

### 7. Global Report Phase (High - LLM Synthesis)
- Generate audit report (inline or file) with: executive summary, scorecards, fix prioritization, dependency matrix, duplicates, legacy migration candidates
- Save to engram: `mem_save(topic_key="skill-audit/{timestamp}", type="architecture")`

### 8. Optional Auto-Fix Phase (Medium - With Confirmation)
- **Only if `--apply` flag + interactive confirmation per skill**
- Safe fixes: inject DRY-RUN rule, fix frontmatter, add/update model_tier
- Script extraction: create `execution/{skill}.py`, thin the skill to a wrapper
- **NEVER** auto-fix `sdd-*` or `skill-*` (core) skills without explicit human confirmation
- Invoke `skill-registry` post-change
- Output: change log

#### 8a. Legacy Migration Sub-Phase (Medium - With Confirmation)
- **Only if `--apply` flag + skill detected as legacy format (v1.0)**
- **Complexity Filter**: If the skill delegates aggressively to sub-agents, invokes external scripts (node, python), or belongs to `sdd-*`: **SKIP** with explanation. If individual mode: abort and request manual rewrite.
- **Semantic Mapping**: Remap old structure to new template:
  - *When to use / Triggers* → `Context & Triggers`
  - *Implicit env requirements* → `Prerequisites`
  - *Workflow / Steps* → `Execution Phases` (Diagnosis, Action, Verification)
  - *Critical Rules* → `Guardrails (Critical Rules)`
  - *Code Examples / Bash Commands* → `Data Structures / Examples & Commands`
- **Traceability Injection**: Add to YAML frontmatter:
  ```yaml
  generator_model: [current model]
  inherited_from: [original SKILL.md path]
  migrated_by: skill-optimizer@2.0.1
  ```
- **Dry-Run Rule Injection**: Inject the universal DRY-RUN block under `## Execution Phases`
- **Residue Analysis**: Compare full original vs mapped. Any block that doesn't fit → append under `## ⚠️ Migration Residue (Evolution Feedback)`
- Overwrite the old `SKILL.md` with the new content

## Guardrails (Critical Rules)
- **NEVER** modify skills without explicit `--apply` (dry-run by default)
- **NEVER** modify `skill-optimizer` itself (avoid recursion)
- **NEVER** auto-fix `sdd-*` or `skill-*` core — only report + human confirmation
- **NEVER** silently delete code examples, commands, or documentation links during migration. Force them to Migration Residue.
- **ALWAYS** generate report before any change
- **ALWAYS** single JSON line on stdout for script execution
- **ALWAYS** verify target directory exists before scanning — if missing, abort with clear error
- **ALWAYS** handle unreadable SKILL.md gracefully — skip with warning, don't crash the batch

## Data Structures / Examples & Commands

### Traceability Seal (Frontmatter Injection for Migration)
```yaml
generator_model: [the raw LLM model you use, e.g. gemini-1.5-pro]
inherited_from: [Absolute or relative path of the original SKILL.md file]
migrated_by: skill-optimizer@2.0.1
```

### Output JSON Line (stdout)
```json
{"status": "success", "data": {"report_path": ".atl/skill-audit-report.md", "skills_analyzed": 35, "script_candidates": 3, "tier_mismatches": 2, "duplicates": 1, "migrated": 2}, "error_log": ""}
```

### Report Format
```markdown
## Executive Summary
## Per-Skill Scorecards
## Fix Prioritization
## Dependency Matrix
## Duplicates
## Legacy Migration Candidates
```

## Troubleshooting
- *If `skill-registry` is missing*: Run `skill-registry` first or use fallback direct scan of the skills directory.
- *If dry-run mode*: Only generate reports, do not write changes.
- *If migration context loss occurs and file is emptied*: Restore original from source and process manually.
- *If an `sdd-*` skill is detected as script_candidate*: Ignore — SDD pipeline is not extractable to script.
