# taotomate-skills

> OpenCode skills: SDD workflow, Go testing, PR workflows, skill registry, and more.

Skills are specialized instruction sets that teach AI assistants how to work with specific workflows, libraries, and patterns. They provide on-demand context so the AI follows best practices and conventions.

## Skills

| Skill | Description | Trigger |
|-------|-------------|---------|
| **SDD Workflow** | | |
| [sdd-init](sdd-init/SKILL.md) | Initialize SDD context in any project | When starting SDD |
| [sdd-explore](sdd-explore/SKILL.md) | Investigate ideas before committing to a change | When exploring a feature |
| [sdd-propose](sdd-propose/SKILL.md) | Create change proposals | When proposing a change |
| [sdd-spec](sdd-spec/SKILL.md) | Write specifications with requirements | When writing specs |
| [sdd-design](sdd-design/SKILL.md) | Create technical design documents | When designing a solution |
| [sdd-tasks](sdd-tasks/SKILL.md) | Break down changes into task checklists | When planning tasks |
| [sdd-apply](sdd-apply/SKILL.md) | Implement tasks following specs and design | When implementing code |
| [sdd-verify](sdd-verify/SKILL.md) | Validate implementation against specs | When verifying work |
| [sdd-archive](sdd-archive/SKILL.md) | Close a completed change | When archiving |
| [sdd-onboard](sdd-onboard/SKILL.md) | Guided walkthrough of SDD workflow | When onboarding |
| **Meta-Skills** | | |
| [skill-creator](skill-creator/SKILL.md) | Create new skills | When creating a skill |
| [skill-optimizer](skill-optimizer/SKILL.md) | Audit and improve existing skills | When optimizing skills |
| [skill-registry](skill-registry/SKILL.md) | Create skill registry index | When registering skills |
| **Development** | | |
| [go-testing](go-testing/SKILL.md) | Go testing patterns + Bubbletea TUI | When writing Go tests |
| [branch-pr](branch-pr/SKILL.md) | PR creation with issue-first enforcement | When creating PRs |
| [chained-pr](chained-pr/SKILL.md) | Split large changes into chained PRs | When changes > 400 lines |
| [work-unit-commits](work-unit-commits/SKILL.md) | Plan commits as reviewable units | When committing |
| [comment-writer](comment-writer/SKILL.md) | Write warm, direct collaboration comments | When reviewing |
| [issue-creation](issue-creation/SKILL.md) | Create GitHub issues | When filing issues |
| **Quality** | | |
| [double-blind-review](double-blind-review/SKILL.md) | Parallel adversarial review protocol | When auditing code |
| [auditor](auditor/SKILL.md) | Recovery from failures or guardrail violations | When recovering |
| [error-miner](error-miner/SKILL.md) | Analyze transcripts for protocol deviations | When mining errors |
| [conversation-distillation](conversation-distillation/SKILL.md) | Technical distillation of conversations | When distilling threads |
| **LLM & Synapse** | | |
| [synapse](synapse/SKILL.md) | Multi-topic distillation engine | When distilling topics |
| [experimental-compress](experimental-compress/SKILL.md) | Experimental synapse distillation | When testing compress |
| [dual-execution-validation](dual-execution-validation/SKILL.md) | Cross-validate Cloud vs Local LLM | When comparing models |
| [cognitive-doc-design](cognitive-doc-design/SKILL.md) | Design documentation reducing cognitive load | When writing docs |
| **Specialized** | | |
| [fast-file-locator](fast-file-locator/SKILL.md) | Find files via Everything HTTP API | When locating files |
| [glossary-extractor](glossary-extractor/SKILL.md) | Extract technical vocabulary | When building glossaries |
| [sdd-local-distiller](sdd-local-distiller/SKILL.md) | Distill via local LLMs | When using local LLM |
| [sdd-telemetry](sdd-telemetry/SKILL.md) | SDD telemetry collection | When tracking metrics |
| [sdd-telemetry-backfill](sdd-telemetry-backfill/SKILL.md) | Backfill SDD telemetry data | When backfilling |
| [sdd-token-miner](sdd-token-miner/SKILL.md) | Token usage analytics | When analyzing tokens |

