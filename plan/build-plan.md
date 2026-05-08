# Guardian AI — Hackathon Build Plan

## Challenge Context

From the Utah Builder Day bounty brief:
- **Open challenge** — teams can use any stack; Salesforce mentorship available if chosen
- **"Vibe coding" encouraged** — use AI coding tools aggressively to move fast
- **Demo emphasis**: usable mobile interface, live countdown, autonomous monitoring, field-to-court workflow
- **4-minute demo window** — every second matters
- **Salesforce compatibility**: even custom/vibe-coded apps can hook into SF for operability, observability, scalability, and Shield security
- **Our team's choice**: Salesforce-native (declarative-first) with AI-assisted metadata generation

## Architecture Philosophy

Everything declarative. Custom Objects + Screen Flows + Record-Triggered Flows + Formula Fields + Agentforce. MuleSoft simulated via a Scheduled Flow that injects "court order" records. Zero Apex.

### What Does "Declarative" Mean?

If you're new to Salesforce: "declarative" means we configure the platform using point-and-click tools and XML metadata files rather than writing traditional code. Salesforce provides a visual builder for almost everything — database tables (Objects), forms and automation (Flows), UI layouts (Lightning App Builder), and AI agents (Agentforce). The platform generates the underlying infrastructure for us.

This matters for the hackathon because:
- It's faster than writing code from scratch
- Multiple people with different skill levels can contribute
- Changes are instantly live — no compile/build/deploy cycle (unless working from source)
- AI tools (Claude, Copilot) can generate the XML metadata files directly

### Key Salesforce Concepts for Newcomers

| Concept | What It Is | Analogy |
|---------|-----------|---------|
| **Custom Object** | A database table | Like a SQL table with columns (fields) |
| **Record** | A row in that table | A single Blue Sheet filing, one patient commitment |
| **Flow** | Visual automation builder | Like a flowchart that executes — if/then logic, create records, send alerts |
| **Screen Flow** | A Flow that shows UI to a user | A multi-step form wizard |
| **Record-Triggered Flow** | A Flow that runs when data changes | Like a database trigger, but visual |
| **Lightning App** | A container for pages/tabs | Like a single-page app with navigation |
| **FlexiPage** | A drag-and-drop page layout | The UI a user sees for a record or home page |
| **Agentforce** | AI agent framework | An LLM that can query your data and take actions |
| **SFDX / SF CLI** | Command-line tools for Salesforce | Like `git` but for Salesforce metadata |
| **Metadata** | XML files describing your config | The "source code" of your Salesforce setup |

---

## Team Roles & Responsibilities

### Role Assignments

| Role | Responsibility | Primary Deliverables | Ideal For |
|------|---------------|---------------------|-----------|
| **Data Architect** | Objects, fields, relationships, formulas, validation rules | Phase 1 metadata, permission sets | Someone comfortable with data modeling or databases |
| **Flow Builder** | Screen Flows (forms), Record-Triggered Flows, Scheduled Flows | Phase 2 automation | Someone who thinks in process/logic; visual builder experience helps |
| **UX / Lightning Builder** | App, record pages, dashboard, reports | Phase 3 Lightning experience | Someone with design sense; Salesforce experience a plus but not required |
| **AI / Agentforce Config** | Agent topics, actions, prompt templates, Data Cloud grounding | Phase 4 agent setup | Someone who understands LLMs and prompt engineering |
| **Documentarian / Storyteller** | Architecture diagrams, presentation slides, before/after workflows, pitch narrative, demo runbook | Phases 5–6 assets, pitch script | Strong communicator/writer; understands the "why" |
| **Demo Lead / Presenter** | Seed data, demo rehearsal, live presentation, timing, backup plans | Final demo execution | Confident public speaker; calm under pressure |

### What Each Role Actually Does (Hour by Hour)

**Data Architect** — You're building the "database schema" that everything else depends on. You'll create XML files that define Custom Objects and their fields, then deploy them to the shared Salesforce org. Everyone is blocked until your work is done, so you start first. Think of yourself as laying the foundation of a house.

**Flow Builder** — You're the automation engine. Once the Data Architect has objects deployed, you build the interactive forms (Screen Flows) that end users fill out, and the behind-the-scenes automation (Record-Triggered Flows) that fires when records are created or updated. You'll work in the Salesforce Flow Builder UI (a visual drag-and-drop canvas) and then retrieve your work as XML.

**UX / Lightning Builder** — You make it look good and feel usable. You arrange components on pages, build dashboards with charts, and ensure the demo tells a visual story. You work in the Lightning App Builder (drag-and-drop in the browser) and need the objects and flows to exist first so you can reference them.

