# Coverage map: 23 mandatory items vs. the DLAS prototype code

**Codebase:** AdribMahmud101/legal-voice-agent @ `352df07` (2026-09-26)

**Coverage labels** — exactly one per row:
- ☑ **Implemented** — works end-to-end, a juror can trigger and see it right now
- ☑ **Integrated** — real logic exists and is connected to the shared record, but a required piece is missing
- ☑ **Testable** — a rule or table exists and is provably correct, but no screen or route in the app can reach it
- ☑ **Not built** — no code found for this item (not one of the three you gave me — flagging separately rather than mislabeling something that plainly doesn't exist)

---

| ID | Persona or Item | Where Implemented | Coverage |
|---|---|---|---|
| **A1** | Moyuri — safe contact, identity gap, representation | • Flags a call that mentions restricted contact, a missing ID, or someone reporting on another person's behalf, and marks it high priority<br>• Hides high-risk cases from every role except the district officer and the chief officer<br>• A rule for which contact channel is safe exists, but nothing in the running app checks it before a message goes out<br>• The confirmation text with the login PIN is sent to whatever number was given, with no safety check first | ☑ **Integrated** |
| **A2** | Ripon — blind access, independent status/tasks | • Lets a caller complete the whole intake by voice and keypad<br>• Speaks the login PIN aloud instead of showing it on screen<br>• No visual step (form, CAPTCHA, image OTP) exists anywhere in this path<br>• Nothing records who is speaking on someone else's behalf, or what they're allowed to do | ☑ **Integrated** |
| **A3** | Nabila — urgency, sensitive access, tracked referral | • Recognises fake/altered-image and harassment language and marks the case an emergency<br>• Hides the case from everyone except the district officer and chief officer<br>• A "this access will be logged" confirmation exists in the code but is never shown to anyone<br>• There is no way to send the case to another office, get an acknowledgement, or track a deadline | ☑ **Integrated** |
| **A4** | Nuching — assisted access, language/provenance, offline | • Converts Marma/Chakma speech into normalised Bangla with a confidence score, keeping the original words alongside it<br>• Lets a district officer approve or correct that translation<br>• Keeps the original call recording linked to the case<br>• Nothing detects a lost connection, queues work locally, or resumes after it drops | ☑ **Integrated** |
| **A5** | Malek — long-running status, unstable contact, lawyer follow-up | • Lets someone check case status by voice PIN, but the answer is only "filed" or "not yet filed" — no next step<br>• Computes a deadline and an overdue/due-soon state for each step a lawyer is expected to take<br>• Nothing ever marks a step done, so an overdue item stays overdue forever<br>• A complaint about the lawyer sends a notice to the office, but no failed-contact attempt is ever logged | ☑ **Integrated** |
| **B1** | DLAO officer — operational view, backlog, priority | • Shows a queue of new applications, active cases, and cases with a lawyer, each with an age/deadline badge<br>• Suggests a lawyer for a case with a plain-language reason for the ranking<br>• Highlights how many open cases have no lawyer at all<br>• Search only filters cases already loaded in the browser, not a real query<br>• Nothing records when an officer accepts or overrides the suggested lawyer | ☑ **Integrated** |
| **B2** | Mediator — end-to-end mediation + remote/hybrid | • A rule exists for when mediation is or isn't allowed on a case<br>• No screen or feature actually schedules, records, or tracks a mediation session | ☑ **Testable** |
| **B3** | 16699 helpline agent — shared lookup + Bangla intake/status | • The automated voice agent's intake and the human-facing case screens read and write the same record<br>• Status can be checked by voice PIN<br>• There is no screen for a human call-centre agent to look up an existing case by ID | ☑ **Integrated** |
| **B4** | UDC entrepreneur — assisted intake + checklist + safe contact | • The role exists in a list of staff roles, with no screen or feature behind it | ☑ **Not built** |
| **B5** | Panel lawyer — assignment/worklist + hearings/deadlines | • Shows a lawyer their assigned cases and lets them close a case<br>• Assigning or reassigning a lawyer restarts the deadline clock and records the change<br>• Shows the district officer how many overdue steps each lawyer has<br>• A lawyer cannot accept or decline an assignment, and cannot mark a step done | ☑ **Integrated** |
| **B6** | Receiving DLAO — complete referral + acknowledgement/status | • A rule exists for when a transfer between offices is or isn't allowed<br>• No screen sends, receives, acknowledges, or tracks an actual transfer | ☑ **Testable** |
| **B7** | Admin/case-support — structured record + search + reporting | • Every case is one structured record with a visible history of stage changes<br>• No reporting or statistics view exists<br>• Search only filters cases already loaded, not a real query | ☑ **Integrated** |
| **T1** | Lawyer change/inactivity + payment reconciliation | • A citizen can file a complaint about their lawyer, which notifies the office<br>• Reassigning a lawyer restarts the deadline clock and is recorded<br>• A payment-clawback rule exists, but nothing in the app ever triggers it<br>• No automatic alert exists for a lawyer with a repeated pattern of missed deadlines | ☑ **Integrated** |
| **T2** | Jurisdiction ping-pong + escalation | • A rule exists for when a transfer is currently allowed<br>• Nothing counts repeated transfers, escalates them, or lets a human make the final routing call | ☑ **Testable** |
| **T3** | Related incident cases + shared evidence | • Only a one-to-one link from an application to the case it became exists<br>• Nothing links several separate cases from one incident, and nothing shares one piece of evidence across them | ☑ **Not built** |
| **T4** | Duplicate/fraud-risk detection | • Nothing compares applicant records against each other for similarity<br>• The only fuzzy-text matching in the code compares spoken words to a dictionary, not people to people | ☑ **Not built** |
| **T5** | Conversational Bangla intake agent | • Runs a multi-turn Bangla voice conversation that fills in an application step by step<br>• Asks the caller to confirm before anything is filed, rather than filing automatically<br>• Keeps the original spoken words alongside anything interpreted from them<br>• Sensitive or complex cases go to a scripted "officer call-back" — but that call-back decides eligibility and appoints a lawyer by rule, not through an actual human reviewing the case | ☑ **Integrated** |
| **T6** | Document summary/checklist agent | • Only reads one ID photo to pull out a name, date of birth and address, explicitly simulated<br>• Nothing summarises multiple documents, checks them against a case-type checklist, or flags missing/unclear items | ☑ **Not built** |
| **T7** | Settlement drafting assistant | • A rule blocks certifying a settlement until all parties have signed<br>• Nothing drafts a settlement, fills a template, or flags an inconsistency | ☑ **Testable** |
| **T8** | Multi-agent triage pipeline | • Separate pieces of logic each judge one thing — severity, eligibility, whether an action is currently allowed, and which lawyer fits best — and each shows its own reasons<br>• Nothing runs them together as a pipeline, and nothing surfaces it when two of them disagree | ☑ **Integrated** |
| **T9** | Offline-first sync + conflict/integrity handling | • Nothing checks for a lost connection, queues work locally, or re-syncs after reconnecting | ☑ **Not built** |
| **T10** | Low-bandwidth PWA | • No installable app, no offline caching, and no lighter mode for slow connections exist | ☑ **Not built** |
| **T11** | Asynchronous secure e-signature | • Captures a drawn signature image, hashes it, and records consent — but only as part of identity verification, not a legal document<br>• A three-party sign-off gate exists for settlements, but nothing in the app ever writes to it<br>• No cryptographic document-signing, offline signing, or independent verification exists | ☑ **Integrated** |

---

## Summary

| Coverage | Count |
|---|---|
| Implemented | 0 |
| Integrated | 13 |
| Testable | 4 |
| Not built | 6 |
| **Total** | **23** |
