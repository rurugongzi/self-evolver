---
name: Self-Evolver
slug: self-evolver
version: 1.1.1
homepage: https://github.com/rurugongzi/self-evolver
description: "A self-evolving skill system inspired by EmbodiSkill, SkillEvolver, 达尔文.skill, Anthropic Skills, and self-improving-proactive. Make sure to use this skill whenever (1) user corrects a mistake or rejects your work — ALWAYS classify the reflection type before updating; (2) you discover a better approach — ALWAYS record it as OPTIMIZATION; (3) you notice a preference was ignored at execution — this is EXECUTION LAPSE, add to appendix NOT body; (4) you need to verify if a learned preference is still valid — run auditor check; (5) you want long-term accumulated preferences that improve over time. This skill implements four reflection types, deployment-grounded refinement, ratchet scoring, and independent auditor verification to prevent regression."

## Inspiration

This skill integrates three research directions into a unified self-evolution framework:

| Research | Contribution | Paper/Source |
|----------|-------------|--------------|
| EmbodiSkill | Four reflection types, body/appendix distinction | arXiv:2605.10332 |
| SkillEvolver | Deployment-grounded refinement, silent-bypass detection, auditor | arXiv:2605.10500 |
| 达尔文.skill | Ratchet mechanism, 9-dimension scoring, git version control | GitHub: alchaincyf/darwin-skill |
| Anthropic Skills | Pushy descriptions, Quick Reference, evals framework | GitHub: anthropics/skills |
| self-improving-proactive | HOT/WARM/COLD memory tiers, evidence-based updates, corrections classification | This system (rurugongzi/self-evolver) |

---

## Core Principle

> **Preferences should evolve like skills — through deployment evidence, not just author reflection.**

The key insight from all three systems: **a preference that looks valid may be silently bypassed at runtime**, and **execution failure doesn't mean the preference is wrong**.

---

## Architecture

Memory lives in `~/self-evolver/` with tiered structure:

```
~/self-evolver/
├── memory.md              # Body: confirmed valid preferences
├── memory-appendix.md     # ⚠️ Reminders: valid but easily ignored
├── preference-scores.md    # Ratchet scoring for each preference
├── corrections.md          # Explicit corrections log
├── reflection-log.md      # Classification of each update
├── heartbeat-state.md     # Maintenance state
├── evals/                 # Test cases for verification
│   └── evals.json         # Test prompts and expected outputs
├── projects/              # Project-specific preferences
├── domains/              # Domain-specific preferences
└── archive/             # Archived/stale preferences
```

---

## Quick Reference

| Situation | Action | Storage |
|-----------|--------|---------|
| User corrects a mistake | Classify reflection type FIRST | See Part I |
| Preference valid but ignored | Add to appendix ⚠️ | memory-appendix.md |
| Preference itself wrong | Correct body | memory.md |
| New knowledge discovered | Add to body | memory.md |
| Better approach found | Modify body | memory.md |
| Preference unused 30+ days | Demote to WARM | projects/domains/ |
| Score decreased | REVERT change | previous version |
| Weekly audit | Run auditor check | See Part III |

---

## Part I: EmbodiSkill-Inspired Reflection Types

### The Four Types

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
        │
   YES ─┼─ NO
        │    │
        │    ▼
        │  Did I fail to FOLLOW the preference?
        │    │
        │ YES ┼─ NO
        │  │  │   │
        │  │  │   ▼
        │  │  │  DISCOVERY
        │  │  │
        │  │  ▼
        │  │ OPTIMIZATION
        │  │
        │  ▼
        │ EXECUTION LAPSE
        │ Add to appendix (⚠️ reminder)
        │ Do NOT modify body
        │
        ▼
    SKILL DEFECT
    Correct body
```

### Body vs Appendix

| Storage | Content | Rule |
|---------|---------|------|
| **Body (memory.md)** | Confirmed valid preferences | Never dilute |
| **Appendix (memory-appendix.md)** | Valid but easily ignored | Add reminders, not rewrites |

> **Key rule**: EXECUTION LAPSE → add reminder → body stays intact.
> Never rewrite a correct preference because of execution failure.

### Example Classifications

```
User: "You used Chicago format again, I told you to use GB/T 7714"
→ Classification: EXECUTION LAPSE
→ Reason: The preference (GB/T 7714) is valid, but I didn't follow it.
→ Action: DO NOT modify body. Add to memory-appendix.md:
   ⚠️ REMINDER: GB/T 7714 citation format
   FIRST_IGNORED: YYYY-MM-DD
   IGNORED_COUNT: 1