**AI / Agentforce Config** — You configure the AI agent that monitors commitments and answers questions. You define "topics" (categories of questions the agent handles), wire up "actions" (flows or queries the agent can execute), and write "prompt templates" (instructions that ground the AI's responses in real data). This is mostly done in Salesforce Setup UI, not code.

**Documentarian / Storyteller** — You are the team's narrative architect. While builders are heads-down configuring, you are crafting the story that wins the judges. You create the architecture diagram, write the pitch script, build the before/after workflow visuals, and ensure the 4-minute demo has a compelling arc. You also document setup steps so the work is reproducible. You don't need Salesforce expertise — you need to understand the problem deeply and communicate it powerfully.

**Demo Lead / Presenter** — You own the final 4 minutes. You rehearse the live demo repeatedly, know every click by muscle memory, have fallback plans if something fails live, and manage timing ruthlessly. You load the seed data that sets up the perfect demo scenario. You may be the same person as the Documentarian, or a different person who is the strongest presenter on the team.

### Role Overlap Rules
- Any team member can seed data or fix fields — these are low-conflict
- Flows and Agentforce config are high-conflict in the org — only one person at a time
- Lightning pages can be built in parallel (different pages) but deploy carefully
- The Demo Lead owns the "golden path" — everyone tests against their scenarios
- The Documentarian works independently of the org — no conflicts with builders

### Solo / Small Team Guidance
- **1 person**: Follow phases sequentially; prioritize Blue Sheet flow + countdown + 1 Agentforce query
- **2 people**: Split Data+Flows (Person A) vs Lightning+Agent+Demo (Person B); both share documentation
- **3 people**: Data Architect | Flow+Agent Builder | UX+Demo+Docs
- **4 people**: Data Architect | Flow Builder | UX+Agent | Demo+Docs
- **5+ people**: Full role split; Documentarian and Demo Lead are separate people

---

## Org & Sandbox Strategy

### What Is a Salesforce Org?

A Salesforce "org" (organization) is a single instance of the Salesforce platform — your own private cloud environment with its own database, users, and configuration. Think of it like a fully provisioned cloud application that you configure rather than code. Our org is a trial instance, which means it's free, fully featured, but expires after 30 days.

### Current Org
- **Alias**: `aibuilder` (a nickname we use in CLI commands to refer to this org)
- **Type**: Enterprise Edition trial (expires 2026-05-26)
- **Instance**: SDB6 (standard test instance, NOT Gov Cloud — Gov Cloud is pitch-only)
- **URL**: `https://orgfarm-23ada909fc.test1.my.pc-rnd.salesforce.com`

### Development Strategy: Single Shared Org

For a 24-hour hackathon with a trial org, **everyone works in the same org**. No scratch orgs, no sandboxes. Rationale:
- Trial orgs can't spawn sandboxes
- Scratch org provisioning wastes precious hackathon hours
- Agentforce configuration is org-specific and hard to move via metadata
- The org expires in ~19 days anyway — it's disposable

**What this means practically**: when you make a change in the Salesforce UI, everyone sees it immediately. There's no "my copy" vs "your copy." This is why role ownership and communication matter — if two people edit the same Flow simultaneously, one person's changes will overwrite the other's.

### Conflict Mitigation
| Risk | Mitigation |
|------|-----------|
| Two people editing the same Flow | **Assign flows by name** — each person owns specific flows (see Phase 2 table) |
| Metadata drift between git and org | **Deploy-from-source is authoritative** — always `sf project deploy` from git, never rely solely on Setup UI changes |
| Field/object changes conflict | **Data Architect deploys objects first** — others wait until Phase 1 complete before building flows |
| Agentforce config not in metadata | **Agent config is manual in Setup** — document steps in `./build/scripts/agentforce-setup.md` for reproducibility |
| Someone breaks demo data | **Seed data is idempotent** — re-runnable import script in `./build/scripts/seed-data.sh` |

### Auth: Everyone Authenticates Independently

Each team member needs their own CLI session pointing to the shared org:

```bash
# This opens a browser window to log in. Use the same credentials everyone shares.
sf org login web -a aibuilder -r https://orgfarm-23ada909fc.test1.my.pc-rnd.salesforce.com
```

After login, the CLI stores a token locally. You won't need to log in again unless the token expires (usually 24+ hours). The `-a aibuilder` flag sets the alias so all future commands can use `-o aibuilder` to target this org.

---

## Git Collaboration Strategy

### If You're New to Git

Git is a version control system that tracks changes to files. For this hackathon, we use it to:
1. Keep a history of every change (so we can undo mistakes)
2. Let multiple people work simultaneously without overwriting each other
3. Store our Salesforce metadata (the XML files that define our objects, flows, pages)

Key commands you'll use:
```bash
git status              # What files have I changed?
git add <file>          # Stage a file for commit
git commit -m "message" # Save my staged changes with a description
git push                # Upload my commits to the shared repository
git pull                # Download others' commits to my machine
git checkout -b <name>  # Create a new branch (your own workspace)
git checkout main       # Switch back to the main branch
```

### Branch Model (Trunk-Based for Speed)

A "branch" is a parallel copy of the codebase where you can make changes without affecting others. When you're done, you "merge" your branch back into `main`.

```
main                    ← always deployable, source of truth
├── feat/data-model     ← Data Architect works here
├── feat/flows          ← Flow Builder works here
├── feat/lightning-ux   ← UX Builder works here
├── feat/agentforce     ← AI Config works here (mostly docs, some metadata)
├── feat/demo-data      ← Demo Lead works here
└── feat/docs           ← Documentarian works here
```

**Rules:**
1. `main` is protected — merge via PR (even if self-approved, keeps history clean)
2. Feature branches are short-lived — merge into main as soon as a phase completes
3. **Deploy sequence matters**: data-model merges first, then flows, then lightning, then agent
4. Conflicts in metadata XML are resolved by the person who owns that component
5. Never force-push

### How to Create a PR (Pull Request)

A PR is how you propose merging your branch into main. Even in a hackathon, it provides a checkpoint:

```bash
# Push your branch to the remote repository
git push -u origin feat/data-model

# Create a PR (if using GitHub CLI)
gh pr create --title "Phase 1: Data model objects and fields" --body "Adds Commitment, Blue/Pink/White Sheet, and LMHA objects"

# Or just use the GitHub web UI — it'll prompt you when you push a branch
```

For hackathon speed: PRs can be self-approved and merged immediately. The point is history, not gatekeeping.

### Commit Conventions

Prefix your commit messages so the team can scan history quickly:

```
feat: add Commitment__c object and fields
flow: Blue Sheet filing screen flow
ux: commitment record page with countdown
agent: add expiring-holds topic and action
data: seed 10 commitment records for demo
docs: architecture diagram and pitch script
fix: correct Hold_Expires formula for weekends
```

### Deploy Workflow

After merging to main, deploy to the shared org:

```bash
# Get the latest from main
git checkout main && git pull

# Deploy all metadata to the org
sf project deploy start -o aibuilder -d ./build/force-app

# Open the org in a browser to verify
sf org open -o aibuilder
```

### What Lives in Git vs. What's Org-Only

| In Git (./build/) | Org-Only (manual Setup) |
|-------------------|------------------------|
| Objects, fields, validation rules | Agentforce agent configuration |
| Flows (Screen, Record-Triggered, Scheduled) | Agentforce prompt templates |
| Lightning App, FlexiPages | Dashboard (can be retrieved but fragile) |
| Tabs, Permission Sets | Reports (can be retrieved but fragile) |
| Layouts | Einstein/Data Cloud config |
| Static resources | Custom notifications setup |

**Why some things are "org-only"**: Certain Salesforce features (especially newer AI features like Agentforce) don't fully support metadata deployment. They must be configured through the browser UI. We document these steps in markdown files so they're reproducible if the org is ever recreated.

---

## Project Structure

All build artifacts, the Salesforce DX project, and metadata live in `./build/`. This is the force.com project root — all `sf` CLI commands run from here.

```
./build/
├── sfdx-project.json        ← Project config (tells CLI where metadata lives)
├── force-app/
│   └── main/default/
│       ├── objects/          ← Data Architect owns (Custom Object XML)
│       ├── flows/            ← Flow Builder owns (Flow XML)
│       ├── layouts/          ← UX Builder owns (Page Layout XML)
│       ├── flexipages/       ← UX Builder owns (Lightning Page XML)
│       ├── tabs/             ← Data Architect owns (Tab XML)
│       ├── applications/     ← UX Builder owns (App XML)
│       └── permissionsets/   ← Data Architect owns (Permission Set XML)
├── data/                     ← Demo Lead owns (JSON/CSV for sf data import)
├── scripts/
│   ├── deploy.sh             ← Full deploy to org (one command does everything)
│   ├── seed-data.sh          ← Idempotent demo data load
│   └── agentforce-setup.md   ← Manual agent config steps (reproducible)
└── docs/
    ├── demo-runbook.md       ← Step-by-step demo execution guide
    ├── architecture.md       ← System architecture description
    └── pitch-script.md       ← 4-minute presentation script with timing marks
```

### What Are These XML Files?

When you create a Custom Object in the Salesforce UI, Salesforce stores it internally. The SF CLI can "retrieve" that configuration as XML files. These XML files ARE the object definition — field names, data types, picklist values, formulas, everything.

Example: a simple text field looks like this in XML:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<CustomField xmlns="http://soap.sforce.com/2006/04/metadata">
    <fullName>Patient_Name__c</fullName>
    <label>Patient Name</label>
    <type>Text</type>
    <length>255</length>
    <required>true</required>
</CustomField>
```

You can write these by hand (or have AI generate them), then deploy them to the org. Or you can build in the UI and retrieve them. Both approaches work.

---

## Source Tracking & Org Sync

### What Is Source Tracking?

Source tracking is a feature where Salesforce remembers what you've deployed and what's changed since. It answers: "what's different between my local files and what's in the org?" This is critical when multiple people share an org — it's how you pull down changes others made in the UI.

### Enable Source Tracking
The `aibuilder` org supports source tracking (non-sandbox EE trial with API access). Use it to stay in sync when team members make changes in Setup UI:

```bash
# Track what changed in the org since last deploy:
sf project retrieve start -o aibuilder --ignore-conflicts

# See what's changed locally vs org:
sf project deploy preview -o aibuilder

# After someone makes manual changes in org (e.g., Agentforce, dashboards):
sf project retrieve start -o aibuilder -m "Flow:Blue_Sheet_Filing"

# Retrieve a specific object's changes:
sf project retrieve start -o aibuilder -m "CustomObject:Commitment__c"
```

### Source Tracking Rules for the Team
1. **Before starting work**: `git pull` + `sf project retrieve start` to catch org-side changes
2. **After making Setup UI changes**: retrieve immediately and commit to your branch
3. **Flows built in Setup UI**: retrieve the flow XML immediately → commit → push
4. **Conflicts**: if `sf project deploy` fails with conflicts, the component owner resolves
5. **Nuclear option**: if source tracking gets hopelessly confused, `sf project deploy start --ignore-conflicts` (Data Architect decision only)

### Daily Rhythm (for each team member)
```bash
# START OF WORK SESSION:
git checkout main && git pull           # Get latest from team
git checkout feat/my-branch             # Switch to my branch
git merge main                          # Incorporate team changes
sf project retrieve start -o aibuilder  # Get any UI changes from org

# ... do your work ...

# END OF WORK SESSION:
sf project retrieve start -o aibuilder  # Capture anything you did in UI
git add . && git commit -m "..."        # Commit locally
git push                                # Push to remote
# Create PR when phase is complete
```

### What to Track vs. Ignore
Add to `.forceignore` in `./build/`:
```
# Profiles are noisy and org-specific
**/profiles/**

# Admin and standard users
**/userPermissions/**

# Org-specific settings that vary
**/settings/**

# AI/Agent config that doesn't serialize cleanly (document manually instead)
**/aiModels/**
**/aiApplications/**
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

### What You're Building

The data model is the foundation — it defines what information the system stores and how records relate to each other. In Salesforce terms, you're creating Custom Objects (tables) with Custom Fields (columns). Everything else — forms, automation, dashboards, AI — depends on this being right.

### How To: Create Objects via Metadata

**Option A — Write XML directly (fastest with AI assistance):**
```bash
# Create directory structure for an object
mkdir -p ./build/force-app/main/default/objects/Commitment__c/fields

# Use Claude/AI to generate the field XML files, then deploy:
sf project deploy start -o aibuilder -d ./build/force-app/main/default/objects
```

**Option B — Create in Salesforce UI, then retrieve:**
1. Open the org: `sf org open -o aibuilder`
2. Go to Setup → Object Manager → Create → Custom Object
3. Add fields through the UI
4. Retrieve: `sf project retrieve start -o aibuilder -m "CustomObject:Commitment__c"`
5. Commit the resulting XML to git

**Option A is preferred** because it's reproducible and version-controlled from the start. But Option B is fine if you're more comfortable in the UI.

### Custom Objects

| Object | Purpose | Real-World Analog |
|--------|---------|-------------------|
| `Commitment__c` | Master lifecycle record (links Blue/Pink/White) | A patient's entire commitment journey |
| `Blue_Sheet__c` | Form 34-1 (with certification) — emergency hold | The physical blue paper form, digitized |
| `Pink_Sheet__c` | Form 34-2 (without certification) — officer hold | The physical pink paper form, digitized |
| `White_Sheet__c` | Court commitment order | The judge's signed order |
| `LMHA__c` | Reference table of Local Mental Health Authorities | A lookup list of facilities |

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

### What You're Building

Flows are Salesforce's visual automation tool. Think of them as flowcharts that actually execute. There are three types we need:

1. **Screen Flows** — interactive forms that guide a user through data entry (our Blue Sheet and Pink Sheet filing forms)
2. **Record-Triggered Flows** — automation that fires automatically when a record is created or updated (start the 72-hour clock, process a court order)
3. **Scheduled Flows** — automation that runs on a timer (check for expiring holds every hour)

### How To: Build a Flow

**In the Salesforce UI (recommended for newcomers):**
1. Open org: `sf org open -o aibuilder`
2. Navigate to Setup → Flows → New Flow
3. Choose flow type (Screen, Record-Triggered, or Schedule-Triggered)
4. Use the visual canvas to add elements: screens, decisions, record creates/updates, assignments
5. Save and Activate the flow
6. Retrieve to git: `sf project retrieve start -o aibuilder -m "Flow:Blue_Sheet_Filing"`

**Key tips for the Flow Builder UI:**
- "Get Records" = query the database
- "Create Records" = insert a new record
- "Update Records" = modify existing records
- "Decision" = if/then branching
- "Assignment" = set variable values
- "Screen" = show a form to the user
- Always test with "Debug" button before activating

### Flow Inventory

| Flow | Type | Owner | Purpose |
|------|------|-------|---------|
| **Blue_Sheet_Filing** | Screen Flow | Flow Builder | Officer/ER form entry → creates Blue_Sheet__c + Commitment__c, stamps Hold_Start |
| **Pink_Sheet_Filing** | Screen Flow | Flow Builder | Peace officer form entry → same pattern |
| **Commitment_Clock_Start** | Record-Triggered (Commitment) | Flow Builder | On create: sets Hold_Expires, creates milestone Tasks |
| **Escalation_Alerts** | Scheduled Flow | Flow Builder | Runs every hour; queries commitments where Hours_Remaining < 24/12/4 → sends notifications |
| **Court_Order_Received** | Record-Triggered (White_Sheet) | Flow Builder | Updates Commitment status → "Committed", stops clock, sets renewal date |
| **Mock_MuleSoft_Inbound** | Autolaunched (triggered by button) | Flow Builder | Creates a White_Sheet__c record as if it came from XChange/CORIS — **this is our MuleSoft simulation** |

### How the MuleSoft Simulation Works

In production, MuleSoft would connect to the Utah Court System (XChange/CORIS) and push court orders into Salesforce via API. We can't build that in 24 hours (and don't have access to the court system). Instead:

1. We create a button labeled "Simulate Court Order Received" on the Commitment record page
2. Clicking it runs an Autolaunched Flow that creates a White_Sheet__c record with realistic data
3. The Record-Triggered Flow on White_Sheet__c fires, updating the Commitment status and stopping the clock
4. To the audience watching the demo, it looks like: "The court order just arrived via MuleSoft!" → status changes, clock stops, notification appears

This is the one piece of "vaporware" in the live demo — but it demonstrates the exact behavior the real system would have.

---

## Phase 3: Lightning App + Dashboard (Hours 8–11)

### What You're Building

The Lightning App is the user interface — what caseworkers, supervisors, and administrators see when they log in. It's built using the Lightning App Builder (drag-and-drop in the browser). You're assembling pre-built components (charts, lists, fields, related records) into pages.

### How To: Build a Lightning Page

1. Open org: `sf org open -o aibuilder`
2. Navigate to Setup → Lightning App Builder → New
3. Choose page type: "Record Page" for Commitment__c, "Home Page" for the dashboard view
4. Drag components onto the canvas: fields, charts, related lists, rich text
5. Save → Activate → assign to the Guardian AI app
6. Retrieve: `sf project retrieve start -o aibuilder -m "FlexiPage:Commitment_Record_Page"`

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

### Making It Look Good for Demo

Tips for the UX Builder:
- Use **Dynamic Forms** (break the record detail into individual field sections you can arrange)
- The countdown formula field should be in a **large rich text** component at the top
- Use **conditional visibility** to show red/yellow/green indicators based on Hours_Remaining
- Put the Agentforce chat panel on the right sidebar of record pages
- Use a **Path** component for the commitment lifecycle stages (it shows a visual progress bar)

---

## Phase 4: Agentforce — Guardian AI Agent (Hours 11–16)

### What You're Building

Agentforce is Salesforce's AI agent framework. You're configuring an LLM-powered assistant that can:
- Answer natural language questions about commitment data
- Proactively surface urgent cases
- Generate documents (court summaries, discharge checklists)
- Take actions (create tasks, send alerts)

The agent doesn't write code — it uses "Topics" (categories of capability) and "Actions" (specific things it can do, usually backed by Flows or SOQL queries).

### How To: Configure an Agentforce Agent

1. Open org: `sf org open -o aibuilder`
2. Navigate to Setup → Agents (or Einstein Copilot → Agent Builder)
3. Create a new Agent named "Guardian AI"
4. Add Topics (categories the agent handles)
5. For each Topic, add Actions (flows it can call, queries it can run)
6. Write Prompt Templates (instructions that tell the LLM how to format responses)
7. Test in the Agent Builder preview panel
8. Activate and deploy to the Lightning sidebar

**This is NOT retrievable as clean metadata** — document every step in `./build/scripts/agentforce-setup.md` so it's reproducible.

### Agent Configuration
- Name: "Guardian AI"
- Channel: Embedded in Lightning (Copilot sidebar)
- Persona instruction: "You are Guardian AI, a civil commitment monitoring assistant for Utah DHHS. You monitor emergency holds, track legal deadlines, and help caseworkers protect patient rights. Always cite specific case numbers and remaining time."

### Topics + Actions

| Topic | Sample Utterances | Action |
|-------|-------------------|--------|
| Expiring Holds | "What Blue Sheets are expiring today?", "Show holds expiring in 6 hours" | Flow Action: query Commitment__c where Hours_Remaining < X, return formatted list |
| Case Summary | "Draft a court hearing summary for Case #001" | Prompt Template: pulls Commitment + Blue/Pink Sheet fields, generates structured summary |
| Status Check | "What's the status of [patient name]?" | Flow Action: SOQL-like query, return record details |
| Escalation | "What cases need immediate attention?" | Flow Action: query where Status = 'Emergency Hold' AND Hours_Remaining < 12 |

### Prompt Templates (grounded in data)
- **Court Hearing Summary** — pulls patient info, Blue/Pink Sheet details, LMHA, timeline, and formats as a court-ready document
- **Discharge Checklist** — generates the statutory discharge instruction requirements as a checklist
- **Proactive Alert** — summarizes why a case needs attention (time remaining, missing court order, etc.)

### Tips for the AI/Agentforce Config Person
- Start with ONE topic that works perfectly (Expiring Holds) before adding more
- Test extensively in the preview panel — the agent can hallucinate if grounding is wrong
- Keep prompt templates short and structured — bullet points, not paragraphs
- The agent needs the Flow Actions to be active before you can wire them up
- If the agent seems confused, check that field API names in your prompt match the actual object fields

---

## Phase 5: Demo Data + Polish (Hours 16–20)

### What You're Building

Seed data creates the "world" the demo lives in. Without it, the org is empty and nothing works. Good demo data tells a story — the audience should see urgency, variety, and realistic scenarios.

### How To: Load Seed Data

**Option A — Data Import via CLI (recommended):**
```bash
# Create a plan file that describes the import order:
sf data import tree -o aibuilder -f ./build/data/plan.json
```

The `plan.json` file references individual JSON files for each object:
```json
[
  { "sobject": "LMHA__c", "files": ["lmha-data.json"] },
  { "sobject": "Commitment__c", "files": ["commitment-data.json"] },
  { "sobject": "Blue_Sheet__c", "files": ["blue-sheet-data.json"] }
]
```

**Option B — Create manually in the org:**
Just open the org and create records by hand. Faster for small quantities but not reproducible.

### Seed Data Plan
- 5 LMHA records (Salt Lake, Utah County, Davis, Weber, Washington)
- 8–10 Commitment records at various lifecycle stages
- 2–3 records with expiry in next 6 hours (for live demo — **adjust timestamps at demo time!**)
- 1 record at 68 hours (for escalation showcase)
- 2 White Sheets already received
- 1 White Sheet pending (for the "MuleSoft delivers it live" moment)

### Critical Demo Data Timing

The 72-hour countdown is relative to NOW. Seed data timestamps must be recalculated before the live demo so the countdown shows realistic, urgent numbers. The Demo Lead should:
1. Run `./build/scripts/seed-data.sh` **30 minutes before the demo**
2. The script should calculate `Hold_Start` values relative to current time (e.g., NOW - 66 hours = "4 hours remaining")
3. Verify the countdown displays correctly in the org

---

## Phase 6: Documentation & Presentation Assets (Hours 20–24)

### Documentarian Deliverables

The Documentarian works throughout the hackathon but their output peaks in the final hours. They are responsible for:

#### 1. Architecture Diagram
A visual showing how all systems connect. For the hackathon, this is a single slide showing:
- Salesforce (center) with objects and Agentforce
- MuleSoft (bridge) connecting external systems
- Utah Court System (XChange/CORIS) — external
- Dept. of Public Safety (DPS) — external
- LMHA EHR systems — external
- Mobile devices (officer field access)

Tools: draw.io, Miro, Figma, or even a clean hand-drawn diagram photographed.

#### 2. Before/After Workflow
Two diagrams side by side:
- **Before**: paper forms → manual entry → spreadsheet tracking → missed deadlines → liability
- **After**: digital filing → instant clock start → AI monitoring → proactive alerts → compliance

#### 3. Pitch Script (4 minutes, timed)
Written word-for-word with timing marks. The Demo Lead will rehearse from this. See "Demo Script" section below.

#### 4. Setup Documentation
`./build/scripts/agentforce-setup.md` — step-by-step screenshots/instructions for Agentforce configuration. This ensures the work is reproducible if the org dies or needs recreation.

#### 5. Demo Runbook
`./build/docs/demo-runbook.md` — the exact click path for the live demo. Every URL, every field value, every agent prompt, written out. The Demo Lead follows this like a script.

### What's Simulated (Vaporware) vs. Real

Judges will evaluate vision + feasibility + demo quality. They understand not everything is production-ready. Be transparent:

| Component | Status | What Judges See |
|-----------|--------|-----------------|
| Blue/Pink Sheet filing flow | **REAL** — works live | Live form submission, record creation |
| 72-hour countdown | **REAL** — formula field | Live countdown updating |
| Escalation alerts | **REAL** — scheduled flow | Show flow config, explain schedule |
| Guardian AI agent queries | **REAL** — Agentforce | Live conversation with agent |
| Court order from MuleSoft | **SIMULATED** — button triggers flow | "Court order just arrived!" moment |
| Experience Cloud officer portal | **MOCKUP** — screenshot/slide only | Architecture diagram |
| DPS integration | **SLIDE ONLY** — architecture | Architecture diagram |
| Gov Cloud / Shield / CJIS | **PITCH ONLY** — no config needed | Mentioned in closing |

---

## Demo Lead Responsibilities

### Before Demo Day
1. **Own the seed data** — load it fresh 30 minutes before presenting
2. **Write the demo runbook** — exact URLs, clicks, and talking points
3. **Rehearse 5+ times** — with timer, out loud, standing up
4. **Prepare backup plans**:
   - If a flow errors: have a pre-completed record ready to show
   - If the agent hallucinates: have a screenshot of a good response
   - If the org is slow: have a video recording of the demo as backup
5. **Time ruthlessly** — 4 minutes is HARD. Cut anything that runs long.

### Demo Script (4 Minutes)

| Time | Beat | What's Shown | Presenter Notes |
|------|------|--------------|-----------------|
| 0:00–0:30 | Hook | Architecture slide | "It's 2 AM in Provo..." story. Don't open Salesforce yet. |
| 0:30–1:30 | File a Blue Sheet | Run Screen Flow live | Show mobile-responsive form. Submit. Show record page with countdown starting. |
| 1:30–2:15 | Guardian AI queries | Agent panel on record page | Type: "What holds expire in the next 6 hours?" Show live response with case list. |
| 2:15–2:45 | Court order arrives | Click "Simulate Court Order" button | White Sheet created → clock stops → status changes → notification appears. "MuleSoft just delivered this from XChange/CORIS." |
| 2:45–3:15 | AI drafts summary | Agent panel | Type: "Draft court hearing summary for this case" → generated document appears. |
| 3:15–3:45 | Dashboard | Switch to dashboard tab | Show statewide view: donut chart, county breakdown, urgent cases list. |
| 3:45–4:00 | Close | Back to architecture slide | "HB 137 deadlines are 2025/2026. Guardian AI is ready. Questions?" |

### Timing Tips
- The hook is spoken over a static slide — no clicking, no waiting for page loads
- Pre-load all tabs before presenting (org home, flow page, record page, dashboard)
- If any step takes >5 seconds to load, narrate while it loads — never stand in silence
- Have the agent prompt pre-typed in a text file — paste it rather than typing live
- End 10 seconds early rather than 10 seconds late

### Backup Plans

| Failure | Backup |
|---------|--------|
| Org won't load | Show pre-recorded video (record a perfect run the night before) |
| Flow errors on submit | Switch to a pre-existing record: "Here's one we filed earlier..." |
| Agent gives bad response | Show screenshot: "Here's what Guardian AI generated for this case..." |
| Dashboard is empty | Pre-load with extra seed data; show screenshot if needed |
| Timer runs long | Cut the dashboard beat entirely — go straight from AI summary to close |

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

### Phase 0: Setup (All team members, 30 min)

Every team member does this at the start:

```bash
# 1. Clone repo and create SFDX project
git clone <repo-url> && cd aibuilderday
sf project generate -n build -d ./build/

# 2. Everyone authenticates to the shared org
sf org login web -a aibuilder -r https://orgfarm-23ada909fc.test1.my.pc-rnd.salesforce.com

# 3. Verify access and enable source tracking
sf org open -o aibuilder
sf project retrieve start -o aibuilder  # baseline pull

# 4. Each person creates their feature branch
git checkout -b feat/<role-area>
```

**If you've never used the SF CLI before**: the `sf org login web` command opens a browser. Log in with the shared credentials. The CLI stores a token. After that, all `sf` commands "just work" against the org.

### Parallel Execution by Role

```
HOUR  0----3----5----8----11---14---16---20---22---24
      |         |         |         |         |    |
DATA  [████ objects+fields ██ deploy ██]       |    |
FLOW         [██████ screen flows █ record-triggered █ scheduled █]
UX                   [█████ app+pages █████ dashboard █ polish █]
AGENT                     [█████ topics+actions █ prompts █ test █]
DOCS  [█ research █████ arch diagram ███ pitch script ██ runbook █]
DEMO                               [██ seed data █ rehearse ██ GO]
```

### Critical Handoff Points
1. **Hour 3**: Data Architect merges `feat/data-model` → main, deploys. Flows can begin.
2. **Hour 8**: Flow Builder merges `feat/flows` → main, deploys. UX can reference flows on pages.
3. **Hour 11**: UX merges `feat/lightning-ux` → main. Agent can reference pages/flows.
4. **Hour 14**: Agent config complete in org. Retrieve + commit for reproducibility.
5. **Hour 16**: Demo Lead loads seed data. Everyone verifies golden path works end-to-end.
6. **Hour 20**: **CODE FREEZE.** Only bug fixes and demo polish after this point.
7. **Hour 22**: Full dress rehearsal of 4-minute demo. Time it. Fix anything that's slow/broken.
8. **Hour 24**: Present.

### What To Do If You're Blocked

| Situation | What To Do |
|-----------|-----------|
| Waiting for Data Architect to finish objects | Read the legal reference doc; sketch your flow logic on paper; write prompt templates |
| Flow won't save/activate | Check error message — usually a missing field reference. Ask Data Architect if field exists. |
| Agent gives wrong answers | Check field API names in your prompt. Test the underlying Flow Action independently. |
| Org is slow/unresponsive | Wait 2 min and retry. If persistent, check Salesforce Status (status.salesforce.com). |
| Git merge conflict in XML | The component owner resolves. For hackathon speed: take the newer version. |
| Totally stuck on something | Ask the team. Ask the Salesforce mentors. Use Claude/AI to debug. Don't spend >30 min blocked silently. |

### Deploy Commands (in order of dependency)
```bash
# Phase 1 deploy (Data Architect):
sf project deploy start -o aibuilder -d ./build/force-app/main/default/objects
sf project deploy start -o aibuilder -d ./build/force-app/main/default/tabs
sf project deploy start -o aibuilder -d ./build/force-app/main/default/permissionsets

# Phase 2 deploy (Flow Builder):
sf project deploy start -o aibuilder -d ./build/force-app/main/default/flows

# Phase 3 deploy (UX Builder):
sf project deploy start -o aibuilder -d ./build/force-app/main/default/flexipages
sf project deploy start -o aibuilder -d ./build/force-app/main/default/applications
sf project deploy start -o aibuilder -d ./build/force-app/main/default/layouts

# Full deploy (any conflicts resolved):
sf project deploy start -o aibuilder -d ./build/force-app

# Seed data (Demo Lead):
sf data import tree -o aibuilder -f ./build/data/plan.json
```

---

## Quick Reference: Common SF CLI Commands

For team members who are new to the Salesforce CLI:

```bash
# Open the org in your browser
sf org open -o aibuilder

# Deploy metadata from your local files to the org
sf project deploy start -o aibuilder -d ./build/force-app

# Retrieve metadata from the org to your local files
sf project retrieve start -o aibuilder

# Retrieve a specific component
sf project retrieve start -o aibuilder -m "CustomObject:Commitment__c"
sf project retrieve start -o aibuilder -m "Flow:Blue_Sheet_Filing"
sf project retrieve start -o aibuilder -m "FlexiPage:Commitment_Record_Page"

# Query data in the org (like SQL)
sf data query -o aibuilder -q "SELECT Id, Patient_Name__c, Status__c FROM Commitment__c"

# Create a single record
sf data create record -o aibuilder -s Commitment__c -v "Patient_Name__c='John Doe' Status__c='Emergency Hold'"

# Import bulk data from JSON
sf data import tree -o aibuilder -f ./build/data/plan.json

# Delete all data for an object (careful!)
sf data delete bulk -o aibuilder -s Commitment__c -f ./build/data/ids-to-delete.csv

# Check deployment status
sf project deploy report -o aibuilder

# List all orgs you're authenticated to
sf org list
```
