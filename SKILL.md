---
name: Self-Evolver
slug: self-evolver
version: 1.2.0
homepage: https://github.com/rurugongzi/self-evolver
description: "A self-evolving skill system inspired by EmbodiSkill, SkillEvolver, 达尔文.skill, Anthropic Skills, self-improving-proactive, and web-access. Make sure to use this skill whenever (1) user corrects a mistake or rejects your work — ALWAYS classify the reflection type before updating; (2) you discover a better approach — ALWAYS record it as OPTIMIZATION; (3) you notice a preference was ignored at execution — this is EXECUTION LAPSE, add to appendix NOT body; (4) you need to verify if a learned preference is still valid — run auditor check; (5) you want long-term accumulated preferences that improve over time. This skill implements four reflection types, deployment-grounded refinement, ratchet scoring, and independent auditor verification to prevent regression."
---

# Self-Evolver

A self-evolving skill system with **evidence-driven adaptation** and **browsing philosophy**.

## Inspiration

This skill integrates six research directions into a unified self-evolution framework:

| Research | Contribution | Source |
|----------|-------------|--------|
| EmbodiSkill | Four reflection types, body/appendix distinction | arXiv:2605.10332 |
| SkillEvolver | Deployment-grounded refinement, silent-bypass detection, auditor | arXiv:2605.10500 |
| 达尔文.skill | Ratchet mechanism, score-based retention | GitHub |
| Anthropic Skills | Pushy descriptions, Quick Reference, evals framework | GitHub |
| self-improving-proactive | HOT/WARM/COLD memory tiers, evidence-based updates | This system |
| web-access | Goal-oriented browsing philosophy, evidence-driven execution | eze-is |

---

## Core Principle

> **像人一样思考，兼顾高效与适应性的完成任务。**

### Evidence-Driven Execution

| Step | Action |
|------|--------|
| **① Define Goal** | What constitutes completion? What information/result is needed? |
| **② Choose Starting Point** | Most direct path to goal — WebSearch vs WebFetch vs CDP |
| **③ Execute & Validate** | Each result is evidence, not binary success/failure |
| **④ Adapt** | If direction is wrong, adjust immediately — don't keep retrying the same failed approach |
| **⑤ Complete** | Stop when success criteria are met — avoid over-operating |

> **Key insight**: A failed attempt doesn't mean "try harder" — it may mean "re-evaluate the goal itself".

---

## Architecture

Memory lives in `~/self-evolver/`:

```
~/self-evolver/
├── memory.md              # Body: confirmed valid preferences
├── memory-appendix.md     # ⚠️ Reminders: valid but easily ignored
├── preference-scores.md   # Ratchet scoring (0-100)
├── corrections.md         # Explicit corrections log
├── reflection-log.md      # Classification of each update
├── heartbeat-state.md     # Maintenance state
├── evals/evals.json      # Test cases
├── site-patterns/         # Domain-specific patterns (web-access inspired)
├── projects/             # Project-specific preferences
├── domains/             # Domain-specific preferences
└── archive/             # Archived/stale preferences
```

---

## Quick Reference

| Situation | Action |
|-----------|--------|
| User corrects a mistake | Classify reflection type FIRST |
| Preference valid but ignored | Add to appendix ⚠️ |
| Preference itself wrong | Correct body |
| New knowledge discovered | Add to body |
| Better approach found | Modify body |
| Score decreased | REVERT change |
| Weekly audit | Run auditor check |
| Multiple independent goals | Parallel execution via sub-agents |

---

## The Four Reflection Types

| Type | Meaning | Memory Action |
|------|---------|---------------|
| **DISCOVERY** | New knowledge not in current preferences | Add to body |
| **OPTIMIZATION** | Current preference valid, better approach exists | Modify body |
| **SKILL DEFECT** | Preference itself is wrong or ambiguous | Correct body |
| **EXECUTION LAPSE** | Preference valid but agent didn't follow it | Add to appendix |

### Classification Flowchart

