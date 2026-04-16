# Requirements Conversation Cheat Sheet
_Grounded in the Campus Lab Booking brief (Dr. Miriam Kasper) · S3 Assignment_

---

## 1. Stakeholder Map — Build This First

| Who | Actor? | Interest | Hidden? |
|-----|--------|----------|---------|
| Students | ✅ Primary | Book / cancel / view slots | No |
| Lab Assistants | ✅ Primary | See prep list, cleaning log | Partially — brief almost treats them as passive |
| Dr. Kasper (Domain Expert) | ❌ Non-actor | Defines requirements, not a user | No |
| University IT Dept. | ❌ Non-actor | Infrastructure & hosting constraints | **Yes — never explicitly named as stakeholder** |
| Exchange Students | ✅ Primary | Same as students, but need English UI | **Yes — implied by "a third" stat** |
| GDPR / Data Protection Officer | ❌ Non-actor | Compliance sign-off | **Yes — legally required, not mentioned** |
| Future admins / module coordinators | ❌/✅ unclear | No-show tracking, scheduling | **Yes — who actually acts on no-show data?** |

**Pattern to remember:** The person writing the brief is rarely the person using the system. Always ask: _"Who touches this daily?"_ and _"Who has to sign this off?"_

---

## 2. Three-Bucket Classification

### Functional Requirements (FR) — what the system *does*
- FR1 · Students can log in with existing campus SSO account
- FR2 · Students can book a bench (lab + bench + time slot), view upcoming slots, cancel
- FR3 · System prevents double-bookings (hard constraint, not just a warning)
- FR4 · Lab assistants see a daily prep view: which experiments run, what to stage
- FR5 · Each bench has a "last cleaned at" timestamp; system blocks booking if bench uncleaned since last use
- FR6 · Contaminated / out-of-service benches are unbookable (hard block)
- FR7 · Cancelled slots become immediately available to other students
- FR8 · Failed bookings surface an explicit error — no silent failures
- FR9 · UI in German and English (switchable)
- FR10 _(nice-to-have)_ Email reminder 24 h before slot
- FR11 _(nice-to-have)_ Printable PDF "my bookings" page
- FR12 _(nice-to-have)_ No-show tracking with reminder mechanism

### Non-Functional Requirements (NFR) — how well it does it
- NFR1 · **"Fast / snappy"** ⚠️ — _no metric given. Flag: define p95 response time (e.g. < 1 s for booking confirmation)_
- NFR2 · **"Always available during teaching weeks"** ⚠️ — _no SLA number. Flag: 99.9%? Define planned maintenance windows_
- NFR3 · **"Easy to use without training"** ⚠️ — _unmeasurable as stated. Flag: task completion rate benchmark? First-session success?_
- NFR4 · Mobile-friendly (responsive design; primary device is phone)
- NFR5 · Offline capability for already-authenticated in-lab users (read-only confirmation at minimum)
- NFR6 · Power-user view for lab assistants (keyboard shortcuts)
- NFR7 · GDPR-compliant; student data stays within EU

### Constraints (C) — things you cannot change
- C1 · Hosted on university Kubernetes cluster only — no external SaaS
- C2 · Open-source stack only (budget = ~zero for licenses)
- C3 · Data residency: EU only
- C4 · Pilot deadline: week 3 of summer term (hard date)
- C5 · Lab assistants must not need a separate tool / workflow

---

## 3. Four-Colour Markup on the Brief

### 🟡 Ambiguities — unclear, needs a number or definition

| Ambiguity | One follow-up question |
|-----------|----------------------|
| "Fast / snappy" (no metric) | _"What's the slowest acceptable response for a booking confirmation — 1 second? 3 seconds?"_ |
| "Always available during teaching weeks" (no SLA) | _"What uptime percentage is acceptable, and does 'teaching weeks' exclude evenings and weekends?"_ |
| "Easy to use without training" (unmeasurable) | _"Could you describe a scenario where a first-year student failed to complete a booking — what went wrong?"_ |
| "Some kind of no-show tracking" (vague mechanism) | _"Who receives the reminder — the student only, or also a supervisor — and how many no-shows trigger it?"_ |
| "Offline" scope unclear | _"Does offline mean full booking capability, or just checking in to confirm an existing booking?"_ |

### 🔴 Hidden Assumptions — stated as fact, but never confirmed

