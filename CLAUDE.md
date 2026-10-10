# CLAUDE.md — School Management System, Pilot v1

Private repo. Read this first every session. Full contract: `docs/spec/pilot-spec.md` (Spec v2.1). Plan: `docs/Pilot_User_Stories.md`. Status: `docs/progress.md`.

## Product
On-premise school management system for Bangladesh. Pilot: Masco School, Kanchan (Narayanganj), a *potential* pilot. Team: two experienced .NET / SQL Server / Angular developers. Long-term vision is a full ERP; **this repo covers v1 only**.
Priorities: **1) Fees and payments, 2) Exam results, 3) ZKTeco gate-arrival attendance + SMS to guardians.**
Constraints: on-premise; server needs outbound internet only for SMS; parents reached by SMS only.

## v1 scope (spec §1)
In: foundation/security, institution setup, students/guardians/enrollment, legacy migration, fees & payments, exams & results, gate attendance, SMS, ops & recovery tooling, core reports/dashboard, parallel run & cut-over.
**Out (keep extension points, do not build):** online gateways/callbacks, parent/student portal, cloud/SaaS/multi-tenancy, HR/payroll/double-entry accounting, library/transport/hostel/inventory/LMS/CMS/admission CRM, teacher period attendance beyond a manual fallback, native mobile, AI.

## Tech stack (spec §3)
ASP.NET Core Web API on current .NET LTS, modular monolith with architecture tests; EF Core + SQL Server only (Express by default, pending the capacity spike); Windows Server + IIS (Windows 10 unsupported); Angular latest stable (standalone, signals, typed forms, lazy routes), one UI kit; Transloco (runtime Bangla/English); NSwag API client; ClosedXML; PDF library per ADR-008; Hangfire (SQL storage); Serilog with redaction; ASP.NET Core Identity.
**Banned:** MediatR, AutoMapper, FluentAssertions, Duende. **Ask before any paid dependency.**
One school per installation/database; no multi-tenancy. Verify current versions and record in an ADR before scaffolding.

## Non-negotiables (full index with owners: `docs/requirements-register.md`)
**Evidence & process**
- Rules are tagged `OBSERVED` / `ASSUMED` / `CONFIRMED`. **Never invent Masco's rules.** Critical fee/payment/exam/attendance-SMS rules must be `CONFIRMED` before live use.
- Legacy system is evidence, not proof of correctness.
- No application code until Gate 0A approved; no Phase 1 until Gates 0B and 0C approved.
- No `TODO` in money, results, attendance-SMS, backup or upgrade logic.
- Critical test expectations change only with a recorded source correction/owner decision plus independent review; never weaken a test to pass.
- No secrets or real personal data in the repo. No paid dependencies without approval.

**Data model**
- Student (person) is separate from Enrollment (per session, effective-dated). Transfers/promotions close and open enrollments; history is never edited. Records reference the enrollment valid at the time.
- `DateOnly` for calendar dates (Asia/Dhaka); `DateTimeOffset` UTC for timestamps; week starts Saturday.
- Money is `decimal(18,2)`; rounding policies are explicit and configurable.
- Financial records and published results are never deleted or soft-deleted; they change only by explicit state (cancelled/reversed/superseded) with reason, approver, audit.
- Row-version concurrency on invoices, payments, allocations, marks, results. Numbering sequences are gap-safe.

**Fees**
- Four payment invariants hold under concurrency, enforced with locks/ledger, not row version alone (allocations ≤ verified amount; refunds ≤ refundable; reversal never moves money; line allocated ≤ due).
- Billing uniqueness = billing account + fee head + period, independent of enrollment (family refinement only via ADR-009). Transfer + re-run never double-bills.
- Receipt creation is atomic and idempotent (client request key). Statement auto-match only on exact transaction ID/reference + provider/account + amount; never by amount, student ID or name.
- Receipts are cancelled, never deleted (three typed outcomes). Refunds are separate approved disbursements.

**Results**
- A missing mark is never zero; finalisation blocked while required cells are Missing.
- Published results, issued receipts and Transfer Certificates are immutable snapshots (including names and institution branding); corrections supersede with approval.

**Attendance & SMS**
- Raw punches are immutable; attendance is derived and re-derivable.
- No "not arrived" SMS without positive per-device completeness evidence; otherwise hold and alert. Wording is "No arrival has been recorded…", never "absent".
- Transactional outbox. `Uncertain` SMS is never auto-resent. `Suppressed` SMS is never sent later; cut-over never releases a backlog. Restored copies start with SMS disabled.

**Security & ops**
- Authorization enforced server-side on every endpoint; segregation of duties on approvals. TOTP 2FA for Super Admin, Principal, Accountant, Exam Controller. No default passwords.
- Sensitive data (birth registration no., NID, phones) redacted in logs and support bundles.
- Upgrades: maintenance mode, verified backup, rollback only until first post-upgrade business transaction. Backups encrypted, one copy the server cannot modify, portable key recovery.
- Every changeable rule is configuration with defaults, versioned where it affects money/results.

## Story Completion Protocol
Full contract: `docs/story-protocol.md`. One story per session; find it with `grep "### US-xxx ·" docs/Pilot_User_Stories.md` and read only that section — never the whole plan file.
1. Apply the execution and completion contract: run applicable checks, distinguish expected-red contract tests from active regression tests, never mark unexecuted checks PASS (use PASS / FAIL / NOT RUN / NOT APPLICABLE; missing prerequisite = BLOCKED).
2. Update `docs/progress.md`: story ID, status, base/head commit, acceptance evidence, actual commands/results, owner verification steps, date, blockers. Evidence in `docs/acceptance/<ID>.md`.
3. Review the diff; commit only this story's files with message `US-xxx: <title>`. Never include unrelated work or claim owner acceptance (Claude sets *Ready for owner check*; only the owner sets *Accepted*).
4. Report: what was built, how to verify/demo, known gaps, open questions, critical rules still ASSUMED.
5. **STOP** — never start the next story.

## Where things are
- Spec → `docs/spec/pilot-spec.md` (§0 context/evidence, §1 scope, §2 Phase 0, §3 stack & data rules, §4 modules A–J, §5 UX, §6 quality, §7 roadmap, §8 working method)
- Legacy study checklist → `docs/spec/legacy-study-checklist.md`
- Plan, tracker, reusable prompts R-1…R-7 → `docs/Pilot_User_Stories.md`
- Status → `docs/progress.md` · Protocol → `docs/story-protocol.md` · MUST index → `docs/requirements-register.md`
- `discovery/` anonymized legacy evidence only · `docs/discovery` discovery docs · `docs/adr` ADRs · `docs/modules` module rules · `docs/operations` runbooks · `docs/acceptance` story evidence
- `spikes/` throwaway · `prototype/` mock Angular · `src/` backend · `web/` frontend · `tests/` · `deploy/` installer & scripts
- Repository paths are repo-relative; a leading slash never means filesystem root.

## Commands
_To be filled in Phase 1 (US-101 onward)._
- Build: _TBD_ · Test: _TBD_ · Run API: _TBD_ · Run web: _TBD_ · Lint: _TBD_
