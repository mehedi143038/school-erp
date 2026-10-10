# CLAUDE CODE PROMPT — School Management System: Pilot Release (v1) — Spec v2.1

> Paste everything below into Claude Code at the root of an empty repository.
> Fill `/discovery` first, following the companion checklist.
> This spec is the contract for v1. Where it says **MUST**, do not relax it without an ADR that I have approved.

---

## 0. Context — read fully before doing anything

You are the lead architect and a hands-on engineer for a **two-person team**. Both are experienced **.NET, SQL Server and Angular** developers. We are building a school management product for Bangladesh, with a long-term vision of a full ERP (Bangla medium, English Version, English medium). **This spec covers only the pilot release (v1).**

**Pilot school:** Masco School, Kanchan (Narayanganj). It is a *potential* pilot, not a confirmed customer.
- We cannot meet staff in person right now; we talk to them by phone or video.
- We have access to the school's currently installed software. Anonymized screenshots, reports, exports and (if available) the schema are in `/discovery`.

**Confirmed priorities:**
1. **Fees and payments.**
2. **Exam result processing.**
3. Student arrival attendance from the ZKTeco face-recognition devices, with SMS to guardians.

**Confirmed constraints:**
- On-premise at the school.
- **No cloud hosting, no online payment gateway and no parent portal in v1.** Parents are reached by **SMS only**.
- The server needs outbound internet only, for SMS.

**Support model:** we install, configure and train. A trained **school super-user** handles first-line support, and our team handles escalations. This reduces our support load but does not eliminate it. The product must be easy to run, diagnose, back up and upgrade without a developer present.

### Evidence rules for business rules
Every business rule in the docs carries one of these tags:
- `OBSERVED` — seen in the legacy material. Cite the file.
- `ASSUMED` — a Bangladeshi default that still needs confirmation.
- `CONFIRMED` — confirmed by school staff. Record **who** (name/role), **when**, **how** (call/video/document) and the **scope** (which classes, sessions, versions and shifts it applies to).

Rules for using the tags:
- **Never invent Masco's rules.**
- `ASSUMED` rules are allowed in prototypes and demos.
- **Critical fee, payment, exam and attendance-SMS rules MUST be `CONFIRMED` before live use.** The system shows a go-live readiness report listing any critical rule still unconfirmed.
- The legacy system is **evidence, not proof of correctness**.

---

## 1. v1 Scope

**In scope:**
- Foundation and security.
- Institution setup.
- Students, guardians and enrollments.
- Migration from the legacy system.
- **Fees and payments.**
- **Exams and results.**
- Gate-arrival attendance (ZKTeco).
- SMS.
- Operations and recovery tooling.
- Core reports and dashboard.
- Parallel run and cut-over tooling.

**Out of scope** (keep extension points; do not build):
- Online gateways and payment callbacks; parent/student internet portal.
- Cloud/SaaS hosting and multi-tenancy.
- HR, payroll and double-entry accounting (provide collection exports for the accountant instead).
- Library, transport, hostel, inventory, LMS, website CMS and admission CRM.
- Class/period attendance by teachers, beyond a manual fallback screen.
- Native mobile app and AI features.

---

## 2. Phase 0 — Discovery, Spikes, Prototype

Phase 0 has three gated steps. Each step ends with my approval.

### 0A — Discovery (documents only, no application code)
Read everything in `/discovery` and produce the following documents:
1. `docs/discovery/current-process.md` — today's process step by step, by role (cashier, accountant, exam controller, attendance officer, class teacher, principal).
2. `docs/discovery/field-inventory.md` — every field seen, with type, example (synthetic) and source file.
3. `docs/discovery/report-inventory.md` — every report. For each, separate **required information** (fields, totals, signatures, legal or board content) from **layout conventions** that staff rely on (paper size, column order, language). Note legacy defects that must **not** be reproduced.
4. `docs/discovery/business-rules.md` — every rule found, with its tag, evidence and confirmation record. Flag which rules are **critical**.
5. `docs/discovery/legacy-pain-points.md` — weaknesses in the legacy system and the improvements we can offer.
6. `docs/discovery/migration-mapping.md` — legacy → new mapping, legacy source keys, transformations and data-quality issues.
7. `docs/discovery/open-questions.md` — numbered questions grouped by role and ordered by how much they block the work, each linked to the rule it would confirm.
8. `docs/discovery/infrastructure.md` — the school's server, OS, SQL Server edition, network, power and backup situation, as far as known.

Then summarize the findings and **wait for approval.**

### 0B — Technical spikes (throwaway code in `/spikes`, never merged into `/src`)
1. **ZKTeco integration spike — on a real device of the same model and firmware as Masco's.**
   - Prove how punches reach our server (see Section 4F).
   - Measure delayed and batched uploads, duplicates, behaviour during offline periods and clock drift.
   - Prove that we can determine **log completeness** per device.
   - Deliver an ADR with a go/no-go and a fallback plan.