| Assumption | One follow-up question |
|------------|----------------------|
| Campus SSO exists and has an API | _"Does IT expose an OAuth / SAML endpoint we can integrate with, or does SSO need to be built?"_ |
| Experiment ↔ chemicals mapping already exists somewhere | _"Where does the 'Organic Chemistry Practical 3 → chemicals list' data live today — a spreadsheet, a LIMS, or someone's head?"_ |
| Lab assistants are comfortable with a web UI | _"Do assistants currently use any web tools, or is their workflow entirely paper/clipboard?"_ |
| "Colour-coded benches" colours are documented | _"Are the bench colours formally defined (RAL codes, hex values) or just physical paint?"_ |
| Pilot in week 3 includes real student data | _"Is the pilot with live students or a controlled test group — and who approves that?"_ |

### ~~Contradictions~~ — statements that conflict

| Contradiction | One follow-up question |
|--------------|----------------------|
| ~~"Works offline" vs. "booking must never silently lose data"~~ — offline write + sync later risks silent loss if sync fails | _"If a student confirms attendance offline and the sync later fails, should the system retry silently or alert someone?"_ |
| ~~"Lab assistants must not use a separate tool" vs. "power user view with keyboard shortcuts"~~ — a dedicated power view is effectively a separate mode | _"Is a different screen within the same app acceptable, or does 'no separate tool' mean no new login / URL at all?"_ |
| ~~"Tiny budget / open source only" vs. hard pilot deadline~~ — timeline pressure often leads to paid shortcuts | _"If an open-source component turns out to require a commercial support contract to meet the deadline, is there any budget flexibility?"_ |

---

## 4. Trouble-Hunting Checklist (run on any brief)

Before the meeting, ask yourself:

- [ ] Who is **not** in the room but has veto power? (IT, Legal, DPO, Finance)
- [ ] Which adjectives have **no number** behind them? (fast, available, easy, secure)
- [ ] What data does the system need that the brief **assumes already exists**?
- [ ] Are there **two requirements that cannot both be true** at the same time?
- [ ] What happens at the **edges**: network down, bench dirty, student cancels 5 min before?
- [ ] Who handles the system **after go-live**? (Ops, maintenance, data exports)
- [ ] Does "the system" include **the data that feeds it**, or only the UI?
- [ ] Is the deadline **fixed by external event** (semester start, audit) or negotiable?

---

## 5. General Patterns Worth Keeping

### Requirement smells
| Smell | What to do |
|-------|-----------|
| Adjective without metric ("fast", "secure", "reliable") | Ask for a number or a failure scenario |
| "The system should…" with no subject | Ask who triggers it and who sees the result |
| Nice-to-have listed alongside must-have | Force a priority stack: _"If you had to cut one, which goes first?"_ |
| Constraint that blocks a common solution | Ask if the constraint is policy or preference — policies can sometimes be waived |
| "We don't want X" without saying why | Ask the reason; the real constraint may be narrower than stated |

### Questions worth asking in any meeting
1. _"Who is the worst-case user — least technical, most time-pressured?"_
2. _"What does success look like on launch day — how will you know it worked?"_
3. _"What workaround do people use today, and what do they secretly like about it?"_
4. _"What's the one thing that, if we got it wrong, would kill adoption?"_
5. _"Who approves go-live — and are they in this room?"_
6. _"What happens to existing data when we switch over?"_
7. _"Are there regulatory or legal reviewers who need to sign off?"_

### Actor vs. non-actor trap
A stakeholder who **dictates constraints** (IT dept., Legal) but **never logs in** is still a stakeholder — just not an actor in the use-case sense. Confusing the two leads to missing sign-offs late in the project.

### The "integration assumption" trap
Whenever a requirement says "integrates with X", treat it as a mini-project. X may not have an API, may require its own procurement, or may be owned by a different team. Always ask: _"Has anyone spoken to the team that owns X?"_

### Offline ≠ simple
"Works offline" is one of the most expensive requirements to get right (sync conflicts, eventual consistency, data loss edge cases). Nail down the exact offline scope early — read-only vs. write, how long, what syncs on reconnect.

---

## 6. Quick-Reference Before Any Stakeholder Meeting

```
BEFORE:  Who are the hidden stakeholders? What constraints exist outside this brief?
OPEN:    "Walk me through a typical day — what breaks most often?"
LISTEN:  Note every adjective without a number. Note every integration mentioned casually.
PROBE:   "What does that mean in practice?" / "What's the worst case?"
CLOSE:   "What would make this a failure?" / "Who else needs to approve this?"
AFTER:   Send a summary email with open questions numbered — makes follow-up trackable.
```

---

_Last updated: 16 April 2026 · S3 Campus Lab Booking analysis_
