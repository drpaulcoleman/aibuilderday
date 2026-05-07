# Guardian AI — Hackathon Build Plan

## Architecture Philosophy
Everything declarative. Custom Objects + Screen Flows + Record-Triggered Flows + Formula Fields + Agentforce. MuleSoft simulated via a Scheduled Flow that injects "court order" records. Zero Apex.

## Project Structure
All build artifacts, the Salesforce DX project, and metadata live in `./build/`. This is the force.com project root — all `sf` CLI commands run from here.

```
./build/
├── sfdx-project.json
├── force-app/
│   └── main/default/
│       ├── objects/
│       ├── flows/
│       ├── layouts/
│       ├── flexipages/
│       ├── tabs/
│       ├── applications/
│       └── permissionsets/
├── data/          ← demo seed data (JSON/CSV for sf data import)
└── scripts/       ← helper shell scripts for deployment
```

---

## Legal Requirements Impacting Build

Full statute text saved in `docs/legal-reference.md`. Key findings that change the data model and flows:

1. **72-hour clock excludes Saturdays, Sundays, and STATE holidays** — not just business hours. Need a formula or flow that accounts for this correctly. For the hackathon, a simplified formula (skip weekends) is sufficient; state holidays are a stretch goal.
2. **Patient rights — phone call within 3 hours of arrival** — need a Task/milestone on the Commitment record.
3. **Discharge instructions are mandatory** — must be documented including patient refusals. Add a `Discharge_Instructions_Status__c` field.
4. **48-hour post-discharge follow-up** — LMHA must attempt contact. Schedule a follow-up Task.
5. **Court hearing within 10 calendar days of examiner appointment** — track on White_Sheet__c.
6. **Commitment max 6 months; renewal 2 weeks before expiry** — `Renewal_Date__c` formula and renewal alerts.
7. **Two designated examiners required** — track on White_Sheet__c (Examiner_1__c, Examiner_2__c, Exam_Date_1__c, Exam_Date_2__c).
8. **Clear and convincing evidence standard** — informational only, but include in generated court summaries.
9. **Blue Sheet (34-1) requires TWO signers**: informant (front) + certifying physician (back). Pink Sheet (34-2) requires ONE signer: peace officer or mental health officer.
10. **Officer discretion NOT to commit** — if exercised, must document. Add `Officer_Discretion_Exercised__c` checkbox + `Discretion_Reason__c` on Pink Sheet.

---

## Phase 1: Data Model (Hours 0–3)

### Custom Objects

| Object | Purpose |
|--------|---------|
| `Commitment__c` | Master lifecycle record (links Blue/Pink/White) |
| `Blue_Sheet__c` | Form 34-1 (with certification) — emergency hold |
| `Pink_Sheet__c` | Form 34-2 (without certification) — officer hold |
| `White_Sheet__c` | Court commitment order |
| `LMHA__c` | Reference table of Local Mental Health Authorities |

