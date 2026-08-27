# Open Contract Migration — Scenario Classification Rules

**Authoritative rules for deciding whether an in-flight contract is migrated as a Cash Sale, a Subscription, both, or neither.**

Source: *NetSuite Subscription and Cash Sale Migration Guide* (Data Steward instructions) and
*Volaris – Data Migration: Subscriptions & Cash Sales*. Where those two documents disagree,
this document wins. Discrepancies are listed in `05-open-items-and-known-issues.md`.

---

## 1. Fixed project parameters

| Parameter | Value |
|---|---|
| Wave 2 NetSuite go-live date | **1 September 2026** |
| Legacy recognition cut-off | **31 August 2026** (last day recognised in the legacy system) |
| Migration approach | **Remaining value approach** — migrate only billing and revenue activity remaining *after* go-live |
| Revenue Recognition Start Date on all migrated Cash Sales | **1 September 2026** |
| Revenue recognition basis | Straight-line over the remaining term (NetSuite ARM) |

**The remaining value approach is a hard constraint.** Do not recreate completed legacy
activity. Do not prepare historical-value templates unless project leadership formally
changes the approach.

---

## 2. The core decision rule

Classify on two axes only: **billing status at go-live** and **revenue recognition status at go-live**.

| Billing status at go-live | Revenue recognition status | Migrate as | Why |
|---|---|---|---|
| Billing complete, revenue still deferred | In progress | **Cash Sale only** | Creates the future recognition schedule without generating another customer invoice or open receivable |
| Billing and recognition both still pending | Not started | **Subscription only** | NetSuite must generate future charges/invoices and recognise the related revenue |
| Current term billed and awaiting recognition; later terms still require billing | Partly complete | **Both** | Cash Sale for the already-billed remaining deferred revenue; Subscription for future billable terms |
| Billing and recognition both complete | Complete | **Neither** | Represented through the cutover trial balance |

---

## 3. Scenario reference and the crossed-numbering warning

The two source documents number their scenarios differently. **They do not line up.**
Always state both identifiers when referring to a scenario.

| Migration Guide | Subs & Cash Sales doc | Condition | Output |
|---|---|---|---|
| **Scenario A** | **Scenario 2** | Single term, fully billed, deferred revenue remaining | Cash Sale only |
| **Scenario B** | **Scenario 1** | Multi-year / renewable; current term billed, future term unbilled | Both |
| **Scenario C** | **Scenario 3** | Billing and revenue both pending after go-live | Subscription only |
| **Scenario D** | **Scenario 4** | Contract fully completed before go-live | Neither |
| **Scenario E** | *(no equivalent)* | Source amount appears to be full contract value | **Exception — do not proceed** |

Note that Guide A maps to doc 2, and Guide B maps to doc 1. This is the single most common
source of confusion when the two documents are read side by side.

---

## 4. Facts required before any classification

A contract **cannot** be classified until all five are known and evidenced:

1. **Contract start date**
2. **Contract end date** (of the current term, and of the whole agreement if multi-year)
3. **Billing status** — which periods has the customer actually been invoiced for in the legacy system?
4. **Revenue recognised through 31 August 2026**
5. **Amount billed for the current term**

Derived values:

- **Remaining deferred revenue at go-live** = amount billed for the current term
  − revenue recognised through 31 August 2026, adjusted for approved credits or modifications
- **Next billable term start date** = the first day of the next period for which billing has *not* occurred

If any of the five inputs is missing or unsupported, the record is an **exception**. Do not guess.

---

## 5. Classification procedure

Apply in order. Stop at the first match.

**Step 1 — Full-contract-value trap check (Scenario E).**
If the amount offered for the Cash Sale equals the total contract value, or appears to include
periods already recognised or future terms not yet billed:
→ **Do not proceed.** Recalculate or obtain confirmation of the deferred balance remaining at go-live.
Put only the remaining deferred amount in Cash Sales and future unbilled terms in Subscriptions.
If the allocation cannot be supported from source data, mark as an exception and obtain
business-owner confirmation before submission.