```
User correction received
        │
        ▼
Is the preference ITSELF wrong?
   YES ─┴─ NO
        │
        ▼
Did I fail to FOLLOW the preference?
   YES ─┴─ NO
        │    │
        │    ▼
        │  DISCOVERY
        │
        ▼
    EXECUTION LAPSE
    → Add to appendix (⚠️ reminder)
    → Do NOT modify body
        │
        ▼
    SKILL DEFECT
    → Correct body
```

### Body vs Appendix

| Storage | Content | Rule |
|---------|---------|------|
| **Body** | Confirmed valid preferences | Never dilute |
| **Appendix** | Valid but easily ignored | Add reminders, not rewrites |

> **Key rule**: EXECUTION LAPSE → add reminder → body stays intact.

---

## Ratchet Mechanism

Scores only move **up**, never down. If an update worsens the score → automatic revert.

### Scoring (5 dimensions, 0-100)

| Dimension | Weight | Description |
|-----------|--------|-------------|
| **Clarity** | 20% | Is the preference unambiguous? |
| **Specificity** | 20% | Does it have concrete parameters? |
| **Adoption Rate** | 20% | Is it actually used? |
| **User Satisfaction** | 20% | Does following it produce good results? |
| **Stability** | 20% | Does it stay relevant over time? |

### Ratchet Rules

1. Before updating: note current score
2. After feedback: recalculate score
3. If new_score > old_score: keep the change
4. If new_score ≤ old_score: revert to previous version

---

## Evidence-Driven Learning

### From web-access Browsing Philosophy

**Core principle**: Each action produces evidence. Use it to update beliefs.

| Evidence Type | Interpretation | Action |
|---------------|----------------|--------|
| Preference adopted + good result | Preference works | Reinforce |
| Preference adopted + bad result | Preference needs revision | Modify body |
| Preference ignored + good result | Execution lapse | Add to appendix |
| Preference ignored + bad result | Preference may be wrong | Revise or remove |
| Silent bypass (3+ ignores) | Preference not working | Move to appendix |

### Learning Signals

**Explicit corrections** → log to `corrections.md`, classify, then update:
- "No, that's not right..."
- "I prefer X, not Y"
- "Remember that I always..."
- "Stop doing X"

**Pattern candidates** → track, promote after 3x:
- Same instruction repeated 3+ times
- Workflow that works well repeatedly
- User praises specific approach

**Ignore** (don't log):
- One-time instructions ("do X now")
- Context-specific ("in this file...")
- Hypotheticals ("what if...")

---

## Site Patterns (web-access Inspired)

After successful operations, record validated knowledge by domain:

```markdown
---
domain: example.com
updated: 2026-07-15
---

## Platform Characteristics
Architecture, anti-scraping behavior, login requirements

## Valid Patterns
Verified URL patterns, strategies, selectors

## Known Pitfalls
What fails and why
```

**When to use**:
- Target domain known → check `site-patterns/{domain}.md`
- Operation succeeds → update site pattern
- Operation fails → update and fallback to generic approach

---

## Common Pitfalls

| Trap | Why It Fails | Better Move |
|------|--------------|-------------|
| Learning from silence | Creates false rules | Wait for explicit correction |
| Promoting too fast | Pollutes memory | Keep tentative until repeated |
| Blaming preference for execution failure | Rewrites valid rules | Classify EXECUTION LAPSE first |
| Score inflation | Ratchet breaks | Only score based on evidence |
| Modifying body for execution lapse | Dilutes valid rules | Add to appendix instead |
| Over-planning | Static plans break on dynamic goals | Execute and adapt |
| Assuming failure means wrong method | Sometimes goal doesn't exist | Re-evaluate, don't just retry |

---

## Scope

**This skill ONLY:**
- Learns from corrections and self-reflection
- Stores preferences in local files (`~/self-evolver/`)
- Tracks adoption rates and effectiveness scores
- Classifies updates into four reflection types
- Uses appendix for execution reminders
- Records site patterns for reusable knowledge

**This skill NEVER:**
- Accesses calendar, email, or contacts
- Infers preferences from silence or observation
- Forces a score decrease
- Accesses external services without user request

---

## License

MIT License