## Supported Agents & Skills Directory

Each agent reads skills from a specific directory. Copy the skills you want into the right path for your agent:

| Agent | macOS / Linux | Windows |
|-------|---------------|---------|
| **Claude Code** | `~/.claude/skills/` | `%USERPROFILE%\.claude\skills\` |
| **OpenCode** | `~/.config/opencode/skills/` | `%USERPROFILE%\.config\opencode\skills\` |
| **Gemini CLI** | `~/.gemini/skills/` | `%USERPROFILE%\.gemini\skills\` |
| **Cursor** | `~/.cursor/skills/` | `%USERPROFILE%\.cursor\skills\` |
| **VS Code Copilot** | `~/.copilot/skills/` | `%USERPROFILE%\.copilot\skills\` |
| **Codex** | `~/.codex/skills/` | `%USERPROFILE%\.codex\skills\` |
| **Windsurf** | `~/.codeium/windsurf/skills/` | `%USERPROFILE%\.codeium\windsurf\skills\` |
| **Antigravity** | `~/.gemini/antigravity/skills/` | `%USERPROFILE%\.gemini\antigravity\skills\` |

Inside each `skills/` directory, every skill lives in its own folder with a `SKILL.md`:

```
skills/
  sdd-apply/SKILL.md
  go-testing/SKILL.md
  branch-pr/SKILL.md
  ...
```

## Installation

### Quick Start

```bash
# Clone the repository
git clone https://github.com/taotomate/taotomate-skills.git

# Copy all skills to your agent's skills directory
# Replace <SKILLS_DIR> with the path from the table above

# Example: OpenCode (macOS/Linux)
cp -r taotomate-skills/* ~/.config/opencode/skills/

# Example: OpenCode (Windows PowerShell)
Copy-Item -Path "taotomate-skills\*" -Destination "$env:USERPROFILE\.config\opencode\skills\" -Recurse

# Example: Claude Code (macOS/Linux)
cp -r taotomate-skills/* ~/.claude/skills/

# Or copy specific skills only
cp -r taotomate-skills/sdd-apply ~/.config/opencode/skills/
cp -r taotomate-skills/go-testing ~/.config/opencode/skills/
```

### Manual Installation

1. Create the skills directory for your agent if it doesn't exist:

   **macOS / Linux:**
   ```bash
   mkdir -p ~/.config/opencode/skills
   ```

   **Windows (PowerShell):**
   ```powershell
   New-Item -ItemType Directory -Path "$env:USERPROFILE\.config\opencode\skills\" -Force
   ```

2. Copy the skill folder(s) you want:

   **macOS / Linux:**
   ```bash
   cp -r taotomate-skills/sdd-apply ~/.config/opencode/skills/
   ```

   **Windows (PowerShell):**
   ```powershell
   Copy-Item -Path "taotomate-skills\sdd-apply" -Destination "$env:USERPROFILE\.config\opencode\skills\" -Recurse
   ```

3. Reference the skill in your agent's configuration file (e.g., `AGENTS.md` for OpenCode):
   ```markdown
   ## Skills
   | `{skill-name}` | {Description} | [SKILL.md](skills/{skill-name}/SKILL.md) |
   ```

## Skill Structure

```
{skill-name}/
├── SKILL.md            # Main skill file (required)
├── references/         # Optional - supporting docs
└── tests/              # Optional - skill tests
```

## Contributing

See [skill-creator](skill-creator/SKILL.md) to create new skills and [skill-optimizer](skill-optimizer/SKILL.md) to audit them. Before submitting a PR, load the [branch-pr](branch-pr/SKILL.md) skill.

## License

MIT