**Step 2 — Completed contract (Scenario D / 4).**
If contract end date < 1 September 2026 **and** billing is complete **and** revenue is fully recognised:
→ **Neither.** Confirm the activity is represented in the cutover trial balance.
Do not recreate legacy invoices or recognition schedules.

**Step 3 — Future contract (Scenario C / 3).**
If no invoice has been issued for the future service period **and** no related revenue has been recognised:
→ **Subscription only.** Subscription Start Date = first day of the future billable service period.
Enter the future billable value and full future term. No Cash Sale — there is no previously
billed deferred revenue to carry forward.

**Step 4 — Fully billed, deferred remaining (Scenario A / 2).**
If the customer has been billed for the full remaining contract term before go-live, and a
deferred balance remains after go-live:
→ **Cash Sale only.**
- Amount = remaining deferred revenue at go-live (not original contract value, not the amount already recognised)
- Revenue Recognition Start Date = 1 September 2026
- Revenue Recognition End Date = original end date of the current contract term
- **Do not create a Subscription** — no further customer billing is required.

**Step 5 — Split (Scenario B / 1).**
If the current billing term was invoiced before go-live and still has deferred revenue, **and**
one or more later terms will be billed after go-live:
→ **Both.**
- Cash Sale for the current term's remaining deferred revenue
- Cash Sale Revenue Recognition Start Date = 1 September 2026
- Cash Sale Revenue Recognition End Date = the last day before the next billing term starts,
  or the original end date of the current billed term
- Subscription for each future term where billing and recognition are both pending
- Subscription Start Date = **first day of the next billable term**
- **Do not include the already-billed current period in the Subscription amount**

---

## 6. Recognition timing convention

**Legacy recognises through 31 August 2026. NetSuite recognises from 1 September 2026.**

The month of go-live (September 2026) is recognised in **NetSuite**, not in the legacy system.
Count months recognised in legacy up to and including **August 2026**.

Worked check: a contract starting 1 March 2026 at $100/month has recognised
March, April, May, June, July, August = **6 months = $600** by go-live.

---

## 7. Validation rules before submission

Every one of these must pass:

- Cash Sale amount agrees to the deferred revenue balance remaining at go-live
- Revenue Recognition Start Date is **not earlier than** go-live
- Revenue Recognition End Date is after the start date and agrees to the source term
- Future subscription dates begin **after** the Cash Sale recognition period — **periods must not overlap**
- Amounts already billed are **not** included in future Subscription billing
- Amounts already recognised are **not** included in Cash Sales
- The same contract is not loaded entirely as a Cash Sale when future billing remains
- The same period and amount are not represented in more than one migration object
  (Cash Sales, Subscriptions, Open AR, trial balance)
- Exception records include a clear issue description and a named business approver

---

## 8. Escalation triggers

Escalate — do not classify — when any of these are present:

- Missing invoice status
- Unsupported deferred revenue amount
- Unclear next billing date
- Contract amendments, credits, or cancellations
- Non-standard recognition patterns
- Source values that do not reconcile
- Contract end date is before go-live but a deferred balance is still reported (contradiction)

**Standard exception note:**
> "Record held for review because the source does not clearly identify the remaining deferred
> revenue at go-live and/or the boundary between the already-billed term and the next billable term."

---

## 9. What happens after loading (context only — not a data steward action)

The migration team reverses the Cash Sale's billing-side GL impact through the approved
Deferred Revenue Clearing Account (Other Current Liability) and the Bank Migration Account
mapped to the subsidiary–currency combination. NetSuite retains the revenue arrangement and
recognition plan so the remaining deferred revenue is recognised over the dates provided.

Data stewards focus on accurate **remaining amounts, dates, accounts, subsidiary, currency,
customer, and source support**. They do not create duplicate customer billing.