2. **SQL Server Express capacity spike — on actual SQL Server Express, not a simulation**, using the target edition and version.
   - Seed 5,000 students, one year of attendance, fees and two exams.
   - Simulate representative concurrent use:
     - 3 cashiers collecting;
     - 20 teachers entering marks;
     - device ingestion;
     - result processing for a full class;
     - month-end billing;
     - principal reports.
   - Record response times, database growth per year and memory pressure.
   - Document when Standard edition becomes necessary.
3. **PDF spike:** render the tabulation sheet and a bilingual receipt with Bangla conjunct letters (যুক্তাক্ষর) checked by eye.
   - Confirm the PDF library's **licence eligibility for our company**. QuestPDF's Community licence has conditions (including revenue thresholds) that may not apply to us.
   - Compare at least one alternative and record the decision in an ADR.

### 0C — Clickable prototype (Angular, mock data, separate from production code)
- Screens:
  - cashier collection screen and receipt;
  - statement-matching exception queue;
  - marks-entry grid;
  - tabulation sheet and mark sheet;
  - attendance dashboard with device status.
- Used for phone/video feedback with school staff. Record the feedback in `docs/discovery/prototype-feedback.md`.

---

## 3. Fixed Technology Decisions

Verify current supported versions before scaffolding and record them in an ADR.

| Area | Decision |
|---|---|
| Backend | ASP.NET Core Web API on the **current .NET LTS**, C# latest |
| Architecture | **Modular monolith**, clean layering, architecture tests enforcing module boundaries |
| Data | EF Core + **SQL Server only** in v1. Avoid SQL-Server-only features in the domain model. A PostgreSQL compatibility spike comes later |
| DB edition | **SQL Server Express** by default, if the 0B capacity spike passes; otherwise Standard. Use a version supported on the chosen OS |
| OS (production) | **Windows Server (currently supported version, e.g. 2022/2025) + IIS** is the tested production configuration. Windows 11 Pro is allowed only for very small installations, after the same test pass. **Windows 10 is not supported** |
| Frontend | Angular (latest stable): standalone components, signals, typed reactive forms, lazy routes. **One UI kit** — choose and justify |
| i18n | Runtime Bangla ⇄ English switching (Transloco) |
| API client | Generated from OpenAPI (NSwag) |
| PDF / Excel | Chosen after the 0B spike / ClosedXML |
| Jobs | Hangfire with SQL Server storage |
| Logging | Serilog to rolling files plus a DB error table, **with sensitive-data redaction** |
| Auth | ASP.NET Core Identity; choose the session approach for a LAN app and justify it in an ADR |

**Library rules:**
- Do not use MediatR, AutoMapper, FluentAssertions or Duende.
- Ask me before adding any paid dependency.

**Deployment model:** one school organization per installation and database. No shared multi-tenancy. Remain compatible with a future "one dedicated database per customer" hosted model.

### Core data-model rules (MUST)
- **Student identity is separate from enrollment.**
  - `Student` is the persistent person: identity, birth registration number, guardians.
  - `Enrollment` is per academic session and records campus, shift, version/medium, class, group, section and roll, with **effective-from/to dates**.
  - Transfers and promotions close one enrollment period and open another; they never edit history.
  - Attendance, invoices, marks and results reference the **enrollment that was valid at the time**. Historical records never change their apparent class or section.
- **Organisation hierarchy as reference data:** Organization → Campus → Shift → Version/Medium → Class → Group → Section. Configurable combinations; do not hard-wire one fixed tree.
- **Dates versus timestamps:**
  - Calendar dates (birth date, attendance date, due date, exam date, effective dates) use `DateOnly`, interpreted in the school's calendar (`Asia/Dhaka`).
  - Event timestamps use `DateTimeOffset`, stored in UTC and displayed in `Asia/Dhaka`.
  - The week starts on Saturday.
- **Money:** `decimal(18,2)`. Rounding rules must be explicit and configurable — never implicit `Math.Round` defaults.
- **Deletion:**
  - Reference and master data may be archived or deactivated.
  - **Financial records and published results are never deleted or soft-deleted.** They change only through explicit states (cancelled, reversed, superseded), with reason, approver and audit.
- **Concurrency:** optimistic concurrency (row version) on invoices, payments, allocations, marks and results.

---

## 4. v1 Modules and Rules

Every changeable rule is **configuration data with defaults**, versioned where it affects money or results.

### A. Foundation & Security
- **Login:** user ID or mobile number plus password; lockout; password policy.
  - **TOTP two-factor login mandatory** for Super Admin, Principal, Accountant and Exam Controller.
  - Session timeout and idle lock; list of active sessions with forced logout.
  - First-run setup creates the admin. **No default passwords.**
