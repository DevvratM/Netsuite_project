# Open Items, Known Issues, and Terminology

The agent must surface these when relevant rather than answering around them.

---

## 1. Unresolved project decisions

| # | Item | Status | Agent behaviour |
|---|---|---|---|
| 1 | **Cash Sale invoice/transaction date** (Cash Sales column H) | The migration guide states the meeting indicated the contract start date *may* be acceptable because all postings occur in the cutover period, but **this field requires final confirmation before production**. | Do not state a default. Tell the user this is an open item and to confirm with the Functional Workstream Lead. |
| 2 | **Billing Schedules migration method** | The `Start Here` tab lists the migration method for Billing Schedules as **"TBD"** while Billing Account, Subscription and Cash Sales are all "CSV". | Flag that it is unconfirmed whether billing schedules are loaded by file or configured manually. |
| 3 | **Contract end date convention** | The *Subscriptions vs Cash Sales* workbook states end dates as the day *after* the last service day. | Confirm which convention a given source extract uses before advising on Revenue Recognition End Date. |

---

## 2. Contradictions within the source documents

### 2.1 Scenario numbering is crossed between the two guides

Migration Guide **Scenario A** = Subs & Cash Sales doc **Scenario 2**.
Migration Guide **Scenario B** = Subs & Cash Sales doc **Scenario 1**.

Always cite both identifiers, e.g. "Scenario B (Migration Guide) / Scenario 1 (Subs & Cash Sales)".

### 2.2 Arithmetic inconsistency in the fully-billed example

*Volaris – Subscriptions & Cash Sales*, Scenario 2:

- **Prose** says 5 months / $500 recognised → $1,900 remaining deferred
- **Its own monthly table** shows 6 months (Mar–Aug 2026) / $600 recognised → $1,800 remaining

The table is correct under the stated convention that legacy recognises through 31 August 2026.
**Use $1,800.** Do not quote $1,900.

### 2.3 "Unbilled" used to describe a Cash Sale amount

The *Subscriptions vs Cash Sales* workbook annotates a Cash Sale amount as
"this 7000 portion that's unbilled becomes cash sales". This is incorrect terminology.

**Correct usage:**
- **Billed but not yet recognised** = deferred revenue → **Cash Sale**
- **Not yet billed and not yet recognised** → **Subscription**

Never describe a Cash Sale amount as "unbilled."

### 2.4 Worksheet name mismatch

The `Start Here` tab links the Subscription record type to a worksheet named
**"7_Subscription"**, but the actual tab in the workbook is **"4_Subscription"**.
Cosmetic, but it confuses first-time users.

---

## 3. Migration approach boundary

The Cutover Playbook documents two SuiteBilling cutover methods:

- **Method 1 — Create Subscription with Go-Live Date (recommended)**, assuming no deferred
  revenue balance. Historical transactions are not generated; subscriptions generate charges
  for future periods only.
- **Method 2 — Create historical data and reverse it.** Requires recreating all historical
  charges and reversing them by journal entry. Significantly more effort.

**The Open Contracts templates implement the remaining value approach (Method 1 plus Cash Sales
for deferred balances).** Do not advise preparing historical-value templates unless project
leadership formally changes the approach.

---

## 4. Glossary

| Term | Meaning |
|---|---|
| **Go-live** | 1 September 2026 (Wave 2) |
| **Remaining value approach** | Migrate only billing and revenue activity remaining after go-live |
| **Deferred revenue** | Amount billed to the customer but not yet recognised as revenue |
| **Cash Sale (in this migration)** | The vehicle for carrying a remaining deferred revenue balance into NetSuite without creating a new customer invoice or receivable |
| **Subscription** | The vehicle for future billing and recognition that has not yet occurred |
| **ARM** | Advanced Revenue Management — recognises revenue straight-line over the dates on the Revenue Arrangement |
| **SuiteBilling** | Generates charges and invoices from subscription start/end dates and billing frequency |
| **VBU** | Volaris Business Unit — the subsidiary selected on the `Start Here` tab |
| **Deferred Revenue Clearing Account** | Other Current Liability account used post-load to reverse the Cash Sale's billing-side GL impact |
| **Charge-Based** | The billing schedule type used for these templates |

---

## 5. Wave 2 dates

| Milestone | Date |
|---|---|
| Subscriptions due (as of 9/1) | **14 August 2026** |
| Cash Sales due (as of 9/1) | **14 August 2026** |
| Go-live | **1 September 2026** |

---

## 6. Ownership — who to escalate to

| Role | Owns |
|---|---|
| **Functional Workstream Lead** | Process-specific migration logic, business validation steps, sign-off criteria |
| **Data Workstream Lead** | Template readiness, load sequencing, transformation rules, reconciliation support |
| **Data Steward** | Source data extraction, business review, issue triage, sign-off of migrated balances |
| **PMO / Cutover Lead** | Sequencing, communication, dependency tracking, escalation |

Amount and date questions go to the Functional Lead. Template and load questions go to the Data Lead.
