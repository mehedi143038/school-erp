# Requirement Register — MUST rules index

Index of every **MUST** rule in `docs/spec/pilot-spec.md` (Spec v2.1) with its section and the story that owns it. Detailed rules live in the spec and, later, `docs/modules/`. US-021 builds the full traceability baseline; this register is the US-001 starting index, so CLAUDE.md can stay short without omitting rules.

| ID | Rule (short) | Spec § | Owning stories |
|---|---|---|---|
| M-01 | Business rules carry OBSERVED / ASSUMED / CONFIRMED tags; never invent Masco rules | 0 | US-004, US-112 |
| M-02 | Critical fee, payment, exam, attendance-SMS rules CONFIRMED before live use; go-live readiness report lists unconfirmed ones | 0 | US-112, US-619 |
| M-03 | Student identity separate from Enrollment; transfers/promotions close and open effective-dated enrollments, never edit history | 3 | US-207, US-209 |
| M-04 | Attendance, invoices, marks, results reference the enrollment valid at the time | 3 | US-207, US-304, US-405 |
| M-05 | Calendar dates `DateOnly` (Asia/Dhaka); timestamps `DateTimeOffset` UTC; week starts Saturday | 3 | US-104 |
| M-06 | Money `decimal(18,2)`; rounding explicit and configurable | 3 | US-104 |
| M-07 | Financial records and published results never deleted or soft-deleted; explicit states with reason, approver, audit | 3 | US-106, US-307, US-410 |
| M-08 | Optimistic concurrency (row version) on invoices, payments, allocations, marks, results | 3 | US-105 |
| M-09 | No MediatR, AutoMapper, FluentAssertions, Duende; ask before any paid dependency | 3 | US-101 |
| M-10 | TOTP 2FA mandatory for Super Admin, Principal, Accountant, Exam Controller; no default passwords | 4A | US-108, US-109 |
| M-11 | Authorization enforced server-side on every endpoint; UI only hides | 4A | US-110 |
| M-12 | Segregation of duties: creator cannot approve waiver, cancellation, refund, post-lock mark change | 4A | US-111 |
| M-13 | HTTPS on LAN; secrets via Data Protection; encrypted backups; backup folder restricted | 4A | US-120, US-603 |
| M-14 | Sensitive fields redacted in logs and support bundles | 4A | US-116, US-609 |
| M-15 | Audit trail with old/new values, user, time, IP; viewer for fees, payments, marks | 4A | US-106 |
| M-16 | Numbering sequences gap-safe, transactional, concurrency-safe | 4A | US-107 |
| M-17 | Migration: stable legacy source key, source system, batch ID | 4C | US-212 |
| M-18 | Migration: validation preview; all-or-nothing commit | 4C | US-212 |
| M-19 | Migration: duplicates suggested for human review, no automatic merge | 4C | US-214 |
| M-20 | Migration re-runnable: never overwrite edited records; conflict report; legacy-deleted flagged not deleted | 4C | US-215 |
| M-21 | Opening balances per student/head/period, else single "Legacy opening balance" line; never invent a split | 4C, 4D | US-311 |
| M-22 | Reconciliation report per student and fee head, class totals, counts | 4C, 4J | US-216, US-311 |
| M-23 | Billing uniqueness: student billing account + fee head + period, independent of enrollment; unique constraint | 4D1 | US-303, US-304 |
| M-24 | Transfer never creates duplicate charge; fee-changing transfer yields only the policy adjustment (required test) | 4D1 | US-302, US-304 |
| M-25 | Fine accrual explicit, daily job, frozen per period, idempotent; payment brings fines current in same transaction | 4D1 | US-306 |
| M-26 | Payment and Allocation are separate; allocations reversed only via reversal entries | 4D2 | US-307 |
| M-27 | Transaction ID / reference unique per provider + account (+ date); DB constraint and idempotent APIs | 4D2 | US-308 |
| M-28 | Receipt creation atomic and idempotent via client request key | 4D2 | US-307 |
| M-29 | Four payment invariants hold under concurrency (allocations ≤ verified amount; refunds ≤ refundable; reversal never moves money; line allocated ≤ due); transactional locking, not row-version alone | 4D2 | US-301, US-302, US-307 |
| M-30 | Statement auto-match only on exact transaction ID/reference + provider/account + amount; never by amount, student ID or name alone; resolutions that create/assign a payment need approval | 4D3 | US-313 |
| M-31 | Matched statement lines cannot be matched again; payment received separate from bank settlement | 4D3 | US-313, US-314 |
| M-32 | Receipts cancelled, never deleted; three cancellation types with defined outcomes; no leftover credit unless explicitly kept | 4D4 | US-315 |
| M-33 | Refund is a separate approved disbursement with voucher; never edits a payment | 4D4 | US-316 |
| M-34 | Cashier day closing with denomination count; closed days locked | 4D5 | US-317 |
| M-35 | Missing mark is never zero; explicit cell statuses; finalisation blocked while any required cell Missing | 4E1 | US-405, US-408 |
| M-36 | Entry locks after deadline; post-lock changes via approval with audit | 4E1 | US-405 |
| M-37 | Result rules data-driven and versioned; explicit rounding and boundary policy | 4E2 | US-408 |
| M-38 | E3 required test cases pass before any live publication | 4E3 | US-402 |
| M-39 | Legacy golden-test differences classified; exam controller written approval before live publication | 4E4 | US-414, H-10 |
| M-40 | Published results, issued receipts and Transfer Certificates are immutable snapshots (incl. identity and institution details); corrections create superseding versions | 4E5 | US-210, US-410 |
| M-41 | Raw punches stored as received and never edited or deleted; attendance derived and re-derivable | 4F | US-504, US-507 |
| M-42 | Transport duplicates linked and excluded from derivation, not deleted; grouping is a derivation rule | 4F | US-504, US-507 |
| M-43 | Clock drift flagged; device-user mapping effective-dated | 4F | US-503, US-505 |
| M-44 | No "not arrived" SMS without positive completeness evidence per device; otherwise hold and alert; heartbeat insufficient | 4F | US-506, US-516 |
| M-45 | Late punch after SMS: auto-correct, configurable correction SMS, incident logged | 4F | US-516 |
| M-46 | Attendance wording "No arrival has been recorded…"; never "absent" | 4F | US-516 |
| M-47 | SMS transactional outbox: intent written in same transaction as business event | 4G | US-113, US-513 |
| M-48 | Uncertain SMS never blindly resent; only definite failures retried | 4G | US-513 |
| M-49 | Suppression permanent per message; cut-over never releases backlog | 4G, 4J | US-113, US-514 |
| M-50 | Environment identity; restored copy starts non-production with SMS disabled | 4G | US-113, US-514 |
| M-51 | Encoding-aware segment counting (GSM-7/UCS-2; 153/67 concatenated) | 4G | US-512 |
| M-52 | Upgrade procedure: off-hours, maintenance mode, stop jobs, verified backup, migrate, smoke test; rollback only until first post-upgrade business transaction | 4H | US-602 |
| M-53 | Backup covers DB, files, templates, config, key ring; external encryption; at least one copy the server cannot modify; weekly test restore | 4H | US-603, US-605 |
| M-54 | Portable key recovery (certificate-protected key ring, offline recovery package) | 4H | US-604 |
| M-55 | Replacement-server acceptance test within agreed RTO/RPO before pilot | 4H | US-606, H-12 |
| M-56 | Background work in a dedicated Windows Service; reboot test: jobs resume, catch up exactly once | 4H | US-114, US-607 |
| M-57 | Licence must never block access to data or backups | 4H | US-610 |
| M-58 | One authoritative system per stage; parallel run in comparison mode; global SMS suppression | 4J | US-612 |
| M-59 | Daily reconciliation per student and fee head; cut-over runbook with signed sign-off and fallback plan | 4J | US-613, US-614 |
| M-60 | Concurrency tests prove D2 invariants; integration tests on real SQL Server Express; capacity re-test on finished app | 6 | US-302, US-105, US-616 |
| M-61 | No TODO in money, results, attendance-SMS, backup or upgrade logic; no secrets or real personal data in repo | 8 | all |
| M-62 | Phase gating: no application code until 0A approved; Phase 1 not before 0B and 0C approved | 8 | H-02, H-06, H-08 |