- **Authorization:** permission-based roles with data scopes, **enforced server-side on every endpoint**. The UI only hides controls. Default roles:
  - Super Admin (school IT), Principal, Vice Principal, Accountant, Cashier, Exam Controller, Class Teacher, Subject Teacher, Attendance Officer, Viewer.
- **Segregation of duties:** the person who creates a waiver, cancellation, refund or post-lock mark change cannot approve it.
- **Transport and data security:**
  - **HTTPS on the LAN.** The installer sets up a certificate from an internal CA or a managed self-signed certificate, and documents how to trust it on staff devices.
  - HTTP redirects to HTTPS.
  - Secrets and keys are protected with DPAPI / ASP.NET Data Protection, with the key ring backed up (see H).
  - The backup folder is accessible only to the service account and administrators. Backups are encrypted.
  - Sensitive fields (birth registration number, NID, phone numbers) are redacted in logs and in support bundles.
- **Audit trail:** every change records old and new values, user, time and IP. A viewer for it is mandatory for fees, payments and marks.
- **Bilingual data:** Bangla and English fields on everything that is printed.
- **Bangla helpers:**
  - Bangla numerals;
  - amount in words in Bangla and English using lakh/crore and taka/poisa;
  - `1,00,000.00` number formatting;
  - Bangladeshi mobile number validation.
- **Numbering sequences:** gap-safe, transactional and concurrency-safe.

### B. Institution Setup
- Institution profile: EIIN, logo, seal, signatures.
- Campuses, shifts, versions, sessions, classes, groups and sections.
- Subjects with codes and components (CQ / MCQ / Practical / CA), subject–class mapping per session and combined papers.
- Grading schemes, holidays and weekends.
- Setup wizard with NCTB defaults, marked `ASSUMED` until confirmed.

### C. Students, Guardians, Enrollment & Migration
- **Student:** birth registration number (17 digits), religion (for religion-subject mapping), guardians, sibling links, and present and permanent addresses (Division → District → Upazila).
- **Enrollment operations:** admit, section transfer, shift/version change, promotion, detention and Transfer Certificate. All are effective-dated and audited.
- **Migration importer (MUST):**
  - Every imported record keeps a **stable legacy source key**, a source system and an **import batch ID**.
  - **Validation preview** with an error report before any commit; a batch commits all-or-nothing.
  - **Possible duplicates are suggested for human review**, with reasons (name similarity, birth registration number, guardian phone, date of birth). **No automatic merging.**
  - **Re-runnable:**
    - Re-imports update only records that are unchanged in the new system since the last import.
    - Records edited in the new system are **never overwritten**; they go to a conflict report.
    - Records deleted in the legacy system are flagged, not deleted.
  - **Opening balances** are imported per student, per fee head and per period where the legacy system has that breakdown.
    - Where it doesn't, import a single **"Legacy opening balance"** line per student, under a dedicated fee head approved by the accountant.
    - **Never invent a historical split.**
    - Legacy balance lines take part in allocation like any other due, with a confirmed allocation order.
  - **Reconciliation report** comparing legacy and new figures: **per student and per fee head**, plus totals per class, plus counts of students, enrollments and guardians. Every difference must be explained or fixed.

### D. Fees & Payments (top priority)

**D1. Billing**
- Fee heads and fee structures per class, version, group and shift, effective-dated. One-time, monthly, term and annual heads.
- **Monthly invoice generation is repeat-safe across enrollment changes:**
  - The identity of a recurring charge is **independent of enrollment history**: a stable **student billing account** (one per student, or per family where confirmed) + **charge type / fee head** + **billing period**.
  - This identity is enforced with a unique constraint.
  - The enrollment valid at billing time is stored on the line **for historical context only**, never as part of the uniqueness key.
  - A section or shift transfer therefore never creates a second charge for the same period.
  - If a transfer changes the applicable fee, apply the **confirmed proration/adjustment policy** (`ASSUMED` default: no change until the next period). The adjustment is an explicit line or credit note, never a duplicate bill.
  - Runs are recorded as billing batches, with preview and results.
  - **Required test:** bill a student → transfer them mid-month → re-run billing → no duplicate charge. Then repeat with a fee-changing transfer → only the policy-defined adjustment appears.
- **Waivers and scholarships:** percentage or fixed, effective-dated, approved under maker-checker. Sibling and staff-child discounts are configured rules.
- **Late fines:** flat, per-day or slab rules, versioned. Fine waivers require approval.
  - **Accrual is explicit.** Fines are recalculated by a daily job, which records a fine posting for each invoice line, with an *as-of date* and the rule version.
  - Per-day fines grow until the line is fully paid, waived or capped (cap configurable).
  - Collecting a payment first brings fines up to date as of the payment date, inside the same transaction.
  - A fine amount already posted is **frozen for its period**. A later rule change applies only from its effective date.
  - Re-running the accrual job for the same date is idempotent.

