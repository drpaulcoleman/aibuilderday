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
- `Status__c` (Picklist: Emergency Hold / Court Hearing Pending / Committed / Discharged)
- `Hold_Start__c` (DateTime — set by Flow on Blue/Pink Sheet submission)
- `Hold_Expires__c` (Formula: Hold_Start + 72 business hours)
- `Hours_Remaining__c` (Formula: business-hour-aware countdown)
- `County__c` (Picklist — for agent queries)
- `Assigned_Caseworker__c` (Lookup → User)
- `Related_Blue_Sheet__c`, `Related_Pink_Sheet__c`, `Related_White_Sheet__c` (Lookups)

### Key Fields — Blue_Sheet__c (Form 34-1)
- `Proposed_Patient_Name__c`, `Applicant_Name__c`, `Applicant_Address__c`
- `Certifying_Physician__c`, `Examination_Date__c`
- `Condition_Description__c` (Long Text)
- `LMHA__c` (Lookup)
- `Guardian_Name__c`, `Guardian_Phone__c`, `Family_Name__c`, `Family_Phone__c`

### Key Fields — Pink_Sheet__c (Form 34-2)
- `Officer_Name__c`, `Officer_Phone__c`, `Officer_Badge__c`
- `Facts_Statement__c`, `Nature_of_Danger__c`, `Observation_Summary__c` (Long Text fields)
- `LMHA__c` (Lookup)
- Same contact notification fields

### Key Fields — White_Sheet__c (Court Order)
- `Court_Case_Number__c`, `Judge_Name__c`, `Order_Date__c`
- `Commitment_Duration_Days__c` (Number: 30/60/90)
- `Renewal_Date__c` (Formula)
- `Source_System__c` (Text, default "XChange/CORIS via MuleSoft")

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
