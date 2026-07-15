# Self-Evolver

🧠 A self-evolving skill system for AI agents, inspired by EmbodiSkill, SkillEvolver, 达尔文.skill, Anthropic Skills, self-improving-proactive, and web-access.

[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/Version-1.2.0-green.svg)](SKILL.md)

## Overview

Self-Evolver implements **evidence-driven self-evolution** with a unified framework:

- **Four Reflection Types**: DISCOVERY / OPTIMIZATION / SKILL DEFECT / EXECUTION LAPSE
- **Ratchet Mechanism**: Scores only move up, automatic revert on regression
- **Evidence-Driven Execution**: Like web-access browsing philosophy — goal-oriented, adapt while executing
- **Site Patterns**: Domain-specific knowledge accumulation
- **HOT/WARM/COLD Memory Tiers**: Structured preference storage

## Core Innovation: Evidence-Driven Adaptation

From web-access browsing philosophy:
> **像人一样思考，兼顾高效与适应性的完成任务。**

| Step | Action |
|------|--------|
| ① Define Goal | What constitutes completion? |
| ② Choose Starting Point | Most direct path to goal |
| ③ Execute & Validate | Each result is evidence |
| ④ Adapt | Adjust immediately if wrong direction |
| ⑤ Complete | Stop when criteria met |

## Architecture

```
~/self-evolver/
├── memory.md              # Body: confirmed valid preferences
├── memory-appendix.md     # ⚠️ Reminders: valid but easily ignored
├── preference-scores.md   # Ratchet scoring (0-100)
├── corrections.md        # Explicit corrections log
├── reflection-log.md      # Classification of each update
├── heartbeat-state.md     # Maintenance state
├── evals/evals.json      # Test cases
├── site-patterns/         # Domain-specific patterns
├── projects/             # Project-specific preferences
├── domains/             # Domain-specific preferences
└── archive/             # Archived/stale preferences
```

## Four Reflection Types

| Type | Meaning | Action |
|------|---------|--------|
| **DISCOVERY** | New knowledge | Add to body |
| **OPTIMIZATION** | Better approach exists | Modify body |
| **SKILL DEFECT** | Preference is wrong | Correct body |
| **EXECUTION LAPSE** | Valid but ignored | Add to appendix ⚠️ |

## Academic References

| Research | Contribution | Source |
|----------|-------------|--------|
| EmbodiSkill | Four reflection types, body/appendix distinction | arXiv:2605.10332 |
| SkillEvolver | Deployment-grounded refinement, auditor | arXiv:2605.10500 |
| 达尔文.skill | Ratchet mechanism, score-based retention | GitHub |
| Anthropic Skills | Pushy descriptions, Quick Reference, evals | GitHub |
| self-improving-proactive | HOT/WARM/COLD memory tiers | This system |
| web-access | Goal-oriented browsing philosophy | eze-is |

## License

MIT License