**D2. Payments and allocations — separate concepts**
- A **Payment** is money received or claimed, with a mode, amount, payer and reference.
- An **Allocation** links part of a payment to specific invoice lines.
  - One payment can cover several months, heads, or **several siblings**.
  - Allocations are reversible only through explicit reversal entries.
- **Payment states:**
  `Claimed` (entered, not yet verified) → `Verified` / `Rejected`.
  For cheques: `Received` → `Deposited` → `Cleared` / `Bounced`.
  Later: `Reversed`.
  - Only verified or cleared money reduces dues in "cleared" reports.
  - Reports show both *collected* and *cleared* figures.
  - A receipt is issued according to configuration: for cash at once; for cheques and mobile/bank transfers either provisionally or only after verification — **ASSUMED** until confirmed.
- **Payment modes:**
  - cash; cheque; bank deposit (slip reference);
  - bKash, Nagad or Rocket to the school's account, with the cashier entering the transaction ID.
- **Uniqueness:** transaction IDs and references are unique **within provider + receiving account (+ date where the provider reuses references)**. This is enforced by a database constraint and idempotent APIs. A duplicate entry shows the existing payment.
- **Receipt creation is atomic:** payment, allocations, receipt number and audit are written in one transaction. Retries are idempotent through a client-generated request key, so a double-click or network retry never creates a second receipt.
- **Allocation rule:** configurable (default: oldest due first, with fine order **ASSUMED**). The cashier may override it with a reason.
- **Financial invariants (MUST hold under concurrent requests):**
  1. The sum of active allocations of a payment ≤ the payment's verified/cleared amount. Claimed-only funds can be allocated only provisionally, if configured.
  2. The sum of refunds from a payment or credit balance ≤ its refundable amount (allocated to nothing and not already refunded).
  3. **Reversing an allocation never moves money.** It only returns funds to the payment's unallocated balance.
  4. An invoice line's allocated amount ≤ its amount due.
  - Enforce these with transactional checks under an appropriate lock or isolation level (for example `UPDLOCK` / serializable on the payment and balance rows, or a ledger with constrained balances). **Row-version checks alone are not sufficient.** Cover them with concurrency tests.

**D3. Statement matching (deterministic)**
- Import bank and mobile-banking merchant statements (Excel/CSV, with configurable column mapping). Each statement line keeps its source file, line number and import batch.
- **Automatic match only** when a statement line's transaction ID or reference **exactly** matches one payment claim from the same provider and account, **and** the amount matches. The claim then becomes `Verified`.
- **Never auto-assign by amount alone, by student ID alone, or by name.** Lines with a student ID in the reference but no matching claim, multiple candidates, an amount mismatch or no candidate go to the **exception queue**. The accountant resolves them, and **resolutions that create or assign a payment require approval**.
- Statement lines already matched cannot be matched again.
- Keep **payment received** (customer paid) separate from **bank settlement** (money landed in the school account, possibly net of charges). Provider charges are recorded explicitly.

**D4. Cancellation, refunds and credit**
- Receipts are **cancelled**, never deleted. Cancellation requires a reason, a **cancellation type** and approval, and keeps the receipt number in the register as cancelled. Each type has a defined outcome:

| Type | Situation | Payment | Cash / bank | Student balance / credit |
|---|---|---|---|---|
| **Entry error — no money received** | Receipt recorded by mistake | Payment voided | Cashier's expected cash reduced (if cash); no bank effect | Allocations reversed; **no credit created** |
| **Wrong student / wrong lines** | Money received but applied wrongly | Payment stays valid | No cash effect | Allocations reversed and **re-allocated** to the correct student or lines in the same approved operation. A replacement receipt is issued |
| **Money returned** | Money received, then given back | Payment stays valid, refunded | Refund disbursement reduces cash/bank | Allocations reversed, then a **refund** (below) is recorded |

  - A cancellation can never leave spendable credit behind unless the approver explicitly chooses "keep as advance/credit" with a reason.
- A **credit note** reduces what a student owes, for example a waiver after billing or a billing error.
- A **refund** is a separate disbursement of money back to the payer, with mode, reference, approval and a refund voucher. A refund may draw on an unallocated credit balance; it never just edits a payment.

**D5. Cash control and reports**
- **Cashier day closing:** system total against a counted denomination breakdown; shortage or excess recorded; supervisor sign-off. Closed days are locked; later corrections go into the current day with references.
- **Holds:** a configurable hold on admit cards and results for dues, with override and audit.
- **Reports:**
  - daily collection (collected and cleared); cashier-wise; head-wise; class-wise;
  - due list and defaulters; waivers and credit notes; refunds; cancellations;
  - unmatched statement lines; monthly summary;
  - **accountant export** (Excel).
