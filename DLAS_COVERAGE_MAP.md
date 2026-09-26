# Coverage map: where each of the 23 mandatory items is implemented

**Codebase:** [AdribMahmud101/legal-voice-agent](https://github.com/AdribMahmud101/legal-voice-agent) @ `8a288ea` (2026-09-26)
**Method:** I read the source, the D1 migrations, the API routes and the test scripts, and checked which tables each piece of code actually reads or writes. I did not run the app.

## Legend

| Mark | Meaning |
|---|---|
| ✅ | Built and wired: the UI or API reads and writes the database |
| 🟡 | Partial: some of the minimum evidence exists, some is missing |
| 🧪 | Logic or schema only: a rule function or DB table exists, but no screen or API uses it |
| ❌ | Not found in the code |

> **Note:** `DLAS Prototype - OPEN THIS FILE (2).html` is a separate static mock-up that stores its state in localStorage. It is **not connected** to the Next.js app and is not counted below.

---

## Coverage table

| ID | Persona or Item | Where implemented | Coverage |
|---|---|---|---|
| **A1** | Moyuri Akter (Joypurhat) | • `lib/agent/knowledge/severity-classification.ts`: tags restricted contact, proxy report and missing NID<br>• `app/api/portal/cases/route.ts` and `lib/auth/screen-guard.ts`: emergency cases are hidden from everyone except the DLAO and Chief<br>• `lib/case/audit.ts` `decideSend()`: safe-contact rule, used only in tests<br>• `migrations/0016`: `case_reps`, `case_facts`, `safe_profiles` tables (unused) | 🟡 **Partial.** Ripon's report is not kept separate from Moyuri's confirmation. Safe number/time is not enforced. She cannot correct or withdraw information. ⚠️ The PIN SMS is sent regardless of risk. |
| **A2** | Ripon (blind representative) | • `lib/voice-sdk/direct/direct_session.ts`, `lib/audio/dtmf.ts`, `components/softphone-modal.tsx`: voice + keypad IVR<br>• `direct_session.ts` `blindPinPrompt`: PIN spoken aloud to blind callers<br>• `app/api/voice/case-status/route.ts`: status by voice PIN | 🟡 **Partial.** Representation authority is not shown. The record doesn't show what Moyuri has confirmed. The phone is a browser simulation. |
| **A3** | Nabila (Jhenaidah) | • `severity-classification.ts`: fake-image / cyber-harassment → emergency, sensitive<br>• `lib/auth/screen-guard.ts` `canSeeSensitiveCases`: role-restricted access | 🟡 **Partial.** No referral package, acknowledgement, deadline or escalation. Evidence upload is simulated. Opening a sensitive case is not logged. |
| **A4** | Nuching Marma (Khagrachari) | • `lib/agent/semantic-bridge/*`, `migrations/0008`: Marma/Chakma transcript → Bangla, with confidence<br>• `app/api/portal/cases/[id]/semantic-review/route.ts`: DLAO approves or rejects the translation<br>• `app/api/recordings/route.ts`: call recording kept with the case | 🟡 **Partial.** No record of who typed or assisted, and no consent record. UDC access is not bounded. No document-quality flags. **No offline support.** |
| **A5** | Abdul Malek (Barguna) | • `app/api/voice/case-status/route.ts`: status by voice PIN, no reading needed<br>• `lib/case/lawyer-tracker.ts`, `lawyer_action_log`: lawyer deadlines and overdue states<br>• `app/api/portal/lawyer-complaint/route.ts`: complaint → notice to DLAO | 🟡 **Partial.** Voice status gives no next step. Failed contact attempts are not logged. No hearing dates. Lawyers cannot submit updates. |
| **B1** | DLAO officer | • `app/dlao/page.tsx`: queue with SLA and urgency/priority badges<br>• `app/dlao/cases/[id]/page.tsx`: case detail, recording, translation review<br>• `components/dlao-assignment-workbench.tsx`, `lib/case/lawyer-recommendation.ts`: triage + lawyer suggestion with reasons | 🟡 **Partial.** Overrides of a recommendation are not recorded. ⚠️ The queue uses a 65-day SLA, but the domain rule says 15. Search is client-side only. |
| **B2** | Legal Aid Officer / Mediator | • `lib/case/domain.ts` `caseActionState()`: mediation rules<br>• `migrations/0016`: `mediations`, `mediator_requests`, `settlements` tables (unused) | 🧪 **Logic only.** No mediation screen or API: no scheduling, notices, attendance, outcome or remote option. |
| **B3** | 16699 helpline agent | • `app/api/portal/applications/route.ts`: the AI voice intake writes to the same `applications`/`cases` tables<br>• `app/api/voice/case-status/route.ts`: PIN status lookup | 🟡 **Partial.** No console for a human agent. No lookup by Case/Application ID. |
| **B4** | UDC entrepreneur | • `migrations/0015`, `0017`: `udc` and `mobile_agent` roles in the registry only | ❌ **Not built.** No assisted-intake mode, checklist, free-service notice, consent, limited access or weak-network support. |
| **B5** | Panel lawyer | • `app/lawyer/page.tsx`, `app/lawyer/cases/[id]/page.tsx`: assigned cases, case view, close case<br>• `lib/case/lawyer-assignment.ts`: deadline plan created on assignment | 🟡 **Partial.** No accept/decline, no progress updates, no hearing dates. No direct overdue alert to the DLAO. |
| **B6** | Referral receiving DLAO | • `migrations/0016`: `transfers` table (unused)<br>• `lib/case/domain.ts`: transfer rule | 🧪 **Logic only.** No referral package, acknowledgement or return flow, and no status visible to both offices. |
| **B7** | DLAO admin / case-support staff | • D1 database: one structured record per application/case<br>• `case_stage_history` + `components/case-progress-panel.tsx`: stage history<br>• `app/dlao/search/page.tsx`: basic search | 🟡 **Partial.** No reporting view. No field-level version history. The audit log records only lawyer assignments. |
| **T1** | Lawyer change / inactivity | • `app/api/portal/lawyer-complaint/route.ts`: citizen complaint → DLAO notice<br>• `lib/case/lawyer-assignment.ts`, `app/api/portal/lawyer-assign/route.ts`: reassign, restart the deadline clock, audit entry | 🟡 **Partial.** Payment reconciliation exists only as a table + tests. No repeated-inactivity alert with a defined threshold. |
| **T2** | Jurisdiction ping-pong | • `transfers` table, transfer rule in `lib/case/domain.ts` | 🧪 **Logic only.** No detection of repeated returns, no escalation, no human routing decision. |
| **T3** | Multiple applicants / one incident | none | ❌ **Not built.** No incident group, no shared evidence. |
| **T4** | Duplicate detection | none | ❌ **Not built.** No fuzzy matching or side-by-side review. |
| **T5** | Conversational Bangla intake | • `lib/voice-sdk/direct/direct_session.ts`, `lib/agent/core/agent-engine.ts`: multi-turn Bangla voice intake<br>• `severity-classification.ts`: asks the caller to confirm, never auto-files<br>• `lib/case/consultation-service.ts`: sensitive cases → DLAO call-back | 🟡 **Partial.** Extracted facts cannot be confirmed one by one. ⚠️ The scripted call-back decides eligibility and appoints a lawyer by rule, with no human confirming. |
| **T6** | Document briefing / checklist | • `lib/identity/ocr.ts`: NID/passport OCR only (simulated) | ❌ **Not built.** No summaries, checklist, or missing/unclear flags. |
| **T7** | Settlement drafting | • `lib/case/domain.ts` `certificationState()` only | ❌ **Not built.** No templates, drafts or inconsistency warnings. |
| **T8** | Multi-agent triage | • `severity-classification.ts` (category)<br>• `lib/case/legal-aid-eligibility.ts` (eligibility)<br>• `caseActionState()` (process checks, tests only)<br>• `lib/case/lawyer-recommendation.ts` (lawyer match) | 🟡 **Partial.** No orchestrator. Disagreements between components are not surfaced. |
| **T9** | Offline-first sync | none | ❌ **Not built.** No local queue, temporary IDs, sync, conflict review or integrity check. |
| **T10** | Low-bandwidth PWA | none | ❌ **Not built.** No manifest or service worker; no light mode. |
| **T11** | Asynchronous e-signature | • `components/identity/signature-pad.tsx`, `citizen_signatures`: signature image + SHA-256 hash, for ID checks only<br>• `settlements` table (unused) | ❌ **Not built.** Not linked to mediation. No cryptographic signature, offline signing or verification. |

---

## Summary

| Part | ✅ | 🟡 | 🧪 | ❌ |
|---|---|---|---|---|
| A: 5 citizen scenarios | 0 | 5 | 0 | 0 |
| B: 7 provider scenarios | 0 | 4 | 2 | 1 |
| C: 11 technical challenges | 0 | 3 | 1 | 7 |
| **Total (23)** | **0** | **12** | **3** | **8** |

## ⚠️ Risks against the responsible-design rules

1. **Safe contact (G3):** the PIN SMS is sent without calling `decideSend()`, even for emergency cases.
2. **Human authority (G5):** the call-back flow decides eligibility and appoints a lawyer with no human confirming.
3. **Audit (G10):** `audit_log` is written only for lawyer assignments. Opening a sensitive case is not logged.
4. **Consistency:** the DLAO queue's 65-day SLA does not match the 15-day review SLA in `lib/case/domain.ts`.

## Quick wins (already exist, only need wiring)

- `decideSend()` + `safe_profiles` → check before every SMS/voice send (A1, G3)
- `case_reps` + `case_facts` → show representative authority and confirmed facts (A1, A2, G2)
- `assist_decisions` → record DLAO overrides (B1, G5)
- `transfers` + transfer rule → referral screen (A3, B6, T2)
- `mediations` / `settlements` + `certificationState()` → mediation flow (B2, T7, T11)
- `lawyer_action_log` → let the lawyer mark steps as done (A5, B5, T1)
