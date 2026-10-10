# Legacy System Study Checklist (Masco School) — v2

For the two-person team. Fill `/discovery` before running the Claude Code prompt.

## 0. Before anything else
- [ ] **Named pilot contact.** One person at the school (ideally the accountant or exam controller), plus the principal's agreement, to answer questions and confirm rules. Record their names and roles.
- [ ] **Written permission** (an email is enough) from the school to study the current software and export anonymized data. Check whether the legacy vendor's contract restricts exports.
- [ ] **Short pilot understanding** (can be informal at first):
  - who confirms rules;
  - pilot period and cost (free or discounted);
  - who owns the data;
  - who the school super-user is;
  - expected support hours;
  - how either side can end the pilot.
- [ ] Settle **ownership of the product IP** between your team and the Masco group.

## 1. Anonymization rules (apply to EVERY file before it enters the repo)
Replacing names and masking phone numbers is not enough. Check every artifact.

- **Consistent synthetic identifiers.**
  - Map each real student, guardian and staff member to a fixed fake ID and fake name (e.g., `STU-0001 / Rahim Test`).
  - Use the same mapping in every file, so relationships (siblings, payments, marks) still connect.
  - Keep the mapping key **only at the school or on an encrypted drive**, never in the repo.
- **Remove or mask:**
  - names, phone numbers, birth registration numbers, NIDs, addresses, photos and signatures;
  - **QR codes and barcodes**;
  - free-text remarks;
  - email addresses;
  - bank account and transaction IDs (replace them with consistent fake ones).
- **Screenshots:** blur or crop personal data, including the window title, logged-in user and taskbar.
- **Filenames and metadata:** rename files that contain names or roll numbers, and strip EXIF/PDF metadata (author, device, location).
- **Dates:** a naive date shift can break the rules you are trying to test, because it moves:
  - weekends (Friday/Saturday) and holidays;
  - month boundaries and fee periods;
  - exam dates and session years.

  Therefore:
  - **Keep real calendar dates** for fees, fines, attendance and exams. On their own, they rarely identify a child once names and IDs are replaced.
  - Anonymize **birth dates** (synthetic, same age group) and other personal dates.
  - If a dataset is still identifiable, use only **whole-year shifts** that keep the weekday pattern (e.g., ±28 years), or build **synthetic test cases** that reproduce the calendar situation (month-end, holiday, weekend).
  - Note in the anonymization log which calendar relationships each file preserves.
- **Amounts and marks:** keep them real (they carry the rules), but watch for distinctive combinations, such as a unique scholarship amount combined with a class and section that identifies one child.
- **Review step:** a second person reviews every file before it is added. Log reviewed files in `/discovery/notes/anonymization-log.md`.
- **Never commit original data.** Not even temporarily, and not on a branch.

## 2. Folder structure
```
/discovery
  /screens        anonymized screenshots, named module_screen_##.png
  /reports        anonymized PDFs/scans of every printed report
  /exports        anonymized Excel/CSV exports
  /database       schema script / table list (no data)
  /attendance     device details, anonymized sample punch logs
  /statements     anonymized sample bank / bKash / Nagad merchant statements
  /notes          call notes, confirmations, anonymization log, observations
```

## 3. Recording rule confirmations
Use this format for every confirmed rule in `/discovery/notes/confirmations.md`:

| Rule ID | Rule (as stated) | Confirmed by (name, role) | Date | How (call/video/doc) | Applies to (classes, sessions, version, shift) | Notes / exceptions |
|---|---|---|---|---|---|---|

**Critical rules** (fees, payment verification, results, absence SMS) must be confirmed by the person responsible for that area, not just anyone at the school.

## 4. Fees & payments (top priority)
- [ ] Fee heads; fee structure per class/version/group/shift; when structures change in a year
- [ ] How monthly bills are generated, and what happens if billing is run twice
- [ ] Waivers, scholarships and discounts: types, who requests, who approves, mid-year changes
- [ ] Late fine rules, with worked examples
- [ ] How partial payments are split (which month or head first; fines first or last?)
- [ ] One payment covering several siblings — how it is handled today
- [ ] **3–5 anonymized money receipts**: normal, partial, multi-month, cancelled, cheque
- [ ] How bKash/Nagad/Rocket and bank payments are recorded and verified today; who checks the transaction ID
- [ ] **Sample merchant and bank statements** (anonymized): column formats, reference formats, whether references can repeat
- [ ] Cheque handling: when the receipt is issued; bounced-cheque procedure
- [ ] Receipt cancellation, refund and adjustment procedures; who approves
- [ ] Day closing / cash handover; what happens with shortages
- [ ] Reports in use: daily collection, due list, defaulters, monthly summary
- [ ] **Current dues per student, per fee head and per month** (for opening balances). If the legacy system only has a total per student, record that. Ask the accountant to approve a single "Legacy opening balance" head instead of inventing a split
- [ ] What happens when a student changes section or shift mid-month — is the fee changed, prorated or left as is?
- [ ] How receipt cancellations are handled today in each case: entry mistake, wrong student, money given back
- [ ] What staff do outside the software (Excel, register books) and why