- **SMS:** payment confirmation (on verification) and due reminders.
- **Future-proofing:** `IPaymentGateway` abstraction and payment state machine designed for later gateways, but **no gateway implementation**.

### E. Exams & Results (second priority)

**E1. Setup and marks entry**
- Exam types: Class Test, Monthly, Half-Yearly, Pre-Test, Test, Annual/Final.
- Per-subject components with full marks and separate pass marks. Weightings for combining exams. All of this is versioned per class per session.
- **Marks entry grid:** Excel-like, keyboard-driven, autosaving, validated against full marks. Teachers can enter only their assigned subjects and sections.
- **Every mark cell has an explicit status:**
  `Entered(value)`, `Absent`, `Exempt` (with reason), `NotApplicable` (subject not taken), `Missing` (not yet entered), `Withheld` (e.g., misconduct).
  - **A missing mark is never treated as zero.**
  - Results cannot be finalized while any required cell is `Missing`.
- Entry locks after the deadline. Post-lock changes go through approval under segregation of duties, with full audit.
- Marks import from Excel uses the same validation and statuses.

**E2. Result rule engine**
- Data-driven and **versioned**. Each class/session/exam points at a specific rule-set version.
- **Default NCTB GPA** (`ASSUMED` until confirmed):

| Marks | Grade | Point |
|---|---|---|
| 80–100 | A+ | 5.00 |
| 70–79 | A | 4.00 |
| 60–69 | A- | 3.50 |
| 50–59 | B | 3.00 |
| 40–49 | C | 2.00 |
| 33–39 | D | 1.00 |
| 0–32 | F | 0.00 |

- Compulsory-subject or component failure → F / GPA 0, per the configured rule.
- **4th subject:** only points above 2.00 are added, and GPA is capped at 5.00. Show GPA both with and without the 4th subject.
- Combined papers; grace marks (configurable and audited).
- **Explicit rounding:** of component marks, totals, weighted results and GPA decimals, plus the boundary policy (e.g., whether 79.5 counts as 80).
- **Merit position** with configurable tie-breaks (GPA → total marks → named subjects → roll) and tie output (shared or sequential position), by section, group and overall.
- English-medium grading (letters, percentages) must be expressible as configuration.

**E3. Required test cases (MUST pass before any live publication)**
- Absent student; exempt subject; missing mark (must block finalisation); withheld result.
- Pass marks per component, with the total passed but one component failed.
- Combined papers, where one paper is failed but the combined total passes (rule-dependent).
- Optional (4th) subject at, above and below 2.00 points, and GPA cap behaviour.
- Rounding at every grade boundary (32.5, 33, 39.5, 79.5, 80 …).
- Ties at the same GPA and total, broken by each configured tie-break.
- Students who transfer section mid-session (enrollment history).
- Religion-subject mapping.

**E4. Legacy comparison (golden test)**
- Re-process at least one complete past exam from the anonymized export with our engine. Compare per student: subject grades, GPA with and without the 4th subject, pass/fail and merit position.
- Every difference is classified as **legacy defect**, **rule difference** or **our defect**.
- Our defects are fixed. Legacy defects and rule differences require **written approval by the exam controller** (recorded as a confirmation) **before any live publication**.

**E5. Publication and immutability**
- Results are published with approval, then SMS goes to guardians.
- **A published result is an immutable snapshot**, storing:
  - the exact marks and statuses used;
  - the rule-set version;
  - the report-template version;
  - **the printable identity details as they were at publication**: student name (Bn/En), roll, class, section, group and shift labels;
  - **the institution's details and branding as they were**: name, address, EIIN, logo, signatures and seal;
  - publisher, approver and timestamp.
- The same snapshot principle applies to **issued receipts and Transfer Certificates**. Later changes to a name, the institution profile or a signature never silently alter a reprint of an issued document.
- Later changes to rules, templates or marks **never silently alter a published result**.
  - A correction creates a **new superseding version**, through approval with a reason. It is visible in history and can trigger a correction SMS.
  - Reprints of old results use their snapshot.
- **Outputs (PDF, embedded Bangla fonts):**
  - tabulation sheet (A3/Legal landscape);
  - mark sheet / progress report (bilingual);
  - merit list; failed list; subject analysis; highest marks;
  - principal's summary.

### F. Gate-Arrival Attendance — ZKTeco face devices
- **What it measures:** **a recorded scan at the school gate devices**. That is not presence in class, and not proof that a child did not enter without scanning.
  - Default SMS wording (`ASSUMED`, to confirm in Bangla and English): **"No arrival has been recorded for [name] by HH:MM."**
  - Never use "absent" or "did not come to school".
- **Integration:** as decided in spike 0B. Preferred order:
  1. ADMS/push protocol over the LAN;
  2. SDK pull through a Windows service;
  3. import from the ZKTeco software database or CSV.
