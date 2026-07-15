# Self-Evolver

🧠 A self-evolving skill system for AI agents, inspired by EmbodiSkill, SkillEvolver, and 达尔文.skill.

[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![arXiv](https://img.shields.io/badge/arXiv-2605.10332-green.svg)](https://arxiv.org/abs/2605.10332)
[![arXiv](https://img.shields.io/badge/arXiv-2605.10500-green.svg)](https://arxiv.org/abs/2605.10500)

## What is Self-Evolver?

Self-Evolver is a preference management skill that helps AI agents learn from corrections and improve over time. Unlike simple preference logging, Self-Evolver implements **evidence-based evolution** with:

- **Four reflection types** (DISCOVERY/OPTIMIZATION/SKILL DEFECT/EXECUTION LAPSE)
- **Deployment-grounded refinement** (track if preferences are actually used)
- **Ratchet scoring** (scores only go up, never down)
- **Independent auditor** (weekly verification)
- **Body/Apendix distinction** (don't dilute valid preferences)

## Quick Start

### Installation

```bash
# Clone the repo
git clone https://github.com/rurugongzi/self-evolver.git ~/self-evolver

# Or create directory manually
mkdir -p ~/self-evolver
```

### Create Required Files

```bash
cd ~/self-evolver

# Create memory files
touch memory.md memory-appendix.md preference-scores.md corrections.md reflection-log.md heartbeat-state.md

# Create directories
mkdir -p projects domains archive
```

### Load in Hermes

```
/skill load self-evolver
```

## Core Concepts

### The Four Reflection Types

| Type | Meaning | Action |
|------|---------|--------|
| **DISCOVERY** | Found new knowledge | Add to body |
| **OPTIMIZATION** | Better approach exists | Modify body |
| **SKILL DEFECT** | Preference itself is wrong | Correct body |
| **EXECUTION LAPSE** | Didn't follow valid preference | Add to appendix |

### Body vs Appendix

| Storage | Content | Rule |
|---------|---------|------|
| **Body** | Confirmed valid preferences | Never dilute |
| **Appendix** | Valid but easily ignored | Add reminder, don't rewrite |

### Ratchet Scoring

```
Score = Clarity(20%) + Specificity(20%) + Adoption(20%) + Satisfaction(20%) + Stability(20%)
```

Scores only move UP. If an update worsens the score → automatic revert.

## Architecture

```
~/self-evolver/
├── memory.md              # Body: valid preferences
├── memory-appendix.md     # ⚠️ Reminders for ignored valid preferences
├── preference-scores.md    # Ratchet scoring
├── corrections.md         # Explicit corrections log
├── reflection-log.md      # Classification of each update
├── heartbeat-state.md     # Maintenance state
├── projects/              # Project-specific
├── domains/              # Domain-specific
└── archive/              # Archived/stale
```

## Research Inspiration

| System | Key Contribution |
|--------|-----------------|
| **EmbodiSkill** (arXiv:2605.10332) | Four reflection types, body/appendix distinction |
| **SkillEvolver** (arXiv:2605.10500) | Deployment observation, silent-bypass detection, auditor |
| **达尔文.skill** (GitHub: alchaincyf/darwin-skill) | Ratchet mechanism, 9-dimension scoring |

## Example

```
User: "You used Chicago format again, I told you to use GB/T 7714"

Classification: EXECUTION LAPSE
Reason: The preference (GB/T 7714) is valid, but I didn't follow it.

Action:
1. DO NOT modify the body preference
2. Add to memory-appendix.md:
   ⚠️ REMINDER: GB/T 7714 citation format
   FIRST_IGNORED: 2026-07-15
   IGNORED_COUNT: 1
```

## License

MIT License

## Citation

If this work is useful for your research, please cite:

```bibtex
@misc{self-evolver2026,
  title={Self-Evolver: A Self-Evolving Skill System for AI Agents},
  author={rurugongzi},
  year={2026},
  howpublished={\url{https://github.com/rurugongzi/self-evolver}},
  note={Inspired by EmbodiSkill, SkillEvolver, and 达尔文.skill}
}
```
