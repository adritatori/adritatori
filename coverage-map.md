# ADLASB Hackathon — Coverage Map (23 Mandatory Items)

*No ADLASB_Hackathon_Final_Round_Case.pdf found in the project; built from the supplied reference data (T2–T11 filled from the original Part C spec).*

| ID | Persona or Item | Where Implemented | Coverage |
|---|---|---|---|
| A1 | Moyuri Akter | Flow 1 (Safe & Accessible Intake) — severity classifier + role screen-guard — permissions, provenance field | Safe contact, identity gap, representation |
| A2 | Ripon (representative) | Flow 1 — voice/keypad IVR, spoken PIN login — Application ID, representation field | Blind access, independent status/task |
| A3 | Nabila | Flow 5 (Urgent Referral) — severity classifier + sensitive-case guard — permissions, audit trail | Urgency, sensitive access, tracked referral |
| A4 | Nuching Marma | Flow 2 (Assisted/Offline Intake) — Marma/Chakma semantic bridge — provenance field, Case ID | Assisted access, language/provenance, offline resilience |
| A5 | Abdul Malek | Flow 6 (Long-Running Case) — voice status check + lawyer tracker — Case ID, deadline field | Long-running status, unstable contact, lawyer follow-up |
| B1 | DLAO Officer | Flow 3 (Daily Operations) — case queue + lawyer recommender — Case ID, priority field | Operational view, backlog, priority, follow-up |
| B2 | Legal Aid Officer/Mediator | Flow 4 (Mediation & Settlement) — mediation eligibility rule only, schema-only — Case ID | End-to-end mediation, remote/hybrid option |
| B3 | 16699 Helpline Agent | Flow 1 — voice intake API, shared record — Application ID, Case ID | Shared case look-up, Bangla intake/status |
| B4 | UDC Entrepreneur | Flow 2 — role listed in registry only, no flow built — permissions | Assisted intake, checklist, safe contact, free-service notice |
| B5 | Panel Lawyer | Flow 6 — lawyer assignment service — Case ID, deadline field, audit trail | Assignment/worklist, hearings/deadlines, updates |
| B6 | Receiving DLAO | Flow 5 — transfer rule only, schema-only — Case ID, status field | Complete referral, acknowledgement/status |
| B7 | Admin/Case-Support Staff | Flow 3 — case record + stage-history view — document history, Case ID | Structured record, search, reporting continuity |
| T1 | Lawyer Change/Pattern Alert | Flow 6 — complaint route + reassignment service — audit trail, Case ID | Lawyer change, repeated inactivity, payment reconciliation |
| T2 | Jurisdiction Ping-Pong/Escalation | Flow 5 — transfer rule only, schema-only — Case ID, status field | Repeated-transfer detection, escalation, human routing decision |
| T3 | Related Incident Cases | Flow 3 — application-to-case link only (1:1, not multi-case) — Case ID | Linked incident cases, shared evidence, no merging |
| T4 | Duplicate/Fraud Detection | None found — no applicant-matching service exists — n/a | Fuzzy duplicate/fraud matching, human review, no auto-reject |
| T5 | Conversational Bangla Intake Agent | Flow 1 — Bangla voice intake engine — provenance field, Application ID | Bangla multi-turn intake, provenance, human handoff |
| T6 | Document Summary/Checklist Agent | Flow 2 — ID-photo OCR only, simulated — document history | Document briefing, checklist, missing/unclear-item flags |
| T7 | Settlement Drafting Assistant | Flow 4 — certification gate rule only, schema-only — Case ID | Template-grounded draft, AI-marked sections, human review |
| T8 | Multi-Agent Triage Pipeline | Flow 3 — severity, eligibility, recommender run separately, not orchestrated — Case ID | Multi-component triage, structured reasons, conflict surfaced |
| T9 | Offline-First Sync | None found — no local queue or sync logic exists — n/a | Offline creation, idempotent sync, conflict/integrity verification |
| T10 | Low-Bandwidth PWA | None found — no manifest or service worker exists — n/a | Installable low-bandwidth app, light mode, throttled testing |
| T11 | Asynchronous E-Signature | Flow 4 — signature capture for identity only, not mediation — document history | Async offline signing, cryptographic verification, independent check |

Second table: single trigger + observed result per item, honest "not built" marked where no demoable path exists.

| ID | Trigger Action | Expected Observable Result |
|---|---|---|
| A1 | Call as Moyuri, mention restricted contact | Case flagged sensitive, hidden from non-DLAO roles |
| A2 | Complete voice intake as a blind caller | PIN spoken aloud, no visual step required |
| A3 | Call as Nabila, mention fake images | Case marked emergency, restricted to DLAO/Chief |
| A4 | Speak Marma phrase into intake | Normalised Bangla + confidence shown for DLAO review |
| A5 | Check status via voice PIN as Malek | Status returned as filed/not-filed only |
| B1 | Open DLAO queue | Cases shown with age badge and lawyer suggestion |
| B2 | Attempt to schedule a mediation | No mediation screen exists — not built |
| B3 | File a voice application, then look it up | Application appears in the same case record |
| B4 | Attempt UDC-assisted intake | No UDC screen exists — not built |
| B5 | Assign a lawyer to a case | Deadline plan seeded, prior assignment closed |
| B6 | Attempt to send/accept a transfer | No transfer screen exists — not built |
| B7 | Search DLAO case list | Client-side filter over already-loaded cases only |
| T1 | File a lawyer complaint, then reassign | Notice sent, deadline clock restarted, audit entry written |
| T2 | Attempt a second transfer on one case | Rule blocks it — no escalation screen exists |
| T3 | Submit two applications for one incident | No incident-group link created — not built |
| T4 | Submit two similar applicant names | No duplicate flag raised — not built |
| T5 | Speak a full Bangla intake, no keypad | Assistant confirms before filing, keeps transcript |
| T6 | Upload multiple case documents | No checklist or summary generated — not built |
| T7 | Request a settlement draft | No draft generated — not built |
| T8 | Open a case with conflicting signals | Each rule shown separately, no combined conflict flag |
| T9 | Disconnect network mid-intake | No local queue — submission is lost |
| T10 | Load the app on a throttled connection | No lighter mode or install prompt appears |
| T11 | Have two parties sign a settlement | No settlement-signing flow exists — not built |
