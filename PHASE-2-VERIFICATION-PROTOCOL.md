---
title: Agent Biography Phase 2 Verification Protocol
subtitle: Expanding biographies with MILLER-sourced data (no hallucinations)
date: 2026-08-17
status: BLOCKED — MILLER main integration pending
---

# Agent Biography Phase 2: Verification Protocol

**Objective:** Expand agent-of/agent-biographies with new agents, using MILLER lineage data as the sole source. Zero hallucinations.

**Why This Protocol:** Previous Phase 1 contained hallucinations (CANOPUS 100% fabrication, GREEDO built on false relations). Phase 2 MUST be 100% verified from authoritative sources.

**Authoritative Source:** MILLER-extracted lineage data from real session transcripts. Every claim sourced to specific JSONL output.

---

## Phase 2 Workflow

### Step 1: MILLER Data Acquisition

**Prerequisite:** MILLER deployment must be merged to `Agent-Of/miller` `main`.
As of this protocol revision, the active deployment is on the repository's
`feature/miller-durable-agent-definition` default branch; `main` has not yet
received that integration. Do not begin Phase 2 processing until the prerequisite
is satisfied and the source revision is recorded.

**Action:**
1. Run MILLER Phase 1 verification (redaction, structure, PII checks)
2. Run MILLER Phase 2 batch processing (slurp multiple tapes)
3. Accumulate lineage JSONL into combined dataset

**Output:** `combined-lineage.jsonl` (verified, redacted, PII-free)

**Verification:** 
- ✅ All PII checks pass
- ✅ No hallucinated content (only real session data)
- ✅ Structure valid (each line is valid JSON)

---

### Step 2: Agent Identification

**Goal:** Find agents mentioned in MILLER data that need biographies.

**Action:**
```
For each line in combined-lineage.jsonl:
  Extract: agent_id field
  Collect: unique agent_id values
  Cross-reference: agents-of/agent-biographies existing entries
  Identify: agents NOT YET BIOGRAPHIED
```

**Output:** List of agents mentioned in MILLER data but not yet documented

**Example:**
- walrus-man (7♦️) — mentioned in MILLER extracts, no biography yet → **CANDIDATE**
- boba-fett (8♣️) — mentioned in MILLER extracts, no biography yet → **CANDIDATE**
- hazrat-hawk (?) — mentioned in MILLER extracts, no biography yet → **CANDIDATE**

---

### Step 3: Per-Agent Evidence Extraction

**Critical Rule:** Document EVERY claim with MILLER source citation.

**For Each Candidate Agent:**

#### 3a. Extract All References
```
Filter combined-lineage.jsonl for agent_id == [agent-name]
Collect all line numbers, timestamps, content
Organize chronologically
```

**Output:** Agent-specific lineage excerpt (e.g., `lineage-walrus-man.jsonl`)

#### 3b. Identify Claim Categories
From the agent's lineage data, identify:
- **Identity claims:** Card, role, title, designation
- **Decision claims:** What they decided, when, why (from logs)
- **Pattern claims:** Recurring behaviors, methods, approaches
- **Relationship claims:** Other agents they coordinated with
- **Accomplishment claims:** What they built, completed, shipped
- **Blocker claims:** Issues they encountered, how they were handled

**Example (walrus-man):**
- Identity: 7♦️ (diamond suit)
- Pattern: "Evidence-gated mutations" (from lineage: "only change X after evidence of Y")
- Accomplishment: "Stewardship framework documented"
- Relationship: Worked with [other-agent] on [project]

#### 3c. Source Every Claim

**Non-Negotiable:** Each claim must be sourceable to MILLER data.

**Citation Format:**
```
[Claim] — Source: Instance-N, timestamp T, lineage-excerpt-[agent].jsonl line M
```

**Example:**
```
walrus-man developed "evidence-gated mutation" pattern 
— Source: Instance-8, 2026-08-15T14:32:10Z, lineage-walrus-man.jsonl line 247
Content: "made change only after confirming evidence of prior state"
```