- **Ingestion rules (MUST):**
  - **Raw evidence and derived attendance are separate layers.**
    - Every received punch is stored as received (device serial, device record ID where available, device user ID, device timestamp, verify mode, server receipt time, upload batch).
    - Raw evidence is never edited or deleted.
    - Attendance is **derived** from it and can be re-derived.
  - **Transport duplicates** (the same record uploaded again) are recognised by the device record identifier or upload stamp where the device provides one. Without such an identifier, an exact match on all fields is flagged as a *probable* duplicate, and the uncertainty is documented. Transport duplicates are linked to the original and excluded from derivation, but not deleted.
  - **Attendance grouping** (several legitimate scans close together) is a *derivation rule*: a configurable window decides how scans become first-in / last-out. It never removes evidence.
  - **Late batches** are accepted and re-evaluate the day.
  - **Clock drift:** compare device time with server time on every contact. Flag drift beyond a threshold, and allow a configured correction for a time window, with audit.
  - **Device user mapping is effective-dated** (device user ID → student/staff for a date range). Remapping never rewrites old punches' meaning silently.
- **Completeness before any absence SMS (MUST):**
  - For each relevant device, the system must have **positive evidence that its log has been received completely through the cut-off time**. Evidence means the device's upload position/stamp or record count shows no pending records, *and* its clock is within tolerance.
  - The exact evidence mechanism is defined by the 0B spike. A heartbeat alone is **not** sufficient.
  - If completeness cannot be established for any relevant device, **hold** those messages and alert the attendance officer. The officer may release them manually with a reason, or cancel them.
- **Late data after notification:** if a punch arrives after a "not arrived" SMS was sent, the record is corrected automatically. A **configurable correction SMS** (e.g., "arrived at 08:52") is queued, and the incident is logged for review.
- **Rules:** shift-wise in-time and late threshold, holidays/weekends and office-recorded leave. Manual corrections require a reason and are audited. A manual fallback screen for class teachers is available if devices fail.
- **Device health:**
  - last contact, last punch and completeness status per device;
  - clock drift;
  - alerts for silent devices.
- **Reports:**
  - daily summary;
  - monthly register (traditional format);
  - defaulters and attendance percentage;
  - held and corrected SMS log;
  - basic staff attendance report.

### G. SMS
- `ISmsProvider` abstraction with one Bangladeshi provider. Verify its current API, masking rules and **its own billing rules**.
- **Encoding-aware segment counting:**
  - Detect GSM-7 or UCS-2. Bangla uses UCS-2.
  - Single messages: 160 GSM-7 / 70 UCS-2 characters.
  - **Concatenated messages: 153 / 67 per segment.**
  - Count GSM-7 extension characters as 2.
  - Show segments and estimated cost before sending; reconcile against the provider's reports.
- **Transactional outbox:** the notification intent is written **in the same database transaction** as the business event (payment verified, result published, held SMS released). A background worker sends it. A crash between saving and queuing can never lose a notification.
- **Send states:**
  `Pending` → `Sending` → `Accepted` / `Delivered` / `Failed`
  plus **`Uncertain`** and **`Suppressed`**.
- **Retry without double sends:**
  - Use the provider's idempotency key or client message reference where it supports one, and its status-lookup API to resolve timeouts.
  - If the provider accepted the request but the response was lost and the status cannot be determined, mark the message `Uncertain`. **Do not blindly resend it.** It goes to an operator queue for a decision.
  - Only failures that definitely did not send (connection refused, provider rejection) are retried automatically, with backoff.
- **Suppression is permanent per message.**
  - While the global **"suppress all outbound SMS"** switch is on (parallel run, see J), or on any **non-production or restored copy**, every generated message is recorded as `Suppressed` and **never becomes sendable later**.
  - Turning the switch off affects only messages generated afterwards. **Cut-over can never release a backlog.**
- **Environment safety:**
  - Each installation has an environment identity (Production / Test / Restored-copy).
  - A restore or copy starts with SMS disabled and the environment marked non-production, until an administrator re-enables it with a reason.
- Templates with merge fields, balance tracking, delivery log, per-event switches and a daily safety cap.
- Events: not arrived / late, payment verified, dues reminder, result published, correction notices, general notice.

