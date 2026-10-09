# BPMN 2.0 Process Models

Two BPMN 2.0 process models built as coursework at SRM IST, each with a written report and a machine-readable model file.

| # | Process | Model file | Report |
|---|---------|-----------|--------|
| 1 | Student Project Approval & Allocation | [`Student_Project_Approval.bpmn`](Student_Project_Approval.bpmn) | [`BPMN_Assignment1_Student_Project_Approval.pdf`](BPMN_Assignment1_Student_Project_Approval.pdf) |
| 3 | Loan Origination & Approval | [`Loan_Origination_Approval.bpmn`](Loan_Origination_Approval.bpmn) | [`BPMN_Assignment3_Loan_Origination.pdf`](BPMN_Assignment3_Loan_Origination.pdf) |

**Author:** Amol Saxena · B.Tech Computer Science Engineering · Department of Computing Technologies, SRM IST, Kattankulathur

---

## Repository contents

```
.
├── README.md
├── Student_Project_Approval.bpmn                 # Assignment 1 model (BPMN 2.0 XML + diagram interchange)
├── BPMN_Assignment1_Student_Project_Approval.pdf # Assignment 1 report + diagram pages
├── Loan_Origination_Approval.bpmn                # Assignment 3 model
├── BPMN_Assignment3_Loan_Origination.pdf         # Assignment 3 report + diagram pages
└── images/
    ├── student-project-approval-diagram.png
    └── loan-origination-diagram.png
```

## How to open the models

The `.bpmn` files are standard BPMN 2.0 XML with diagram interchange, so the layout opens exactly as drawn.

- **Camunda Modeler** (free desktop app): *File → Open File…*
- **demo.bpmn.io** (browser): drag the `.bpmn` file onto the page

The diagrams are wide, so zoom in on a lane to read the labels. The PDFs contain a full-width print followed by zoomed pages of the same model.

---

## Assignment 1 – Student Project Approval & Allocation

![Student Project Approval diagram](images/student-project-approval-diagram.png)

**Scope.** Starts when a student team decides to submit a project proposal. Ends when a guide has been allocated and has confirmed, or the proposal is closed as rejected, lapsed, invalid or withdrawn.

**Participants.** One department pool with four lanes: *Student / Project Team*, *Project Coordinator*, *Project Management System* and *Review Committee (incl. HoD)*. The **Faculty Guide** is a separate black-box pool, because the department can only send a request and wait for a reply. That makes the exchange a pair of message flows and lets an event-based gateway be used correctly.

**Happy path.** Submit proposal → validate form, team and eligibility → parallel similarity check and coordinator screening → committee evaluation → optional ethics / lab clearances → match eligible guides → send allocation request → guide accepts → record allocation and notify team → team acknowledges → project allocated.

**Failure paths modelled (F1–F12):**

| ID | Failure | BPMN construct |
|----|---------|----------------|
| F1 | Invalid form or data | Exclusive gateway with a resubmission counter (≤ 2) |
| F2 | Team composition breaks rules | Send task + error end event |
| F3 | Submission deadline missed | Interrupting timer boundary event |
| F4 | Duplicate or plagiarised topic | Parallel check + "Both checks clear?" gateway |
| F5 | Committee asks for revision | Revision loop capped at 2 cycles |
| F6 | Committee rejects | One replacement topic allowed, else reject |
| F7 | Committee is slow | 7-day non-interrupting reminder, 14-day interrupting escalation to HoD |
| F8 | No eligible guide | Collapsed sub-process *Manual guide allocation* |
| F9 | Guide declines | Exclude guide and re-match, up to 3 attempts |
| F10 | Guide never replies | Timer branch on the event-based gateway (5 working days) |
| F11 | Notification or database failure | Error boundary event, 3 retries, manual fallback |
| F12 | Team dissolves or guide leaves after allocation | Event sub-process + compensation + terminate end event |