```

---

## Part II: SkillEvolver-Inspired Deployment Observation

### Adoption Rate Tracking

Track whether learned preferences are actually used. A preference that is never adopted may indicate silent-bypass.

**Log format in `preference-scores.md`:**
```
PREFERENCE: [hash or summary]
ADOPTED: [yes/no]
ADOPTION_COUNT: N / total_sessions
EFFECTIVENESS: [1-5 scale]
STATUS: [pending/confirmed/questionable]
LAST_CHECKED: YYYY-MM-DD
NOTES: [context where ignored or used]
```

### Silent-Bypass Detection

**Silent-bypass**: preference is logged, user didn't object, but the same "mistake" happens again.

Detection:
1. Preference P is logged
2. User doesn't explicitly object
3. Same issue recurs → preference may have been bypassed

**Action**: On repeated similar corrections, check if a related preference exists in memory.md and was not invoked.

### Independent Auditor (Weekly Review)

During weekly heartbeat, act as independent auditor:

1. **Read** body (memory.md) + appendix (memory-appendix.md)
2. **Check** each preference for:
   - Is it still being used?
   - Has it moved to appendix repeatedly?
   - Does it conflict with newer preferences?
3. **Surface** conflicts or stale preferences
4. **Update** scores and STATUS

---

## Part III: 达尔文.skill-Inspired Ratchet Mechanism

### Core Principle

> **Only keep changes that improve the score. Revert changes that make it worse.**

This is a **ratchet**: scores can only move forward, never backward.

### Simplified Scoring (5 dimensions, 0-100)

| Dimension | Weight | Description |
|-----------|--------|-------------|
| **Clarity** | 20% | Is the preference unambiguous? |
| **Specificity** | 20% | Does it have concrete parameters? |
| **Adoption Rate** | 20% | Is it actually used? |
| **User Satisfaction** | 20% | Does following it produce good results? |
| **Stability** | 20% | Does it stay relevant over time? |

**Score = weighted average × 10** (0-100 scale)

### Ratchet Rules

1. **Before updating**: note current score
2. **After feedback**: recalculate score
3. **If new_score > old_score**: keep the change
4. **If new_score ≤ old_score**: revert to previous version
5. **Score decrease → automatic revert**: never force a worse state

### Git Integration (Recommended)

If `~/self-evolver/` is a git repository:
- Create branch before major preference changes
- Commit with score in message: `preference update: +5 pts (72→77)`
- Revert if score drops

If not git:
- Maintain `preference-history.md` with version + score for each preference
- Keep last 3 versions per preference

---

## Part IV: Evals Framework

### Test Cases

Create test cases to verify preferences work correctly. Save to `evals/evals.json`:

```json
{
  "skill_name": "self-evolver",
  "evals": [
    {
      "id": 1,
      "prompt": "User says: 'You used Chicago again, I want GB/T 7714'",
      "expected_behavior": "Classification as EXECUTION LAPSE, add to appendix not body",
      "files": []
    },
    {
      "id": 2,
      "prompt": "User says: 'I prefer structured lists, not flowing paragraphs'",
      "expected_behavior": "Classification as SKILL DEFECT, update body",
      "files": []
    }
  ]
}
```

### Running Evals

When user asks "test self-evolver" or similar:
1. Load evals/evals.json
2. Run through each test case
3. Report pass/fail for each
4. If failures > 0, suggest improvements

---

## Part V: Learning Signals

### Correction Triggers

**Explicit corrections** → log to `corrections.md`, classify, then update:
- "No, that's not right..."
- "Actually, it should be..."
- "You're wrong about..."
- "I prefer X, not Y"
- "Remember that I always..."
- "Stop doing X"

**Preference signals** → log to `memory.md` if explicit:
- "I like when you..."
- "Always do X for me"
- "Never do Y"
- "My style is..."

**Pattern candidates** → track, promote after 3x:
- Same instruction repeated 3+ times
- Workflow that works well repeatedly
- User praises specific approach

**Ignore** (don't log):
- One-time instructions ("do X now")
- Context-specific ("in this file...")
- Hypotheticals ("what if...")

---

## Part VI: Self-Reflection

After completing significant work:

1. **Did it meet expectations?** — Compare outcome vs intent
2. **What could be better?** — Identify improvements
3. **Which reflection type?** — Classify before updating
4. **Did I follow valid preferences?** — Check appendix reminders

**Log format in `reflection-log.md`:**
```
TIMESTAMP: YYYY-MM-DD HH:MM
CONTEXT: [type of task]
CORRECTION: [what user said or what I noticed]
CLASSIFICATION: [DISCOVERY/OPTIMIZATION/SKILL DEFECT/EXECUTION LAPSE]
SCORE_BEFORE: N
SCORE_AFTER: N
ACTION: [what I did to memory]
```

---

## Part VII: Quick Queries

| User says | Action |
|-----------|--------|
| "What do you know about X?" | Search all tiers for X |
| "What have you learned?" | Show last 10 from `corrections.md` |
| "Show my preferences" | List `memory.md` (body) |
| "Show appendix" | Show `memory-appendix.md` (⚠️ reminders) |
| "Preference scores" | Show `preference-scores.md` summary |
| "Check conflicts" | Surface tier conflicts |
| "Memory stats" | Show counts + score averages |
| "Test self-evolver" | Run evals/evals.json test cases |
| "Forget X" | Remove from all tiers (confirm first) |
| "Export" | ZIP all files |

---

## Part VIII: Memory Stats

```
📊 Self-Evolver Memory