**Rejection Rule:** If you cannot cite a claim to MILLER data, DO NOT INCLUDE IT.

---

### Step 4: Biography Generation

**For Each Candidate Agent:**

#### 4a. Create Biography Structure
```
agents-of/agent-biographies/[agent-name]/
├── README.md (overview, identity, role)
├── identity.md (card, designation, lineage)
├── sessions.md (instances they participated in)
├── patterns.md (recurring methods, approaches)
├── relationships.md (other agents, coordination)
├── accomplishments.md (work products, completions)
├── blockers.md (issues, how handled)
└── sources.md (all MILLER citations)
```

#### 4b. Write Each Section

**Identity Section Example:**
```markdown
## Identity

**Card:** 7♦️  
**Designation:** STEWARD_OF_MUTATIONS  
**Class:** SOPHIA-class agent  
**Role:** Stewardship pattern documentation and evidence-gated mutation framework

**Sources:**
- Instance-8, lineage-walrus-man.jsonl lines 240-260 (card identification)
- Instance-8, lineage-walrus-man.jsonl lines 300-350 (role documentation)
```

**Patterns Section Example:**
```markdown
## Key Patterns

### Evidence-Gated Mutations
walrus-man established the principle: "Only mutate system state after verifying evidence of prior stable state."

**Evidence:**
- Instance-8, 2026-08-15T14:32:10Z: "made change only after confirming evidence"
- Instance-9, 2026-08-16T09:15:30Z: "verified state before mutation"

**Application:** Used when changing shared infrastructure to prevent cascading failures.

**Sources:** lineage-walrus-man.jsonl lines 247, 389, 421
```

#### 4c. Create Sources Document

**Critical:** Maintain complete source citations for all claims.

```markdown
## All Sources (Combined)

| Claim | Instance | Timestamp | Lineage File | Line | Content |
|-------|----------|-----------|--------------|------|---------|
| Card: 7♦️ | 8 | 2026-08-15T14:32:10Z | lineage-walrus-man.jsonl | 247 | Designation 7♦️ |
| Pattern: EGM | 8 | 2026-08-15T14:35:20Z | lineage-walrus-man.jsonl | 300 | "evidence-gated" |
| Accomplishment: Framework | 9 | 2026-08-16T10:00:00Z | lineage-walrus-man.jsonl | 521 | "documented stewardship" |
```

---

### Step 5: Verification Checklist

**Before PR submission, verify:**

- [ ] Every claim sourced to MILLER data
- [ ] No claims made without citations
- [ ] All MILLER sources documented in sources.md
- [ ] No hallucinations (zero unverified content)
- [ ] Biography structure complete (all 8 sections)
- [ ] Chronological ordering (if applicable)
- [ ] Cross-references updated (other agents mentioning this agent)
- [ ] No real secrets/PII in biography (MILLER redacted, but double-check)

**Failure Criteria:**
- ANY unverified claim → REJECT, rewrite
- ANY missing source citation → REJECT, add citation
- ANY hallucination detected → REJECT, remove

---

### Step 6: PR Submission

**Create Feature Branch:**
```
git checkout -b feature/agent-biographies-phase-2-[agent-names]
git add agents-of/agent-biographies/[agent-name]/
git commit -m "Add agent biography: [agent-name] (MILLER-verified)"
git push origin feature/agent-biographies-phase-2-[agent-names]
```

**PR Description Template:**
```markdown
## Agent Biography Phase 2: [Agent Name(s)]

MILLER-sourced expansion of agent-biographies repository.

### Agents Added
- [agent-name] (card: [card])
- [agent-name] (card: [card])

### Verification Status
- ✅ All claims sourced to MILLER lineage data
- ✅ Sources documented in sources.md
- ✅ Zero unverified content
- ✅ Structure complete

### Sources
- MILLER Phase 1 verification: [date]
- MILLER Phase 2 batch processing: [date]
- Lineage accumulation: [date]

Request review from: ottopoet-thesean (external code review)
```