**Element counts (from the `.bpmn` file):** 2 participants, 4 lanes, 15 user tasks, 5 service tasks, 2 business rule tasks, 4 send tasks, 2 sub-processes, 11 exclusive / 2 parallel / 2 inclusive / 1 event-based gateways, 5 boundary events, 71 sequence flows, 3 message flows.

---

## Assignment 3 – Loan Origination & Approval

![Loan Origination diagram](images/loan-origination-diagram.png)

**Scope.** Starts when an applicant submits a loan application with supporting documents. Ends when the loan is disbursed, the application is rejected, the offer lapses, expires or is declined, or the applicant withdraws.

**Participants.** One bank pool with six lanes: *Applicant*, *Loan Officer*, *Core Banking System*, *Underwriter / Credit Committee*, *Operations / Disbursement* and *Fraud & Compliance*. The **Credit Bureau** is a separate black-box pool, so the bureau exchange is a pair of message flows and the wait for its reply uses an event-based gateway.

**Happy path.** Submit application → check document completeness → parallel KYC / sanctions check, income verification and credit bureau request → join → screening clear → optional collateral valuation and enhanced due diligence → score eligibility, risk and DTI → auto-approve or underwrite → generate and send offer → applicant reviews and accepts → e-sign → verify bank account → disburse funds → create loan account and repayment plan → notify applicant → loan disbursed.

**Failure paths modelled (F1–F12):**

| ID | Failure | BPMN construct |
|----|---------|----------------|
| F1 | Documents incomplete or illegible | Loop back to the applicant; 7-day interrupting timer ends at *Application lapsed* |
| F2 | Identity mismatch | Manual identity check, then reject with reason code |
| F3 | Forged documents or AML / sanctions hit | Escalation event, collapsed *Fraud and AML investigation* sub-process, terminate end event |
| F4 | Credit bureau does not respond | Timer branch (2 min) of the event-based gateway, retry up to 3 times, then manual credit check |
| F5 | Credit score below cut-off | Rejection with reasons (*Rejected – credit*) |
| F6 | Income too low or DTI too high | Counter-offer with exactly one re-scoring loop |
| F7 | Amount above underwriter authority | Escalation event to senior credit committee, non-interrupting SLA reminder timer |
| F8 | Collateral valued low or title doubtful | Inclusive gateway branch, reduce amount or demand clear title |
| F9 | Applicant ignores or declines the offer | 15-day interrupting timer (*Offer expired*) vs explicit *Offer declined* |
| F10 | Agreement not e-signed | 5-day timer, up to 2 reminders, then cancel offer |
| F11 | Disbursement fails | Error boundary event (payment gateway), hold funds, retry on corrected details |
| F12 | Applicant withdraws at any stage | Event sub-process, compensation activity *Reverse fees, release holds and close the account*, terminate end event |

**Element counts (from the `.bpmn` file):** 2 participants, 6 lanes, 16 user tasks, 6 service tasks, 2 business rule tasks, 9 send tasks, 2 sub-processes, 14 exclusive / 2 parallel / 2 inclusive / 1 event-based gateways, 6 boundary events, 86 sequence flows, 2 message flows.

---

## BPMN constructs demonstrated

Both models use the same set of constructs, each chosen for a stated reason (explained in sections 3 and 4 of each report):

- **Task types:** user, service, business rule, send, collapsed sub-process, event sub-process, compensation activity
- **Gateways:** exclusive (XOR), parallel (AND) split and join, inclusive (OR) split and join with a default flow, event-based
- **Events:** timer boundary (interrupting and non-interrupting), error boundary, message catch, escalation throw, compensation throw, terminate end
- **Structure:** lanes inside one pool, a separate black-box pool for the external party, labelled sequence and message flows

Every loop in both models has an explicit counter or timeout, so no path can run forever.

## Tools

Models are standard BPMN 2.0 and open in [Camunda Modeler](https://camunda.com/download/modeler/) or [demo.bpmn.io](https://demo.bpmn.io). Reports are included as PDF.