## 5. Exams & results (second priority)
- [ ] Exam types per year and their weightings into the final result
- [ ] Subjects per class/group; components (CQ/MCQ/Practical/CA); full and pass marks per component
- [ ] 4th subject; combined papers (Bangla/English 1st + 2nd); religion subjects
- [ ] **How absent, exempt and missing marks are treated today**
- [ ] Rounding: of marks, totals and GPA; what happens at boundaries (e.g., 32.5, 79.5)
- [ ] Grace marks policy
- [ ] Merit position rules and tie-breaks; shared or sequential positions
- [ ] Marks entry: who enters, deadlines, locking, how corrections after publication work
- [ ] **One complete past exam export** (all marks with statuses, GPAs and positions for at least one class), plus the printed tabulation sheet for that exam — used as the golden test
- [ ] Tabulation sheet, mark sheet and merit list. Mark which elements are **required** (fields, signatures, totals) and which are just layout habit
- [ ] Known mistakes or complaints about results in the legacy system
- [ ] How long result processing takes, and where it slows down
- [ ] **Date of the next real exam** — the parallel run depends on it

## 6. Attendance (ZKTeco)
- [ ] Device model, firmware version, serial numbers, count and locations (photo of the device label and its "About" screen)
- [ ] How the current software gets punches (push/ADMS, pull/SDK, or through ZKTeco software); the software's name and version
- [ ] Whether a **spare device of the same model** can be borrowed or bought for our integration spike
- [ ] Network: are the devices on the same LAN as the server? Static IPs?
- [ ] Anonymized sample of the raw punch log, including a day when a device was offline, if possible
- [ ] Device clock accuracy — does it drift? Who sets it?
- [ ] Enrolment process for new students; reuse of device user IDs
- [ ] Shift timings, cut-off and late rules
- [ ] Is absence SMS sent today? Wording? Any false-SMS complaints?
- [ ] Is the device at the gate (arrival) or in classrooms? Is class attendance taken separately?

## 7. Students & setup
- [ ] Anonymized student export with all fields; guardian and sibling data
- [ ] Classes, sections, shifts, versions (Bangla medium / English Version), groups
- [ ] Student ID and roll numbering formats
- [ ] Section transfers, shift changes, promotion and detention — how they are recorded and whether history is kept
- [ ] Transfer Certificate procedure

## 8. Infrastructure & operations
- [ ] Server/PC specification (CPU, RAM, disk), **OS and version**, SQL Server edition and version
- [ ] Current database size, number of students and years of history
- [ ] Number of concurrent users at peak (cashiers on fee days, teachers during marks entry)
- [ ] LAN/Wi-Fi coverage, internet type, UPS/IPS, power-cut frequency and duration
- [ ] Current backup practice; where backups are kept; whether a restore has ever been tested
- [ ] Is there a place for a **disconnected backup copy** (rotated USB drives kept in the office safe, or an off-site location)? Who will rotate it?
- [ ] Who at the school can safely hold the **recovery package** (certificate file and sealed password)?
- [ ] Is spare hardware available, or how quickly can a replacement PC/server be obtained?
- [ ] Acceptable data loss and downtime, from the principal's/accountant's view (to set the RPO/RTO)
- [ ] Who would be the school super-user; their IT skill level
- [ ] SMS provider, sender ID / masking, monthly SMS volume and cost

## 9. Phone and video calls
Use Claude Code's `open-questions.md` as the script. Call each role separately:
- **Accountant / cashier:** fees, payments, verification, refunds, day closing
- **Exam controller:** result rules, statuses, rounding, ties, report requirements
- **Attendance officer:** devices, cut-off, SMS wording
- **Principal:** approvals, reports, parallel-run expectations, what would make them switch

Record answers in `/discovery/notes/`, and log every confirmed rule in the confirmations table (Section 3).
