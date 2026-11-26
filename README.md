# EPBCS Approval Workflow & Security — Requirements Questionnaire

A workshop toolkit for Oracle EPM Planning (EPBCS / PBCS) implementations. It helps a consultant run the **approvals and security** requirements session and turn the client's answers straight into configuration: the Approval Unit Hierarchy, approval groups, and security.

> **Author:** Dhruv — Senior Consultant, Oracle EPM (EPBCS / PBCS / TRCS)
> **Scope:** generic and client-neutral. The Province → Region → District → Mayor scenario is illustrative.

---

## Why this exists

Approval workflow is usually designed late, and projects run into three problems:

1. **One person per level.** If the only approver is on leave, the budget stops.
2. **"Approval groups" is misunderstood.** In EPM Planning an approval group is a *slice of data* (for example Opex accounts), not a group of users.
3. **Approvals and security are treated as separate topics.** They are not: approvals can only take access away, never give it.

This questionnaire puts all three on the table in a single workshop.

---

## The three ideas at a glance

### 1. Approval path — who moves the budget up

```mermaid
flowchart LR
    P["District preparer<br/><small>Owner: enters data<br/>Starts and promotes</small>"]
    A["District approver<br/><small>Reviewer 1<br/>Promotes or rejects</small>"]
    R["Regional reviewer<br/><small>Reviewer 2<br/>Promotes or rejects</small>"]
    M["Mayor<br/><small>Final reviewer<br/>Approves province</small>"]
    P -- Promote --> A -- Promote --> R -- Promote --> M
    A -. Reject .-> P
    R -. Reject .-> P
    M -. Reject .-> P
    style M fill:#e6f0fb,stroke:#1f6fd1,stroke-width:2px
```

Make each level a **group** (a primary person plus a named backup) so no level depends on one person. If you name individual reviewers, all of them must approve, in the order entered. With a group reviewer, any member can promote.

### 2. Approval groups — reviewing one slice of the budget

```mermaid
flowchart LR
    D["District budget<br/><small>One approval unit<br/>split into phases</small>"]
    D --> O["Opex phase<br/><small>Opex accounts</small>"] --> OR["Opex reviewers<br/><small>Any member promotes</small>"]
    D --> RV["Revenue phase<br/><small>Revenue accounts</small>"] --> RR["Revenue reviewers<br/><small>Any member promotes</small>"]
    D --> S["Salaries phase<br/><small>Workforce accounts</small>"] --> SR["Workforce reviewers<br/><small>Any member promotes</small>"]
    OR --> M["Mayor<br/><small>Final approval</small>"]
    RR --> M
    SR --> M
    style M fill:#e6f0fb,stroke:#1f6fd1,stroke-width:2px
```

Each phase is promoted separately, and one approval unit hierarchy holds up to 20 approval groups. Who can **see** each slice is still decided by security, not by the phase.

### 3. Security — what a user can actually edit

```mermaid
flowchart LR
    I["Identity groups<br/><small>IDCS or Azure AD</small>"]
    S1["1. Write security on cell<br/><small>Entity + Account filters</small>"]
    S2["2. You are current owner<br/><small>Unit or phase is with you</small>"]
    S3["3. Status allows edits<br/><small>Not approved or locked</small>"]
    AND(("AND"))
    E["Can edit the cell<br/><small>Otherwise read only or hidden</small>"]
    I --> S1
    I --> S2
    S1 --> AND
    S2 --> AND
    S3 --> AND
    AND --> E
    style E fill:#e6f0fb,stroke:#1f6fd1,stroke-width:2px
```

A reviewer who is named in the approval path but has no Write security will find every cell read-only when the budget reaches them.

### 4. Choosing the design from the answers

```mermaid
flowchart TD
    Q1{"Must one slice be approved<br/>and locked while another<br/>is still open?"}
    Q1 -- Yes --> AG["Approval groups<br/>(Extended template)"]
    Q1 -- No --> Q2{"Different reviewer<br/>per slice?"}
    Q2 -- Yes --> SD["Secondary dimension<br/>on the approval unit"]
    Q2 -- No --> EN["Entity-only hierarchy"]
    AG --> Q3{"Must the slice reviewer be<br/>blocked from other accounts?"}
    SD --> Q3
    Q3 -- Yes --> AS["Account dimension security<br/>+ filters per group"]
    Q3 -- No --> FD["Focused forms and<br/>dashboards only"]
```

---

## What's in this repo

| File | What it is |
| --- | --- |
| [`questionnaire.md`](questionnaire.md) | The full questionnaire: Parts A–F, about 60 questions, each with "why it matters" |
| [`templates/questionnaire-template.csv`](templates/questionnaire-template.csv) | The same questions as a blank workshop sheet; open it in Excel or Google Sheets |

### Questionnaire structure

| Part | Topic | Drives |
| --- | --- | --- |
| A | Approval hierarchy and people | Approval Unit Hierarchy, owners, reviewers, template |
| B | Continuity: one person or a group | User vs group owners/reviewers, Take Ownership, Delegate |
| C | Approval groups | Phases per data slice, or a secondary dimension |
| D | Security | Roles, Entity/Account/Scenario/Version filters, access grant matrix |
| E | Process controls | Validation rules, deadlines, notifications, locking, reopen |
| F | Answer → configuration map | The design decision for each answer |

---

## How to use it

1. Send `questionnaire.md` to the client before the workshop.
2. In the session, fill in the CSV template live. Mark open items as `TBC` with an owner.
3. Use Part F to turn the answers into the approval and security design.
4. Log every level still held by one named person, and every slice whose visibility rule isn't agreed yet, as open items.

---

## Sources

Behaviours were checked against Oracle Cloud EPM documentation (November 2026):

- [Assigning Approval Unit Owners and Reviewers](https://docs.oracle.com/en/cloud/saas/planning-budgeting-cloud/pfusa/assigning_planning_unit_owners_and_reviewers.html)
- [About Group-based Approvals](https://docs.oracle.com/cloud/latest/pbcs_common_si/CSPAG/ch09s05s01s04.html)
- [Understanding Approval Groups](https://docs.oracle.com/en/cloud/saas/planning-budgeting-cloud/pfusa/understanding_approval_groups.html)
- [Assigning Approval Groups to an Approval Unit Hierarchy](https://docs.oracle.com/en/cloud/saas/planning-budgeting-cloud/pfusa/assigning_approval_groups_to_an_auh.html)
- [Setting Approval Unit Hierarchy Name, Scope, and Template](https://docs.oracle.com/en/cloud/saas/planning-budgeting-cloud/pfusa/setting_planning_unit_hierarchy_name_scope_and_template.html)
- [Approvals Design](https://docs.oracle.com/en/cloud/saas/planning-budgeting-cloud/pfusa/approvals_design_considerations.html)
- [Planning Unit States](https://docs.oracle.com/cloud/latest/pbcs_common_si/CSPLU/ch10s01s01.html)

---

*Oracle, EPBCS and PBCS are trademarks of Oracle Corporation. This is an independent consultant toolkit, not affiliated with or endorsed by Oracle. It contains no client data.*