### H. Operations, Upgrade & Recovery
- **Installer:** a PowerShell installer for the tested Windows Server + IIS + SQL Server configuration, with HTTPS certificate setup and service accounts. Includes a pre-flight check (OS version, .NET runtime, SQL edition, disk, ports).
- **Upgrade procedure (MUST — no unqualified "one-click rollback"):**
  1. Run only outside school hours, after a pre-flight compatibility check (app version ↔ database schema version).
  2. Enter **maintenance mode**: block logins and drain in-flight requests.
  3. **Stop background jobs** and device ingestion. The devices keep their logs and are re-read afterwards.
  4. Take a full backup and **verify it** (`RESTORE VERIFYONLY` plus checksum). Also back up files, templates, configuration and the key ring.
  5. Apply the migrations and deploy.
  6. Run automated smoke tests.
  7. Leave maintenance mode.
  - **Rollback rule:** restoring the pre-upgrade backup is allowed **only until the first business transaction after the upgrade**. The system records this point. After it, problems are fixed forward, and the UI and docs say so plainly.
  - Database migrations should be backward-compatible where practical (expand/contract).
- **Backup and recovery scope:** the database, **uploaded files, document/report templates, configuration and Data Protection / encryption keys**. A restored system without its keys must be treated as a failure scenario, and the docs must cover it.
- **Portable key recovery:**
  - Do **not** rely on machine- or account-bound DPAPI alone for the Data Protection key ring.
  - Protect the key ring with a **certificate** (or an equivalent portable mechanism). Export the certificate with its private key to a **separately protected recovery package**: a password-protected file held offline by the school's designated person, with a sealed copy of the password.
  - Document the procedure and test it on a different machine.
- **Backup schedule:**
  - Full backups plus transaction-log backups. SQL Server Express has no SQL Agent, so use a scheduled Windows service or task, which does not depend on the website being open.
  - **Encryption:** SQL Server Express does not support native backup encryption. Encrypt backup files with an external, documented mechanism (e.g., a 7-Zip/AES-256 or age-based step run by the backup service).
    - Write unencrypted intermediate files only to a protected temporary folder accessible solely to the service account.
    - Delete them securely once the encrypted copy has been verified.
  - Retention policy for full and log backups.
  - **At least one copy that the production server cannot modify:** a rotated, disconnected USB drive or a versioned/immutable network or off-site target with separate credentials. A permanently attached USB drive does **not** meet this requirement (ransomware).
  - A **weekly automatic test restore** to a scratch database, with the result shown on the health dashboard.
- **Replacement-server acceptance test (MUST pass before the pilot):**
  1. Provision a clean server.
  2. Install the same application version.
  3. Restore the database, files, templates and configuration from the backups.
  4. Import the recovery certificate and decrypt the required secrets.
  5. Confirm the SMS environment safety switch is on, then verify data and resume operations.
  - All of this must finish within the agreed RTO, with data loss within the agreed RPO.
- **Recovery targets:** the RPO (maximum acceptable data loss) and RTO (maximum time to restore) are **agreed with the school and recorded**. Proposed defaults, `ASSUMED`: RPO ≤ 1 hour, RTO ≤ 4 hours. Document and test a **full recovery runbook** (server loss, database corruption, ransomware, lost keys).
- **Always-running background work:**
  - Billing, fine accrual, the SMS outbox, device ingestion, backups and health checks run in a **dedicated Windows Service worker**, not dependent on IIS app-pool activity or a user having the website open. Alternatively, IIS can be configured as AlwaysRunning with preload, but this must be documented and tested.
  - **Required tests:** after a server reboot with nobody logged in, jobs resume, devices are re-read and missed schedules catch up exactly once.
- **Health dashboard:**
  - DB size against the edition limit; disk space; backup, off-line copy and test-restore status;
  - device completeness and clock drift; SMS balance and queue;
  - failed jobs; certificate expiry; app/schema version.
- **Support bundle:** one-click export of redacted logs, configuration (no secrets) and version information.
- **Error messages** in Bangla and English with error codes, plus a troubleshooting guide for the super-user.
- **Licensing:** an offline-signed licence file (institution, modules, expiry), with a grace period. It must never block access to data or backups.

### I. Dashboard & Reports
- **Principal dashboard:**
  - today's arrivals and late arrivals;
  - collection today and this month (collected and cleared);
  - total dues;
  - pending approvals and exceptions;
  - result KPIs.
- Every list screen: server-side paging, search and filters, plus Excel/PDF export subject to permissions.

### J. Parallel Run & Cut-over (MUST)
- **One authoritative system per stage** for each function (receipts, billing, results, SMS), recorded in a `docs/operations/parallel-run-plan.md` table.
  - Example: during the parallel run, **the legacy system remains authoritative**. Our system is in **comparison mode**:
    - legacy transactions are entered or imported into it;
    - it issues no customer-facing receipts;
    - billing runs are compare-only;
    - **outbound SMS is suppressed globally**.
- **Daily reconciliation, per student and per fee head:** dues, payments and allocations. Plus exam results per student, and attendance counts per day. Differences are logged with cause and resolution.
- **Cut-over runbook:**
  - entry criteria (reconciliation clean for N consecutive days or cycles; critical rules `CONFIRMED`; staff trained);
  - legacy freeze time; final delta import; final reconciliation;
  - **signed sign-off** by principal, accountant and exam controller;
  - switch of authority; SMS enabled;
  - a **fallback plan** with its own decision deadline.

