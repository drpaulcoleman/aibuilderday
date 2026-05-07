# **Guardian AI \- Protecting Rights, Preventing Gaps \[DRAFT\]**

### **Involuntary Civil Commitment Management |** **Utah DHHS | Hackathon Build Plan | Salesforce Agentforce \+ MuleSoft |** \----------------------------------------------------------------------------------------------------

**1: Requirements Document**

## **Business Problem**

The problem and the current process: [https://vimeo.com/1055344765?fl=pl\&fe=vl](https://vimeo.com/1055344765?fl=pl&fe=vl) 

Utah's Involuntary Civil Commitment process is fragmented across paper forms, siloed legacy systems, and disconnected law enforcement workflows. Legal holding windows (72 hours under a Blue Sheet) can lapse without anyone knowing. Court orders may not reach the right facility in time. People in crisis fall through the cracks \- and the state faces massive legal liability.

### 

### [Blue Sheet](https://dsamh-training.utah.gov/_documents/civilcommitment/adultforms/applicationemergency/34_1_bluesheet_revised_fillable_10.21.2024.ada.pdf) (Form 34-1) \- Emergency Application

* *Who uses it:* Peace officers (police) and mental health professionals in the field or at an ER  
* *What it is:* A legal form to admit someone for up to 72 hours if they're a "substantial danger to self or others" \- involuntary commitment with certification up to 72 hrs  
* *Where it lives today:* Filed in the field \- often on paper, disconnected from any central system until much later

### [Pink Sheet](https://dsamh-training.utah.gov/_documents/civilcommitment/adultforms/applicationemergency/34_2_pinksheet_revised_fillable_10.21.2024.ada.pdf) (Form 34-2) \- Administrative/Medical Certification

* *Who uses it:* Medical staff and transport/detention personnel  
* *What it is:* A form used to authorize transport and initial detention before a formal court hearing \- involuntary commitment without certification up to 72 hrs  
* *Where it lives today:* Also paper-based, initiated at ERs or in the field \- same silo problem as Blue Sheets

### White Sheet \- Court Commitment Order

* *Who uses it:* Judges in the Utah Court System (via XChange/CORIS)  
* *What it is:* The formal court order for Involuntary Commitment \- this is what legally extends detention beyond the 72-hour Blue Sheet window  
* *Where it lives today:* In the Utah AOC Court System, which currently does not automatically notify DHHS when a judge signs one

Each sheet is a physical form that triggers a legal process:

| Sheet | Form | Process Triggered |
| :---- | :---- | :---- |
| Blue | Emergency Application | 72-hr emergency hold begins |
| Pink | Medical/Transport Cert | Transport & initial detention authorized |
| White | Court Order | Formal involuntary commitment begins |

*The problem:*

* Blue & Pink Sheets start on paper in the field (police car, ER)  
* They eventually get entered into a DHHS system \- but often hours or days later  
* The White Sheet lives in the Utah Court System (XChange/CORIS) \- a completely separate platform  
* Each Local Mental Health Authority (LMHA) runs its own EHR system  
* None of these systems talk to each other automatically

This is exactly why Guardian AI \+ MuleSoft is the pitch \- one system of record, with automated bridges to all the others. 

## **Stakeholders**

| Stakeholder | Role |
| :---- | :---- |
| Utah DHHS Administrators | Oversight of Local Mental Health Authorities (LMHAs) |
| Law Enforcement Officers | Front-line commitment initiators (Blue/Pink Sheet issuers) |
| Emergency Department Staff | Initial receiving facilities |
| Judges / Utah AOC Courts | Issuers of commitment orders (White Sheet) |
| Local Mental Health Authorities | Facility managers, treatment coordinators |
| Utah State Hospital | Long-term commitment facility |
| DHHS Compliance Officers | Audit, legal compliance, legislative reporting |

## **Functional Requirements**

### **Core Data & Records**

* Capture and track Blue Sheet (Emergency Application), Pink Sheet (Medical/Transport Certification), and White Sheet (Court Order) as structured records in Salesforce  
* Enforce 72-hour holding window with automated countdown and escalation alerts  
* Track commitment lifecycle: Emergency Hold → Court Hearing → Commitment Order → Renewal/Discharge  
* Store Psychiatric Advance Directives linked to individual records  
* Maintain full audit trail of every status change, user action, and system event

### **Integration Requirements**

* Real-time integration with Utah AOC Court System (XChange/CORIS) via MuleSoft for commitment orders and renewals  
* Integration with Utah Department of Public Safety (DPS) for law enforcement access  
* Integration with LMHA Electronic Health Records (EHR) systems (multiple vendors) via MuleSoft  
* Mobile-accessible officer lookup: officer can query active commitment status in the field

### **Agentforce AI Requirements**

* Agentforce Agent ("Guardian AI") to proactively monitor all open commitments  
* Automated 72-hour countdown with escalation: alerts at 48hrs, 60hrs, 68hrs  
* Natural language query interface for case workers ("Show me all Blue Sheets expiring today in Salt Lake County")  
* Auto-draft court hearing preparation summaries  
* Renewal reminder agent: 30, 60, 90 days before commitment expiry  
* Anomaly detection: flag cases where required court action has not been received  
* Discharge coordination: auto-generate community treatment plan checklist

### **Non-Functional Requirements**

* Deployed on **Salesforce Government Cloud** to meet HIPAA and CJIS compliance  
* Role-based access control: officers see only active status, clinicians see clinical data, courts see legal data  
* Full audit logging for legislative compliance (HB 137 / Utah Code 26B-5-331)  
* 99.9% uptime SLA as this is a public safety system  
* Data encryption at rest and in transit

**2: Hackathon Project Plan**

For the demo, we will need:

1. One working Salesforce record flow (Blue Sheet → 72hr clock)  
2. Blue Sheet, Pink Sheet, and White sheet need to be forms with their own workflow (probably develop these forms to show)  
3. One Agentforce agent with 2-3 polished queries  
4. One mock MuleSoft integration returning a court order  
5. A clean Lightning dashboard  
6. Simple architecture diagram  
7. Pitch narrative  
8. Show before and after workflow  
9. Story to sell

Judges probably will be evaluating vision \+ feasibility \+ demo quality. They probably would ignore if we used mock data or actually connected to the Utah Court System.

This is exactly the right level of complexity for a hackathon that wants to showcase Agentforce. The idea needs to stay big vs what we actually build in 2 days vs. what we show in diagrams.

# **3: The Pitch \- Wins & Value**

## **The Headline**

"Every hour a Blue Sheet sits in the wrong inbox is an hour someone's civil rights \- and the state's legal standing \- are at risk. Guardian AI on Agentforce makes sure that never happens."

## **The Wins**

### **For the State of Utah**

* **Legal Liability Eliminated** \- Automated 72-hour monitoring ensures no one is held past their legal window without a court order  
* **Legislative Compliance** \- Built-in reporting for HB 137 and Utah Code 26B-5-331 mandates, with 2025/2026 effective dates approaching fast  
* **Audit-Ready** \- Full chain-of-custody audit trail from first officer contact to final discharge

  ### **For DHHS Administrators**

* **Single Source of Truth** \- No more checking 10 different systems; one unified view of every active commitment across all LMHAs  
* **Proactive, Not Reactive** \- Guardian AI surfaces problems before they become crises

  ### **For Law Enforcement**

* **Real-Time Field Access** \- Officers know instantly if a person has an active commitment order or Psychiatric Advance Directive  
* **Safer Interactions** \- Better information leads to better, safer outcomes in the field

  ### **For Individuals in Crisis**

* **Dignity & Rights Protected** \- Automated compliance means no one is held without legal authority  
* **Continuity of Care** \- Seamless handoff from emergency hold to long-term treatment plan

## **Why Salesforce Wins vs. Any Alternative**

| Capability | Legacy / Custom Build | Other Gov't SaaS | Salesforce \+ Agentforce |
| :---- | :---- | :---- | :---- |
| AI-powered proactive monitoring | ❌ | ❌ | ✅ Guardian AI agent |
| Real-time court system integration | ❌ | ⚠️ Custom only | ✅ MuleSoft pre-built connectors |
| HIPAA \+ CJIS compliant cloud | ⚠️ Expensive | ⚠️ Varies | ✅ Government Cloud FedRAMP |
| Configurable without heavy development | ❌ | ⚠️ Limited | ✅ Clicks not code |
| Scales across all LMHAs statewide | ⚠️ Hard | ⚠️ Possible | ✅ Multi-tenant, native |
| Built-in audit & compliance | ❌ | ⚠️ Add-on | ✅ Native Shield / Field Audit |
| Time to value | 18–36 months | 12–24 months | ✅ 6–9 months |

# **4: The Story \- Presentation Talking Points**

## **Opening Hook (30 seconds)**

"Imagine it's 2 AM. A police officer in Provo responds to a mental health crisis call. The person is a danger to themselves. The officer files a Blue Sheet \- an emergency commitment order \- and the person is taken to a facility. The clock starts: 72 hours. If no court order arrives in 72 hours, that person must be released \- regardless of their condition. Right now in Utah, that clock is tracked on a spreadsheet. Nobody gets an alert. Nobody knows. And the state is one missed deadline away from a lawsuit \- or worse, a tragedy."

## **The Problem (60 seconds)**

* Today, Blue Sheets and Pink Sheets are paper forms filed in the field, disconnected from any central system  
* DHHS administrators are manually checking multiple systems to track where people are in the commitment process  
* The Utah Court System (XChange/CORIS) doesn't automatically notify DHHS when a judge signs a commitment order  
* Local Mental Health Authorities each run different EHR systems \- none talking to each other  
* The result: data silos, missed deadlines, legal risk, and worst of all \- people falling through the cracks

## **The Solution (90 seconds)**

* We built **Guardian AI** \- an Agentforce-powered Civil Commitment Management system on Salesforce Government Cloud  
* The moment a Blue Sheet is filed \- in the ER, in the field, anywhere \- it's captured in Salesforce and the 72-hour legal clock starts automatically  
* MuleSoft connects the Utah Court System, the Department of Public Safety, and every LMHA in the state \- in real time  
* Guardian AI, powered by Agentforce, monitors every single open commitment 24/7. It alerts case workers at 48 hours. It escalates at 60 hours. It pages supervisors at 68 hours. Nobody gets held past their legal limit  
* Law enforcement officers can pull up a person's active commitment status from their phone \- in under 10 seconds \- without accessing any clinical data  
* When a judge signs a commitment order, MuleSoft pushes it to Salesforce instantly. The case updates. The clock resets. The right people are notified automatically  
* And when it's time to plan a discharge or community treatment, Guardian AI drafts the care coordination checklist so nothing gets missed

## **The Live Demo Moment (60 seconds)**

* Live: Ask Guardian AI: "What Blue Sheets are expiring in the next 6 hours?"  
* Watch it return a real-time list with facility, officer, case worker, and time remaining  
* Ask: "Draft a court hearing summary for Case \#001" \- watch it generate a structured, court-ready document in seconds  
* Show the proactive alert: Guardian AI autonomously identifies a case at 68 hours with no court order and escalates it \- without anyone asking

## **The Close (30 seconds)**

"Utah DHHS is under pressure from the Legislature right now to modernize this system. HB 137 and Utah Code 26B-5-331 have 2025/2026 effective dates. They need a solution that's secure, compliant, and fast to implement. No other platform gives you AI-powered autonomous agents, government-grade compliance, and enterprise integration out of the box \- in one platform. Salesforce does. Guardian AI is ready to deploy."

**5: Quick Reference \- Salesforce Products Used**

* **Salesforce Public Sector Solutions** \- base data model and case management  
* **Salesforce Government Cloud** \- HIPAA \+ CJIS \+ FedRAMP compliance  
* **Agentforce** \- Guardian AI autonomous agent (reactive \+ proactive modes)  
* **Einstein Data Library / Data Cloud** \- unified individual profile, data grounding for AI  
* **MuleSoft Anypoint Platform** \- integrations to AOC Courts, DPS, LMHA EHRs  
* **Salesforce Shield** \- field audit trail, event monitoring, encryption  
* **Experience Cloud** \- law enforcement officer field access portal  
* **Salesforce Flow** \- automation of commitment lifecycle events  
* **Slack** \- real-time alerts and collaboration for DHHS teams

# **6: UI Screen Designs**

## **Screen 1 \- Blue Sheet Filing Form (Officer / ER View)**

This is the first screen a police officer or ER nurse sees \- mobile-optimized, minimal friction, designed to be completed in under 2 minutes in the field.  
**Key UX Notes:**

* Officer/clinician auto-populated from login (CJIS SSO)  
* Facility dropdown pre-filtered by GPS location  
* Submit button triggers the Flow instantly \- clock starts  
* Warning banner reinforces the legal weight of submission

**Screen 2 \- Blue Sheet Record Detail Page (Caseworker View)**  
The Salesforce record page a DHHS caseworker sees after a Blue Sheet is filed \- the live countdown is the hero element.  
**Key UX Notes:**

* Live countdown is the dominant visual \- impossible to miss  
* Progress bar turns red as expiry approaches  
* Milestone tasks show at-a-glance what's done and what's overdue  
* Guardian AI is embedded inline \- no need to navigate away

**Screen 3 \- Guardian AI Chat Panel (Full Conversation View)**  
The full Agentforce chat experience, accessible from any record or the dashboard sidebar.  
**Key UX Notes:**

* Suggested prompts reduce friction for first-time users  
* Each result card has a direct "View Case" deep-link  
* Actions are confirmed inline \- Guardian AI shows exactly what it did  
* Color-coded urgency indicators (🔴🟡🟢) match the dashboard

**Screen 4 \- Court Order Received Notification (MuleSoft Event)**  
The real-time notification that appears when MuleSoft delivers a court order from XChange/CORIS.  
**Key UX Notes:**

* Notification is triggered automatically \- no human action needed  
* Clock stopped status is explicit and reassuring for compliance  
* Source attribution (MuleSoft / XChange) builds trust with auditors  
* Renewal review date is auto-set with a future reminder

**Screen 5 \- Supervisor Escalation Alert (Mobile Push)**  
What a DHHS supervisor sees on their phone at 10:01 PM.  
**Key UX Notes:**

* Fits entirely on a mobile lock screen \- no scrolling needed  
* Deep links to the full record with one tap  
* "Call Liaison" button can be wired to click-to-call via Salesforce Mobile

**To Do:** Workflow Before and After  
