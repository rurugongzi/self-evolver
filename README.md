# Self-Evolver

🧠 A self-evolving skill system for AI agents, inspired by EmbodiSkill, SkillEvolver, 达尔文.skill, and Anthropic Skills.

[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/Version-1.1.0-green.svg)](SKILL.md)

## Overview

Self-Evolver implements a **self-evolving preference system** that:
- Classifies updates into four EmbodiSkill-inspired reflection types
- Tracks adoption rates and effectiveness scores
- Uses ratchet mechanism for score-based retention
- Has independent auditor verification
- Includes evals framework for behavior verification

## Core Features

### Four Reflection Types

| Type | Meaning | Action |
|------|---------|--------|
| **DISCOVERY** | New knowledge | Add to body |
| **OPTIMIZATION** | Better approach exists | Modify body |
| **SKILL DEFECT** | Preference is wrong | Correct body |
| **EXECUTION LAPSE** | Valid but ignored | Add to appendix ⚠️ |

### Ratchet Mechanism

Scores only move **up**, never down. If an update worsens the score → automatic revert.

### Deployment Observation

Track whether learned preferences are actually used. Detect silent-bypass patterns.

## Architecture

```
~/self-evolver/
├── memory.md              # Body: confirmed valid preferences
├── memory-appendix.md     # ⚠️ Reminders: valid but easily ignored
├── preference-scores.md   # Ratchet scoring
├── corrections.md        # Explicit corrections log
├── reflection-log.md     # Classification of each update
├── heartbeat-state.md    # Maintenance state
├── evals/                # Test cases
│   └── evals.json       # Test prompts
├── projects/            # Project-specific preferences
├── domains/            # Domain-specific preferences
└── archive/          # Archived/stale preferences
```

## Quick Reference

| Situation | Action |
|-----------|--------|
| User corrects a mistake | Classify reflection type FIRST |
| Preference valid but ignored | Add to appendix ⚠️ |
| Preference itself wrong | Correct body |
| Score decreased | REVERT change |
| Weekly audit | Run auditor check |

## Academic References

- **EmbodiSkill** (arXiv:2605.10332): Four reflection types, body/appendix distinction
- **SkillEvolver** (arXiv:2605.10500): Deployment-grounded refinement, silent-bypass detection
- **达尔文.skill** (GitHub: alchaincyf/darwin-skill): Ratchet mechanism, score-based retention
- **Anthropic Skills** (GitHub: anthropics/skills): Pushy descriptions, Quick Reference, evals framework

## License

MIT License
