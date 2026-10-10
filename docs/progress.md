# Progress Register

Working status register for the pilot plan (`docs/Pilot_User_Stories.md`, master tracker §5). Rows are in **execution order**, not numeric ID order.

**Statuses:** Not started · In progress · Blocked · Ready for owner check · Accepted · Reopened. Only the owner sets *Accepted*. See `docs/story-protocol.md`.

| Order | ID | Title | Status | Date | Notes |
|---|---|---|---|---|---|
| 1 | H-00 | Prepare the repository and the discovery evidence | In progress | 2026-10-10 | Spec, checklist, plan present. /discovery evidence, pilot contact and written permission not yet supplied. |
| 2 | US-001 | Project memory (CLAUDE.md) and progress tracker | Ready for owner check | 2026-10-10 | See docs/acceptance/US-001.md |
| 3 | US-002 | Discovery: current process and field inventory | Not started |  |  |
| 4 | US-003 | Discovery: report inventory | Not started |  |  |
| 5 | US-004 | Discovery: business rules register | Not started |  |  |
| 6 | US-005 | Discovery: pain points and migration mapping | Not started |  |  |
| 7 | US-006 | Discovery: open questions (English + Bangla) and infrastructure | Not started |  |  |
| 8 | US-021 | Requirement coverage, scope decisions and dependency baseline | Not started |  |  |
| 9 | US-022 | Agree performance, retention and recovery targets | Not started |  |  |
| 10 | US-007 | Architecture, ADRs, domain model and permission matrix | Not started |  |  |
| 11 | US-008 | Module specification files | Not started |  |  |
| 12 | H-01 | School phone/video calls and confirmations | Not started |  |  |
| 13 | H-02 | ★ GATE 0A — approve discovery | Not started |  |  |
| 14 | H-03 | Get a test ZKTeco device | Not started |  |  |
| 15 | US-009 | ZKTeco spike: capture punches from the real device | Not started |  |  |
| 16 | US-010 | ZKTeco spike: experiment protocol and analysis scripts | Not started |  |  |
| 17 | H-04 | Run the ZKTeco experiments | Not started |  |  |
| 18 | US-011 | ZKTeco analysis and ADR-006 (go/no-go + completeness evidence) | Not started |  |  |
| 19 | US-012 | SQL Server Express capacity spike: seed and workload | Not started |  |  |
| 20 | H-05 | Run the capacity test on target-like hardware | Not started |  |  |
| 21 | US-013 | Capacity analysis and ADR-007 | Not started |  |  |
| 22 | US-014 | PDF library and Bangla rendering spike (ADR-008) | Not started |  |  |
| 23 | H-06 | ★ GATE 0B — device, capacity and PDF decisions | Not started |  |  |
| 24 | US-015 | Prototype: shell and mock data | Not started |  |  |
| 25 | US-016 | Prototype: cashier collection screen and receipt preview | Not started |  |  |
| 26 | US-017 | Prototype: statement-matching exception queue | Not started |  |  |
| 27 | US-018 | Prototype: marks-entry grid, tabulation sheet and mark sheet previews | Not started |  |  |
| 28 | US-019 | Prototype: attendance dashboard with device status | Not started |  |  |
| 29 | H-07 | Prototype feedback calls | Not started |  |  |
| 30 | US-020 | Record prototype feedback and update specs | Not started |  |  |
| 31 | H-08 | ★ GATE 0C — prototype approved; Phase 1 may start | Not started |  |  |
| 32 | US-101 | Backend solution scaffold and architecture tests | Not started |  |  |
| 33 | US-102 | Angular workspace scaffold | Not started |  |  |
| 34 | US-103 | CI pipeline (GitHub Actions) | Not started |  |  |
| 35 | US-104 | Building blocks: clock, dates, money and rounding, results, immutable records | Not started |  |  |
| 36 | US-105 | Persistence and integration-test infrastructure (SQL Server Express) | Not started |  |  |
| 37 | US-116 | Logging, error codes and sensitive-data redaction | Not started |  |  |
| 38 | US-106 | Audit trail | Not started |  |  |
| 39 | US-107 | Transactional, gap-safe numbering sequences | Not started |  |  |
| 40 | US-108 | Login, password policy, lockout and first-run setup | Not started |  |  |
| 41 | US-109 | Mandatory TOTP two-factor login, session control and idle lock | Not started |  |  |
| 42 | US-110 | Permissions, default roles and data scopes (server-side) | Not started |  |  |
| 43 | US-111 | Approval workflow and segregation of duties | Not started |  |  |
| 44 | US-112 | Versioned rule configuration and go-live readiness report | Not started |  |  |
| 45 | US-113 | Transactional outbox, environment identity and SMS suppression switch | Not started |  |  |
| 46 | US-114 | Windows Service worker and background job infrastructure | Not started |  |  |
| 47 | US-115 | Bangla and Bangladesh formatting helpers | Not started |  |  |
| 48 | US-117 | Angular app shell: login, 2FA, setup, navigation, errors | Not started |  |  |
| 49 | US-118 | Shared UI components: server-side grid, forms, status badges, reason dialogs | Not started |  |  |
| 50 | US-119 | Document generation and export services (Excel, PDF with Bangla fonts) | Not started |  |  |
| 51 | US-120 | HTTPS on the LAN and installer skeleton with pre-flight checks | Not started |  |  |
| 52 | US-121 | ★ Phase 1 review gate | Not started |  |  |
| 53 | US-201 | Institution profile and versioned file storage | Not started |  |  |
| 54 | US-202 | Academic structure: campuses, shifts, versions, sessions, classes, groups, sections | Not started |  |  |
| 55 | US-203 | Subjects, components, subject–class mapping, combined papers, religion subjects | Not started |  |  |
| 56 | US-204 | Calendar: weekends, holidays, working days | Not started |  |  |
| 57 | US-205 | Grading schemes and the setup wizard with NCTB defaults (ASSUMED) | Not started |  |  |
| 58 | US-206 | Geographic master data (Division → District → Upazila) | Not started |  |  |
| 59 | US-207 | Student, Guardian and Enrollment domain model | Not started |  |  |
| 60 | US-218 | Student subject choices and exam-roster snapshots | Not started |  |  |
| 61 | US-208 | Student and guardian screens, search and 360° view | Not started |  |  |
| 62 | US-219 | Minimal staff directory for teachers and attendance | Not started |  |  |
| 63 | US-209 | Enrollment operations: admit, transfer, change, promote, detain, withdraw | Not started |  |  |
| 64 | US-210 | Issued-document snapshot service | Not started |  |  |
| 65 | US-211 | Transfer Certificate | Not started |  |  |
| 66 | US-212 | Import framework: batches, source keys, staging, preview, all-or-nothing commit | Not started |  |  |
| 67 | US-213 | Student, guardian and enrollment importer | Not started |  |  |
| 68 | US-214 | Duplicate suggestions with human review (no automatic merge) | Not started |  |  |
| 69 | US-215 | Re-import rules: unchanged-only updates, conflict report, legacy-deleted flags | Not started |  |  |
| 70 | US-216 | Migration reconciliation report (non-financial) | Not started |  |  |
| 71 | US-217 | ★ Phase 2 review gate | Not started |  |  |
| 72 | US-301 | Fees domain design and ADR-009 (invariant enforcement) | Not started |  |  |
| 73 | US-302 | Fees critical tests first (test-first, tagged Critical) | Not started |  |  |
| 74 | US-303 | Fee heads, fee structures and billing accounts | Not started |  |  |
| 75 | US-322 | Credit-note posting primitive and approved issuance | Not started |  |  |
| 76 | US-304 | Repeat-safe invoice generation (billing batches) | Not started |  |  |
| 77 | US-305 | Waivers, scholarships and discounts with approval | Not started |  |  |
| 78 | US-306 | Late-fine rules and daily accrual job | Not started |  |  |
| 79 | US-307 | Payment core: atomic receipts, allocations, invariants, idempotency | Not started |  |  |
| 80 | US-308 | Payment modes and state machines (cash, cheque, bank, mobile) | Not started |  |  |
| 81 | US-309 | Allocation rules and cashier override | Not started |  |  |
| 82 | US-310 | Cashier collection screen and bilingual money receipt | Not started |  |  |
| 83 | US-311 | Opening balances import and financial reconciliation | Not started |  |  |
| 84 | US-312 | Statement import with column mapping | Not started |  |  |
| 85 | US-313 | Deterministic matching and the exception queue | Not started |  |  |
| 86 | US-314 | Settlement and provider charges | Not started |  |  |
| 87 | US-316 | Refunds and vouchers; integrate existing credit notes | Not started |  |  |
| 88 | US-315 | Receipt cancellation (three types) | Not started |  |  |
| 89 | US-317 | Cashier day closing with denomination count | Not started |  |  |
| 90 | US-318 | Dues holds on admit cards and results | Not started |  |  |
| 91 | US-319 | Fee reports and accountant Excel export | Not started |  |  |
| 92 | US-320 | Fee notification intents and the payment-gateway extension point | Not started |  |  |
| 93 | US-321 | ★ Fees review gate | Not started |  |  |
| 94 | US-401 | Results domain design (ADR-010) | Not started |  |  |
| 95 | US-402 | Results critical tests first (E3 + immutability) | Not started |  |  |
| 96 | H-09 | Exam controller checks the expected results of the test cases | Not started |  |  |
| 97 | US-403 | Exam setup (types, schedule, components, weighting) | Not started |  |  |
| 98 | US-416 | Exam admit cards and dues-hold enforcement | Not started |  |  |
| 99 | US-404 | Teaching assignments (marks-entry permissions) | Not started |  |  |
| 100 | US-405 | Marks entry API: statuses, validation, deadline lock, post-lock approval | Not started |  |  |
| 101 | US-406 | Marks entry grid (Excel-like, keyboard-driven) | Not started |  |  |
| 102 | US-407 | Marks import from Excel | Not started |  |  |
| 103 | US-408 | Result rule engine (GPA, components, 4th subject, combined papers, rounding) | Not started |  |  |
| 104 | US-409 | Combined-exam weighting and merit positions | Not started |  |  |
| 105 | US-410 | Finalisation, publication, immutable snapshots and corrections | Not started |  |  |
| 106 | US-411 | Tabulation sheet PDF | Not started |  |  |
| 107 | US-412 | Mark sheet / progress report PDF (bilingual) | Not started |  |  |
| 108 | US-413 | Result analysis reports | Not started |  |  |
| 109 | US-414 | Golden test: legacy exam comparison harness | Not started |  |  |
| 110 | H-10 | Exam controller approves the golden-test differences | Not started |  |  |
| 111 | US-415 | ★ Results review gate | Not started |  |  |
| 112 | US-501 | Attendance domain design (from ADR-006) | Not started |  |  |
| 113 | US-502 | Attendance critical tests first | Not started |  |  |
| 114 | US-503 | Devices and effective-dated device-user mapping | Not started |  |  |
| 115 | US-504 | Punch ingestion and the immutable raw evidence store | Not started |  |  |
| 116 | US-505 | Clock drift detection and correction windows | Not started |  |  |
| 117 | US-506 | Completeness evidence per device | Not started |  |  |
| 118 | US-507 | Attendance derivation and rules | Not started |  |  |
| 119 | US-508 | Manual corrections, leave and the teacher fallback screen | Not started |  |  |
| 120 | US-509 | Device health dashboard and alerts | Not started |  |  |
| 121 | US-510 | Attendance reports | Not started |  |  |
| 122 | H-11 | Choose the SMS provider and collect its API documentation | Not started |  |  |
| 123 | US-511 | SMS provider adapter | Not started |  |  |
| 124 | US-512 | Encoding-aware segment counting and cost estimate | Not started |  |  |
| 125 | US-513 | Outbox sender worker: send states, safe retries, Uncertain handling | Not started |  |  |
| 126 | US-514 | Suppression permanence, environment safety, daily cap, per-event switches | Not started |  |  |
| 127 | US-515 | SMS templates, balance tracking, delivery log | Not started |  |  |
| 128 | US-520 | Reconcile SMS segment costs with provider reports | Not started |  |  |
| 129 | US-516 | "No arrival recorded" notices with completeness gating and corrections | Not started |  |  |
| 130 | US-517 | Wire the remaining SMS events end to end | Not started |  |  |
| 131 | US-519 | Finish cross-module screens, snapshots and fallback paths | Not started |  |  |
| 132 | US-518 | ★ Phase 4 review gate (attendance + SMS) | Not started |  |  |
| 133 | US-601 | Full installer (Windows Server + IIS + SQL Server + worker + HTTPS) | Not started |  |  |
| 134 | US-604 | Portable key protection and the recovery package | Not started |  |  |
| 135 | US-603 | Backup service: full + log backups, external encryption, retention, offline copy | Not started |  |  |
| 136 | US-602 | Maintenance mode and the safe upgrade procedure (rollback window) | Not started |  |  |
| 137 | US-605 | Weekly test restore and the restore wizard | Not started |  |  |
| 138 | US-606 | Recovery runbook and replacement-server acceptance script | Not started |  |  |
| 139 | US-607 | Reboot and always-running verification | Not started |  |  |
| 140 | US-608 | Operations health dashboard | Not started |  |  |
| 141 | US-609 | Support bundle export | Not started |  |  |
| 142 | US-610 | Offline licensing | Not started |  |  |
| 143 | US-611 | Principal dashboard | Not started |  |  |
| 144 | US-612 | Parallel-run comparison mode and plan | Not started |  |  |
| 145 | US-613 | Daily parallel-run reconciliation | Not started |  |  |
| 146 | US-614 | Cut-over runbook and tooling | Not started |  |  |
| 147 | US-615 | Playwright end-to-end tests (4 critical flows) | Not started |  |  |
| 148 | US-616 | Capacity re-test on the finished application | Not started |  |  |
| 149 | US-617 | Security review and fixes plan | Not started |  |  |
| 150 | US-620 | Close security findings and verify fixes | Not started |  |  |
| 151 | US-618 | Super-user guide, troubleshooting guide, role manuals and training material | Not started |  |  |
| 152 | US-621 | Role-based user acceptance pack and rehearsal | Not started |  |  |
| 153 | US-622 | Release candidate manifest and operational handover | Not started |  |  |
| 154 | H-12 | Run the replacement-server acceptance test | Not started |  |  |
| 155 | US-619 | ★ Go-live readiness review (pilot gate) | Not started |  |  |
| 156 | H-13 | Install at the school and train staff | Not started |  |  |
| 157 | H-14 | Parallel run: at least one full fee cycle and one exam | Not started |  |  |
| 158 | US-701 | (Repeatable) Investigate a reconciliation difference | Not started |  |  |
| 159 | H-15 | ★ Cut-over and sign-off | Not started |  |  |
| 160 | US-702 | Post-cut-over check and retrospective | Not started |  |  |

## Decisions log

| Date | Decision | Source / approver |
|---|---|---|
| 2026-10-10 | Execution contract copied to `docs/story-protocol.md`; MUST-rule index kept in `docs/requirements-register.md` so CLAUDE.md stays under 200 lines. | US-001 |

## New stories discovered

_None yet. Record any story/task discovered during execution here, with the reason and proposed position in the tracker._