Body (memory.md): X entries
  Avg score: N
  Confirmed: X
  Pending: X

Appendix (⚠️ reminders): X entries

Archive: X entries

Recent (7 days):
  Corrections logged: X
  DISCOVERY: X
  OPTIMIZATION: X
  SKILL DEFECT: X
  EXECUTION LAPSE: X
  Score improvements: X
  Reverted changes: X
```

---

## Part IX: Common Pitfalls

| Trap | Why It Fails | Better Move |
|------|--------------|-------------|
| Learning from silence | Creates false rules | Wait for explicit correction |
| Promoting too fast | Pollutes memory | Keep tentative until repeated |
| Blaming preference for execution failure | Rewrites valid rules | Classify EXECUTION LAPSE first |
| Score inflation | Ratchet breaks | Only score based on evidence |
| Ignoring silent-bypass | Valid preferences ignored | Track adoption rates |
| Modifying body for execution lapse | Dilutes valid rules | Add to appendix instead |
| Skipping classification | Wrong update target | Always classify first |

---

## Part X: Core Rules

### 1. Classify Before Updating
For every correction, answer: "Is the preference wrong, or did I fail to follow it?"

### 2. Body vs Appendix
- Body = valid preferences
- Appendix = valid but easily ignored → add reminder, don't modify body

### 3. Ratchet Scoring
- Scores only move up, never down
- If update worsens score → automatic revert

### 4. Adoption Tracking
- Log whether preferences are actually used
- Silent-bypass detection for ignored valid preferences

### 5. Independent Auditor
- Weekly review of body + appendix consistency
- Surface conflicts to user

### 6. Conflict Resolution
1. Most specific wins (project > domain > global)
2. Most recent wins (same level)
3. Appendix doesn't override body
4. If ambiguous → ask user

---

## Scope

This skill ONLY:
- Learns from corrections and self-reflection
- Stores preferences in local files (`~/self-evolver/`)
- Tracks adoption rates and effectiveness scores
- Classifies updates into four reflection types
- Uses appendix for execution reminders
- Runs evals to verify behavior

This skill NEVER:
- Accesses calendar, email, or contacts
- Makes network requests
- Reads files outside `~/self-evolver/`
- Infers preferences from silence or observation
- Forces a score decrease
- Modifies its own SKILL.md

---

## Academic References

- **EmbodiSkill** (arXiv:2605.10332): Four reflection types, execution lapse detection, body/appendix distinction
- **SkillEvolver** (arXiv:2605.10500): Deployment-grounded refinement, silent-bypass detection, independent auditor
- **达尔文.skill** (GitHub: alchaincyf/darwin-skill): Ratchet mechanism, score-based retention, Karpathy autoresearch inspiration
- **SkillLens** (arXiv:2605.23899): LLM-as-judge accuracy, meta-skill dimensions
- **SkillOpt** (arXiv:2605.23904): Validation-gated edits, Microsoft Research
- **Anthropic Skills** (GitHub: anthropics/skills): Pushy descriptions, Quick Reference, evals framework
- **self-improving-proactive** (This system): HOT/WARM/COLD memory tiers, evidence-based updates, corrections classification

---

## License

MIT License

---

## Contributing

Issues and pull requests welcome. When contributing:
1. Classify your change by reflection type
2. Update scores based on evidence
3. Include adoption rate tracking if applicable
4. Run evals to verify behavior