### Key Fields — Commitment__c
- `Status__c` (Picklist: Emergency Hold / Court Hearing Pending / Committed / Voluntary / Discharged)
- `Hold_Start__c` (DateTime — set by Flow on Blue/Pink Sheet submission)
- `Hold_Expires__c` (Formula: Hold_Start + 72 hrs excluding weekends; simplified for hackathon)
- `Hours_Remaining__c` (Formula: business-hour-aware countdown)
- `County__c` (Picklist — for agent queries)
- `Assigned_Caseworker__c` (Lookup → User)
- `Patient_Name__c` (Text — denormalized for quick display)
- `Patient_DOB__c` (Date)
- `Phone_Call_Completed__c` (Checkbox — patient's 3-hour phone right)
- `Phone_Call_Deadline__c` (Formula: Hold_Start + 3 hours)
- `Discharge_Date__c` (DateTime)
- `Discharge_Instructions_Status__c` (Picklist: Pending / Provided / Refused)
- `Follow_Up_Due__c` (Formula: Discharge_Date + 48 hours)
- `Follow_Up_Completed__c` (Checkbox)
- `Related_Blue_Sheet__c`, `Related_Pink_Sheet__c`, `Related_White_Sheet__c` (Lookups)

### Key Fields — Blue_Sheet__c (Form 34-1 — With Certification)
Per statute: requires (a) informant application + (b) physician/DE certification
- `Proposed_Patient_Name__c` (Text)
- `Applicant_Name__c` (Text — the informant/responsible individual)
- `Applicant_Address__c` (Text)
- `Applicant_Relationship__c` (Text — relationship to patient)
- `Condition_Description__c` (Long Text — "circumstances which lead to belief")
- `Certifying_Physician__c` (Text — physician/PA/NP/DE who examined)
- `Certifier_Title__c` (Picklist: Physician / PA / NP / Designated Examiner)
- `Examination_Date__c` (Date — must be within 3 days preceding certification)
- `Certification_Opinion__c` (Long Text — pertinent data obtained)
- `LMHA__c` (Lookup)
- `Guardian_Name__c`, `Guardian_Phone__c`, `Guardian_Address__c`
- `Family_Name__c`, `Family_Phone__c`, `Family_Address__c`
- `Other_Contact_Name__c`, `Other_Contact_Phone__c`, `Other_Contact_Address__c`

### Key Fields — Pink_Sheet__c (Form 34-2 — Without Certification)
Per statute: peace officer or mental health officer observation + application
- `Proposed_Patient_Name__c` (Text)
- `Officer_Name__c` (Text)
- `Officer_Phone__c` (Phone)
- `Officer_Type__c` (Picklist: Peace Officer / Mental Health Officer)
- `Facts_Statement__c` (Long Text — "facts which called patient to attention")
- `Nature_of_Danger__c` (Long Text — "specific nature of the danger")
- `Observation_Summary__c` (Long Text — "summary of observations")
- `Officer_Discretion_Exercised__c` (Checkbox — per 26B-5-331(5)(b))
- `Discretion_Reason__c` (Long Text — required if discretion exercised)
- `LMHA__c` (Lookup)
- `Guardian_Name__c`, `Guardian_Phone__c`, `Guardian_Address__c`
- `Family_Name__c`, `Family_Phone__c`, `Family_Address__c`
- `Other_Contact_Name__c`, `Other_Contact_Phone__c`, `Other_Contact_Address__c`

### Key Fields — White_Sheet__c (Court Order — 26B-5-332)
- `Court_Case_Number__c` (Text)
- `Judge_Name__c` (Text)
- `Order_Date__c` (Date)
- `Commitment_Duration_Days__c` (Number: 30/60/90/180)
- `Commitment_Expiry__c` (Formula: Order_Date + Duration)
- `Renewal_Date__c` (Formula: Expiry - 14 days — per statute, court notifies 2 weeks before)
- `Examiner_1_Name__c` (Text)
- `Examiner_1_Date__c` (Date)
- `Examiner_2_Name__c` (Text)
- `Examiner_2_Date__c` (Date)
- `Hearing_Date__c` (Date — must be within 10 calendar days of examiner appointment)
- `Hearing_Outcome__c` (Picklist: Committed / Dismissed / Assisted Outpatient)
- `Commitment_Type__c` (Picklist: Initial / Renewal / Indeterminate)
- `Source_System__c` (Text, default "XChange/CORIS via MuleSoft")
- `County__c` (Text — county where patient resides or is found)

---

## Phase 2: Flows — The Engine (Hours 3–8)

| Flow | Type | Purpose |
|------|------|---------|
| **Blue Sheet Filing** | Screen Flow | Officer/ER form entry → creates Blue_Sheet__c + Commitment__c, stamps Hold_Start |
| **Pink Sheet Filing** | Screen Flow | Peace officer form entry → same pattern |
| **72-Hour Clock Start** | Record-Triggered (Commitment) | On create: sets Hold_Expires, creates milestone Tasks |
| **Escalation Alerts** | Scheduled Flow | Runs every hour; queries commitments where Hours_Remaining < 24/12/4 → sends notifications |
| **Court Order Received** | Record-Triggered (White_Sheet) | Updates Commitment status → "Committed", stops clock, sets renewal date |
| **Mock MuleSoft Inbound** | Scheduled Flow (or Button) | Creates a White_Sheet__c record as if it came from XChange/CORIS — **this is our MuleSoft simulation** |

---

## Phase 3: Lightning App + Dashboard (Hours 8–11)

### Lightning App: "Guardian AI"
- Home page with dashboard component
- Record pages for Commitment, Blue Sheet, Pink Sheet

### Dashboard Components
- **Active Holds Countdown** — table sorted by Hours_Remaining ascending (most urgent first)
- **Commitments by Status** — donut chart
- **Commitments by County** — bar chart
- **Expiring in Next 24 Hours** — filtered report, red highlight
- **Recent Court Orders Received** — list from White_Sheet__c

### Record Page UX (Commitment__c)
- Hero component: big countdown number (formula field displayed via dynamic form)
- Progress path: Emergency Hold → Court Hearing → Committed → Discharged
- Related lists: Blue/Pink/White Sheets, Activity Timeline

---

## Phase 4: Agentforce — Guardian AI Agent (Hours 11–16)

### Agent Configuration
- Name: "Guardian AI"
- Channel: Embedded in Lightning (Copilot sidebar)

### Topics + Actions

| Topic | Sample Utterances | Action |
|-------|-------------------|--------|
| Expiring Holds | "What Blue Sheets are expiring today?", "Show holds expiring in 6 hours" | Flow Action: query Commitment__c where Hours_Remaining < X, return formatted list |
| Case Summary | "Draft a court hearing summary for Case #001" | Prompt Template: pulls Commitment + Blue/Pink Sheet fields, generates structured summary |
| Status Check | "What's the status of [patient name]?" | Flow Action: SOQL-like query, return record details |
| Escalation | "What cases need immediate attention?" | Flow Action: query where Status = 'Emergency Hold' AND Hours_Remaining < 12 |

### Prompt Templates (grounded in data)
- Court Hearing Summary template
- Discharge Checklist template
- Proactive Alert summary template

---

## Phase 5: Demo Data + Polish (Hours 16–20)

### Seed Data
- 5 LMHA records (Salt Lake, Utah County, Davis, Weber, Washington)
- 8–10 Commitment records at various lifecycle stages
- 2–3 records with expiry in next 6 hours (for live demo)
- 1 record at 68 hours (for escalation showcase)
- 2 White Sheets already received
- 1 White Sheet pending (for the "MuleSoft delivers it live" moment)

---

## Phase 6: Vaporware / Presentation Assets (Hours 20–24)

### Simulated but not real
- MuleSoft architecture diagram (slide only — show Anypoint as the hub)
- Experience Cloud officer portal (screenshot mockup or a single Community page if time allows)
- Mobile push notification (show a screenshot/mockup)
- DPS integration (architecture slide only)

### Presentation materials
- Architecture diagram (Salesforce + MuleSoft + Court System + DPS + LMHAs)
- Before/After workflow comparison
- 4-minute talk track aligned to scoping doc Section 4

---

## Demo Script (4 Minutes)

| Time | Beat | What's Shown |
|------|------|--------------|
| 0:00–0:30 | Hook | "It's 2 AM in Provo..." story + architecture slide |
| 0:30–1:30 | File a Blue Sheet | Run Screen Flow live, show clock start, record page with countdown |
| 1:30–2:15 | Guardian AI queries | Ask agent: "What holds expire in the next 6 hours?" → live response |
| 2:15–2:45 | Court order arrives | Trigger mock MuleSoft flow → White Sheet created → clock stops → notification |
| 2:45–3:15 | AI drafts summary | Ask agent: "Draft court hearing summary for [case]" → generated doc |
| 3:15–3:45 | Dashboard | Show statewide view, escalation indicators |
| 3:45–4:00 | Close | "HB 137 deadlines are coming. Guardian AI is ready." |

---

## What We're NOT Building (Conscious Cuts)

- No Apex triggers or classes
- No actual MuleSoft flows (simulated via SF Flows)
- No Experience Cloud portal (mockup only)
- No Shield/encryption config (mention in pitch)
- No real CJIS SSO (irrelevant for demo)
- No mobile app (show responsive Lightning page on phone if asked)

---

## Execution Order / Critical Path

```
1. sf project generate -n build (create SFDX project in ./build/)
2. sf org open -o aibuilder (verify access)
3. Create objects + fields as metadata in ./build/force-app/main/default/objects/
4. sf project deploy start -o aibuilder (push data model)
5. Build Screen Flows (Blue Sheet, Pink Sheet) — metadata in ./build/force-app/main/default/flows/
6. Build Record-Triggered Flows (clock start, escalation)
7. Build Lightning App + Record Pages — metadata in ./build/force-app/main/default/flexipages/
8. Configure Agentforce agent + topics + actions
9. Load demo data from ./build/data/ via sf data import tree
10. Build dashboard
11. Rehearse demo script
12. Build presentation slides (architecture, before/after)
```