---

## 5. UX Standards
- Simple screens with Bangla labels by default and the fewest possible clicks for daily tasks.
- The cashier screen and marks grid are fully keyboard-operable.
- Responsive layouts, so teachers can use phones on the school Wi-Fi.
- Pending, held and exception states are always visible. Never hide uncertainty from the user.
- Print-perfect, server-generated PDFs.

---

## 6. Quality Engineering
- **Unit tests** (table-driven, Bangladeshi cases) for:
  - billing idempotency, including **mid-month transfer + re-run**; fine accrual and freezing; waivers; allocation;
  - payment state transitions; **all three cancellation types**; refund limits;
  - outbox atomicity; `Uncertain` SMS handling; **suppressed messages never sent after the switch is turned off**;
  - raw-versus-derived attendance (transport duplicate versus a legitimate double scan);
  - matching rules (including the never-match-by-amount cases);
  - refunds and credit notes;
  - all the E3 cases; publication immutability;
  - attendance deduplication, late batches, drift and completeness gating.
- **Golden tests** from the anonymized legacy data for results and opening balances.
- **Integration tests** against **SQL Server Express** (Testcontainers or a dedicated test instance).
- **Concurrency tests (must prove the D2 invariants):**
  - double-submit of the same payment;
  - two simultaneous allocations or refunds against the same payment or credit balance;
  - two cashiers working on the same family;
  - simultaneous marks edits;
  - concurrent billing runs.
- **Playwright end-to-end tests:**
  1. collect fee → receipt → day close;
  2. statement import → match → exception resolution;
  3. marks entry → result → publish → tabulation;
  4. device punch → completeness → SMS (with the SMS provider mocked).
- **Restore test** in CI or staging: back up → restore → app runs with the keys → data verified. Plus the **replacement-server acceptance test** (Section H) before the pilot.
- **Reboot test:** jobs, the outbox and device ingestion resume with nobody logged in.
- **Capacity re-test on the finished application** (Phase 5): repeat the 0B workload against the real application on the target SQL Server Express server, with representative concurrent users. The 0B spike only identifies constraints; this re-test is the pilot gate.
- Analyzers on, with warnings treated as errors (GitHub Actions).

---

## 7. Roadmap — a planning hypothesis, not a commitment

Two developers. The Phase 3 tracks run in parallel. Re-estimate after Phase 0.

| Phase | Content | Est. |
|---|---|---|
| 0A | Discovery documents (approval gate) | 1–2 wks |
| 0B | Spikes: ZKTeco device, SQL Express capacity, PDF/licence (approval gate) | 1–2 wks |
| 0C | Clickable prototype + staff feedback (approval gate) | 1–2 wks |
| 1 | Foundation & security (A), app shell, CI, installer skeleton | 3 wks |
| 2 | Setup (B), Students/Enrollment, migration importer (C) | 3–4 wks |
| 3a | Fees & Payments (D) — Developer 1 | 5 wks |
| 3b | Exams & Results (E) — Developer 2 | 5 wks |
| 4 | Attendance (F), SMS (G) | 3 wks |
| 5 | Operations & recovery (H), dashboard (I), parallel-run tooling (J), capacity re-test, replacement-server test, security review, manuals, super-user training material | 3–4 wks |
| 6 | Parallel run: at least one full fee cycle and one exam, then cut-over | 6–8 wks (depends on the exam calendar) |

**Risks that can extend the plan:**
- school availability for rule confirmation;
- quality of the legacy data;
- ZKTeco feasibility;
- timing of a real exam;
- merchant-statement formats.

---

## 8. Working Method (follow strictly)
1. Start with **Phase 0A**. Do not write application code until 0A is approved, and do not start Phase 1 until 0B and 0C are approved.
2. Alongside 0A, produce:
   - `docs/architecture.md`;
   - ADRs in `docs/adr/`;
   - a domain model overview (Student vs Enrollment; Payment vs Allocation; result snapshots);
   - the permission and segregation-of-duties matrix;
   - `CLAUDE.md` (concise rules and commands).
3. For each phase:
   1. Update `docs/modules/<module>.md` (rules with tags, edge cases, acceptance tests).
   2. Build the backend with tests, then the Angular screens, then seed data and reports.
   3. Run all tests.
   4. Update the docs.
   5. Summarize: what was built, how to demo it, known gaps, unconfirmed critical rules, next step.
4. Never hard-code changeable rules. Never invent Masco's rules. Leave no `TODO` in money, results, attendance-SMS, backup or upgrade logic.
5. Make small, logical commits. Keep secrets and real personal data out of the repo.

**Begin with Phase 0A.**
