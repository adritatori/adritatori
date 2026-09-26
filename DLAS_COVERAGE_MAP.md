# Coverage map: where each of the 23 mandatory items is implemented

**Codebase:** [AdribMahmud101/legal-voice-agent](https://github.com/AdribMahmud101/legal-voice-agent) @ `8a288ea` (2026-09-26)
**Method:** I read the source, the D1 migrations, the API routes and the test scripts, and checked which tables each piece of code actually reads or writes. I did not run the app.

## Legend

| Mark | Meaning |
|---|---|
| ✅ | Built and wired: the UI or API reads and writes the database |
| 🟡 | Partial: some of the minimum evidence exists, some is missing |
| 🧪 | Logic or schema only: a pure rule function or a DB table exists, but no screen or API uses it (tests only) |
| ❌ | Not found in the code |

> **Note:** `DLAS Prototype - OPEN THIS FILE (2).html` is a separate static mock-up that stores its state in localStorage. It contains demos of transfers, safe contact and fact provenance, but it is **not connected** to the Next.js app. It is not counted below.

---

## Summary

| Part | ✅ | 🟡 | 🧪 | ❌ |
|---|---|---|---|---|
| A: 5 citizen scenarios | 0 | 5 | 0 | 0 |
| B: 7 provider scenarios | 0 | 4 | 2 | 1 |
| C: 11 technical challenges | 0 | 3 | 1 | 7 |
| **Total (23)** | **0** | **12** | **3** | **8** |

**Strongest areas:** Bangla voice intake (16699 simulation), severity screening, sensitive-case role restriction, panel-lawyer assignment with deadlines.
**Biggest gaps:** offline/PWA, e-signature, mediation, referral/transfer, duplicate detection, case linking, document agent, settlement drafting.

---

## Part A: Five citizen scenarios

| # | Status | Implemented where | Missing vs. minimum evidence |
|---|---|---|---|
| **A1 Moyuri** | 🟡 | • Severity rules tag `Restricted_Contact`, `Proxy_Applicant`, `Incomplete_NID` → `lib/agent/knowledge/severity-classification.ts`<br>• Emergency cases are marked sensitive and hidden from everyone except the DLAO and Chief → `app/api/portal/cases/route.ts`, `lib/auth/screen-guard.ts`<br>• 🧪 Safe-contact decision `decideSend()` (fails closed) → `lib/case/audit.ts`, tests only<br>• 🧪 Tables `case_reps`, `case_facts`, `safe_profiles`, `safe_contact_destinations` → `migrations/0016_case_operations.sql`, schema only | • Ripon's report is not stored separately from Moyuri's confirmation<br>• No safe channel/number/time or neutral wording is enforced at runtime<br>• No way for Moyuri to correct or withdraw information<br>• ⚠️ The PIN SMS goes to whatever number was given, **regardless of severity** (`lib/voice-sdk/direct/direct_session.ts` ~L3002)<br>• Failure test (unsafe person answers): not handled |
| **A2 Ripon** | 🟡 | • Voice-only IVR with keypad (DTMF) → `lib/voice-sdk/direct/direct_session.ts`, `lib/audio/dtmf.ts`, `components/softphone-modal.tsx`<br>• Blind callers hear their PIN spoken aloud → `direct_session.ts` (`blindPinPrompt`)<br>• Case tracking by voice PIN (root menu option 3) → `app/api/voice/case-status/route.ts`<br>• Portal a11y check → `scripts/verify_portal_a11y.mjs` | • Scope and status of representation authority are not shown (`case_reps` is schema only)<br>• The record does not show what Moyuri has and has not confirmed<br>• The "phone" is a browser softphone simulation, not a real phone line |
| **A3 Nabila** | 🟡 | • Fake-image and cyber-harassment keywords → emergency / sensitive → `severity-classification.ts`<br>• Role-restricted visibility → `lib/auth/screen-guard.ts` (`canSeeSensitiveCases`)<br>• Demo call preset → `components/softphone-modal.tsx` | • No referral package, acknowledgement, deadline or escalation (`transfers` table exists but no code uses it)<br>• Evidence upload is simulated (`app/citizen/documents/page.tsx` shows an alert)<br>• The `sensitive_case_opened` audit kind is defined, but nothing writes it<br>• Failure test (no acknowledgement): not handled |
| **A4 Nuching** | 🟡 | • Marma/Chakma "semantic bridge": stores the raw transcript, normalised Bangla, intent and confidence → `lib/agent/semantic-bridge/*`, `migrations/0008_*`<br>• DLAO approves or rejects the interpretation → `app/api/portal/cases/[id]/semantic-review/route.ts`, `app/dlao/cases/[id]/page.tsx`<br>• Call recording kept with the case → `app/api/recordings/route.ts` | • No record of who typed or assisted, and no UDC consent<br>• No bounded UDC access<br>• No document-quality flags<br>• **No offline support** (no service worker or local queue)<br>• Failure test (network drops): not handled; the voice flow only shows a "try again" message |
| **A5 Abdul Malek** | 🟡 | • Status by voice PIN, with no smartphone or reading needed → `app/api/voice/case-status/route.ts`<br>• Lawyer action plan with deadlines and overdue states → `lib/case/lawyer-tracker.ts`, `lawyer_action_log` (0023)<br>• Citizen complaint about the lawyer → queues a notice to the DLAO → `app/api/portal/lawyer-complaint/route.ts`<br>• Overdue count per lawyer on the roster → `app/api/portal/lawyers/route.ts` | • Voice status only says "filed" or "application"; it gives **no next step**<br>• Failed contact attempts are not logged<br>• No hearing dates, so no alert "before he travels"<br>• Lawyers have no way to submit updates, so overdue items can never be cleared |

---

## Part B: Seven provider scenarios

| # | Status | Implemented where | Missing vs. minimum evidence |
|---|---|---|---|
| **B1 DLAO officer** | 🟡 | • Queue with SLA badges and urgency/priority → `app/dlao/page.tsx`, `components/risk-badges.tsx`<br>• Case detail with recording and semantic review → `app/dlao/cases/[id]/page.tsx`<br>• "AI Suggestion Center": triage plus lawyer recommendation, with weighted reasons → `components/dlao-assignment-workbench.tsx`, `lib/case/lawyer-recommendation.ts` | • Overrides of a recommendation are **not recorded** (`assist_decisions` is schema only)<br>• ⚠️ The queue uses a 65-day SLA (`SLA_DAYS = 65`), but the domain rule says review = 15 days (`lib/case/domain.ts`)<br>• Search filters client-side only (`app/dlao/search/page.tsx`) |
| **B2 Mediator** | 🧪 | • Mediation rules in `caseActionState()` → `lib/case/domain.ts`<br>• Tables `mediations`, `mediator_requests`, `settlements` → migration 0016 | • No mediation screen or API<br>• No scheduling, notices, attendance, outcome, or remote/in-person option |
| **B3 16699 agent** | 🟡 | • The AI voice agent's intake writes to the **same** `applications`/`cases` tables (`source='voice'`) → `app/api/portal/applications/route.ts`<br>• PIN-based status lookup → `app/api/voice/case-status/route.ts` | • No console for a human helpline agent<br>• No lookup by Case/Application ID<br>• The `callcentre` role exists only in the role registry (migration 0015) |
| **B4 UDC entrepreneur** | ❌ | • The `udc` and `mobile_agent` roles exist in the registry only (migrations 0015, 0017) | • No assisted-intake mode, checklist, free-service notice, provenance/consent, limited post-submission access or weak-network support<br>• The apply wizard (`components/apply-wizard.tsx`) is self-service only |
| **B5 Panel lawyer** | 🟡 | • Lawyer portal: assigned cases, case view, close case → `app/lawyer/page.tsx`, `app/lawyer/cases/[id]/page.tsx`<br>• Deadline plan seeded on assignment → `lib/case/lawyer-assignment.ts` | • No accept/decline<br>• No progress-update submission<br>• No hearing dates<br>• No direct overdue alert to the DLAO; only a count on the roster |
| **B6 Referral receiving DLAO** | 🧪 | • `transfers` table (migration 0016)<br>• Transfer rule in `caseActionState()` | • No referral package, acknowledgement or return flow, and no status visible to both offices |
| **B7 Case-support staff** | 🟡 | • One structured D1 record for applications and cases<br>• Stage history → `case_stage_history`, shown in `components/case-progress-panel.tsx`<br>• `audit_log` (written only for lawyer assign/reassign) | • No reporting or statistics view<br>• No field-level version history<br>• Search is basic |

---

## Part C: Eleven technical challenges

| # | Status | Implemented where | Missing vs. acceptance test |
|---|---|---|---|
| **T1 Lawyer inactivity** | 🟡 | • Citizen complaint → DLAO notice → `lawyer-complaint/route.ts`<br>• Manual reassign closes the old assignment, restarts the deadline clock, and writes an audit entry → `lib/case/lawyer-assignment.ts`, `app/api/portal/lawyer-assign/route.ts`<br>• Overdue steps lower a lawyer's recommendation ranking | • Payment reconciliation: 🧪 `tranches` table plus tests only<br>• No separate repeated-inactivity alert with a defined threshold |
| **T2 Jurisdiction ping-pong** | 🧪 | • `transfers` table, transfer rule | • No detection of repeated returns, no escalation, no human routing decision |
| **T3 Multi-applicant incident** | ❌ | none (`application_case_links` only links an application to its case) | • No incident group and no shared evidence |
| **T4 Duplicate detection** | ❌ | none (Levenshtein is used only in the semantic bridge) | • No fuzzy matching, side-by-side review or trap cases |
| **T5 Bangla intake agent** | 🟡 | • Multi-turn Bangla voice intake with slot steps → `lib/voice-sdk/direct/direct_session.ts`, `lib/agent/core/agent-engine.ts`<br>• Severity screening asks the caller to confirm and never auto-files → `severity-classification.ts`<br>• Provenance: original transcript and recording stored<br>• Sensitive cases → restricted and routed to a DLAO callback → `lib/case/consultation-service.ts` | • ⚠️ The scripted DLAO consultation **decides eligibility and appoints a lawyer by rule** (`lib/case/legal-aid-eligibility.ts`, `consultation-script.ts`). This may conflict with "AI does not decide eligibility" and "final lawyer assignment remains with authorised humans"<br>• Extracted facts are not individually confirmable (`case_facts` is schema only) |
| **T6 Document agent** | ❌ | only NID/passport OCR for identity (`lib/identity/ocr.ts`, marked simulated) | • No summarisation, case-type checklist, or missing/unclear flags |
| **T7 Settlement drafting** | ❌ | only `certificationState()` in `lib/case/domain.ts` | • No templates, drafts or inconsistency warnings |
| **T8 Multi-agent triage** | 🟡 | Separate components:<br>• categorisation → `severity-classification.ts`<br>• eligibility → `legal-aid-eligibility.ts`<br>• process checks → `caseActionState()` (🧪)<br>• lawyer match → `lawyer-recommendation.ts` | • No orchestrator<br>• Disagreements between components are not surfaced<br>• No 5-case test run |
| **T9 Offline sync** | ❌ | none | • No temporary UUIDs, local queue, idempotent sync, conflict review or integrity check |
| **T10 Low-bandwidth PWA** | ❌ | none (no manifest, no service worker) | • Not installable; no light mode; no throttled-network measures |
| **T11 E-signature** | ❌ | building blocks only: a signature-pad image with SHA-256 hash and consent, for identity verification (`components/identity/signature-pad.tsx`, `citizen_signatures`); the 🧪 `settlements` table | • Not linked to mediation<br>• No cryptographic signature, offline signing or independent verification |

---

## ⚠️ Risks against the responsible-design rules

1. **Safe contact (G3):** the PIN SMS is sent without calling `decideSend()`, even for Category A (emergency) cases.
2. **Human authority (G5):** the consultation flow decides eligibility and appoints a lawyer by rule. It is presented as a DLAO call, but no human confirms it.
3. **Audit (G10):** `audit_log` is written in one place only (lawyer assign/reassign). Opening a sensitive case is not logged.
4. **Consistency:** the DLAO queue's 65-day SLA does not match the 15-day review SLA in `lib/case/domain.ts`.

## Quick wins (things that already exist and only need wiring)

- `decideSend()` + `safe_profiles` → put them in front of every SMS/voice send (A1, G3)
- `case_reps` + `case_facts` → show representative authority and confirmed/unconfirmed facts (A1, A2, G2)
- `assist_decisions` → record DLAO overrides (B1, G5)
- `transfers` + `caseActionState().transfer` → referral screen (A3, B6, T2)
- `mediations` / `settlements` + `certificationState()` → mediation flow (B2, T7, T11)
- `lawyer_action_log` → let the lawyer mark steps as done (A5, B5, T1)