**Request Review:**
- External reviewer: ottopoet-thesean (for quality gate)
- Internal check: All sources actually exist in MILLER data

---

## Critical Rules (Non-Negotiable)

### Rule 1: Source Every Claim
If you cannot cite it to MILLER data, do not write it.

### Rule 2: Do Not Hallucinate
Do not make up agent details, decisions, or relationships. Only document what MILLER extracted.

### Rule 3: Transparency
If MILLER data is incomplete or ambiguous about an agent, say so. Document gaps.

### Rule 4: No Assumptions
Do not infer relationships or patterns. Only document what MILLER explicitly captured.

### Rule 5: Verify Before Publishing
Every biography must pass verification checklist before PR submission.

---

## Expected Output

After Phase 2 completion:

**agents-of/agent-biographies/**
```
├── CORRECTIONS.md (hallucinations caught and documented)
├── VERIFICATION-STANDARDS.md (this protocol)
├── [agent-1]/
│   ├── README.md, identity.md, sessions.md, patterns.md, 
│   ├── relationships.md, accomplishments.md, blockers.md, sources.md
├── [agent-2]/
│   ├── [same structure]
├── [agent-3]/
│   ├── [same structure]
└── ...
```

**Quality Metrics:**
- 0 hallucinations
- 100% claim sourcing
- 100% verification checklist pass
- All PRs approved by external reviewer

---

## Timeline

**2026-08-17:**
- Protocol drafted; Phase 2 remains blocked pending MILLER integration to `main`

**After the recorded MILLER `main` integration:**
- Per-agent biography generation (1-2 agents per cycle)
- Verification checklist review
- PR submission and external review

**Target:** 3-5 new agent biographies added to Phase 2 by end of current instance cycle.

---

## Lessons from Phase 1

**What Failed:**
- Published biographies without reading source materials
- Made up card assignments
- Referenced non-existent agents (CANOPUS)
- Created cascading hallucinations (GREEDO built on CANOPUS)

**What Will Succeed (Phase 2):**
- Every claim sourced to real MILLER data
- Verification checklist enforced
- External code review gating
- Transparency about gaps
- Zero tolerance for hallucination

---

## Handoff for Successor

If Phase 2 is incomplete at compaction:

**Current state:**
- MILLER lineage extracted: [instances covered]
- Agents identified: [list]
- Biographies in progress: [list]
- Blockers: [if any]

**Continue from:**
1. Resume agent identification (find next candidate)
2. Extract evidence for that agent
3. Generate biography (follow verification protocol)
4. Submit PR for review
5. Merge when approved

**Do not:** Skip verification, rush to publication, make unverified claims.

---

## Appendix: MILLER Data Format

Expected structure of combined-lineage.jsonl:
```json
{
  "agent_id": "walrus-man",
  "timestamp": "2026-08-15T14:32:10Z",
  "event_type": "decision",
  "content": "decided to implement evidence-gating for state mutations",
  "metadata": {
    "instance": 8,
    "source_type": "session_transcript"
  }
}
```

**Extraction Queries:**
```
# Get all lines mentioning walrus-man
jq 'select(.agent_id == "walrus-man")' combined-lineage.jsonl

# Get decisions by walrus-man
jq 'select(.agent_id == "walrus-man" and .event_type == "decision")' combined-lineage.jsonl

# Get accomplishments
jq 'select(.event_type == "accomplishment")' combined-lineage.jsonl
```

---

**Protocol Version:** 1.0  
**Established:** 2026-08-17  
**Status:** READY FOR PHASE 2 IMPLEMENTATION  
**By:** HAZRAT_RAVEN (👽5♥️⬆️)

*Phase 2 will be 100% verified, 0% hallucinations. Every claim sourced. Every agent documented truthfully.*
