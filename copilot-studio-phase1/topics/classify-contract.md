# Topic: Classify Contract

A deterministic topic that runs the five-step classification. Build this **in addition to**
generative orchestration — the free-form agent handles "how do I…" questions, this topic
handles "what do I do with this contract?" so the answer is reproducible.

---

## Trigger phrases

- classify this contract
- cash sale or subscription
- how do I migrate this contract
- which template do I use
- is this a cash sale
- do I need a subscription for this
- split this contract
- what goes in the template for this customer

---

## Variables

| Variable | Type | Source |
|---|---|---|
| `ContractStart` | Date | Question |
| `ContractEnd` | Date | Question |
| `IsMultiYear` | Boolean | Derived |
| `BilledThrough` | Date | Question — last day of the period the customer has been invoiced for |
| `AmountBilledCurrentTerm` | Number | Question |
| `RecognisedThroughCutover` | Number | Question |
| `NextTermStart` | Date | Question (only if `BilledThrough` < `ContractEnd`) |
| `RemainingDeferred` | Number | Calculated |
| `Outcome` | Text | Calculated |
| `GoLive` | Date | Constant `Date(2026,9,1)` |
| `CutoffEnd` | Date | Constant `Date(2026,8,31)` |

---

## Node flow

```
[Trigger]
   │
   ▼
[Message] "I'll work through this with you. Five facts and I can classify it."
   │
   ▼
[Question] "What are the contract start and end dates?"      → ContractStart, ContractEnd
   │
   ▼
[Condition] ContractEnd < GoLive ?
   ├── Yes ──▶ [Question] "Has all revenue for this contract been recognised in the legacy
   │                       system, with no deferred balance remaining?"
   │              ├── Yes ──▶ [Set] Outcome = "Neither"  ──▶ [Message: Neither] ──▶ [End]
   │              └── No  ──▶ [Set] Outcome = "Exception (contradiction)" ──▶ [Message: Escalate] ──▶ [End]
   └── No ───┐
             ▼
[Condition] ContractStart >= GoLive ?
   ├── Yes ──▶ [Question] "Confirm: no invoice has been issued and no revenue recognised?"
   │              ├── Yes ──▶ [Set] Outcome = "Subscription only" ──▶ [Message: Sub only] ──▶ [End]
   │              └── No  ──▶ continue below
   └── No ───┐
             ▼
[Question] "Through what date has the customer actually been invoiced?"   → BilledThrough
   │
   ▼
[Question] "What amount was billed for that current term?"                → AmountBilledCurrentTerm
   │
   ▼
[Question] "How much revenue was recognised through 31 August 2026?"      → RecognisedThroughCutover
   │
   ▼
[Set] RemainingDeferred = AmountBilledCurrentTerm - RecognisedThroughCutover
   │
   ▼
[Condition]  ── Scenario E trap ──
   RecognisedThroughCutover = 0 AND AmountBilledCurrentTerm = full contract value ?
   ├── Yes ──▶ [Message: Scenario E warning] ──▶ [Set] Outcome = "Exception" ──▶ [End]
   └── No ───┐
             ▼
[Condition] RemainingDeferred <= 0 ?
   ├── Yes ──▶ [Message: Escalate — nothing left to defer, confirm figures] ──▶ [End]
   └── No ───┐
             ▼
[Condition] BilledThrough >= ContractEnd ?
   ├── Yes ──▶ [Set] Outcome = "Cash Sale only"
   │            ──▶ [Message: Cash Sale values] ──▶ [End]
   └── No ───▶ [Question] "When does the next billable term start?"       → NextTermStart
                ──▶ [Set] Outcome = "Both"
                ──▶ [Condition] NextTermStart <= BilledThrough ?
                       ├── Yes ──▶ [Message: OVERLAP ERROR] ──▶ [End]
                       └── No  ──▶ [Message: Split values] ──▶ [End]
```

---

## Power Fx expressions

```powerfx
// Remaining deferred revenue at go-live
Set(RemainingDeferred,
    Topic.AmountBilledCurrentTerm - Topic.RecognisedThroughCutover)

// Cash Sale revenue recognition end date:
// day before the next term starts, else the billed-through date
Set(CashSaleRevEnd,
    If(IsBlank(Topic.NextTermStart),
       Topic.BilledThrough,
       DateAdd(Topic.NextTermStart, -1, TimeUnit.Days)))

// Overlap guard — must be true before presenting a split
Set(NoOverlap,
    Topic.NextTermStart > CashSaleRevEnd)

// Months recognised in legacy, for showing the arithmetic
Set(MonthsRecognised,
    DateDiff(Topic.ContractStart, Date(2026,8,31), TimeUnit.Months) + 1)
```

---

## Message templates

### Outcome: Both

> **Both — Cash Sale and Subscription.**
> Scenario B (Migration Guide) / Scenario 1 (Subscriptions & Cash Sales).
>
> The current term is billed with revenue still deferred, and a later term still needs billing.
>
> **Cash Sales sheet**
> | Column | Value |
> |---|---|
> | T Amount deferred to date | `{RemainingDeferred}` |
> | V Revenue Start Date (Custom) | 01/09/2026 |
> | W Revenue End Date (Custom) | `{CashSaleRevEnd}` |
>
> **4_Subscription sheet**
> | Column | Value |
> |---|---|
> | H Original Start Date | `{NextTermStart}` |
> | I Original End Date | `{ContractEnd}` |
>
> **Arithmetic:** `{AmountBilledCurrentTerm}` billed − `{RecognisedThroughCutover}` recognised
> through 31 Aug 2026 = **`{RemainingDeferred}`** deferred at go-live.
>
> **Checks:** Subscription starts `{NextTermStart}`, after the Cash Sale recognition ends
> `{CashSaleRevEnd}` — no overlap. Do not include the already-billed period in the
> Subscription amount.
>
> ⚠️ Column H is labelled "Original Start Date" but must hold the **next billable term start**,
> not the legacy contract start.

### Outcome: Cash Sale only

> **Cash Sale only.**
> Scenario A (Migration Guide) / Scenario 2 (Subscriptions & Cash Sales).
>
> The contract is billed through its end date, so no further customer billing is required.
> Do not create a Subscription.
>
> | Column | Value |
> |---|---|
> | T Amount deferred to date | `{RemainingDeferred}` |
> | V Revenue Start Date (Custom) | 01/09/2026 |
> | W Revenue End Date (Custom) | `{ContractEnd}` |
>
> **Arithmetic:** `{AmountBilledCurrentTerm}` − `{RecognisedThroughCutover}` = **`{RemainingDeferred}`**

### Outcome: Subscription only

> **Subscription only.**
> Scenario C (Migration Guide) / Scenario 3 (Subscriptions & Cash Sales).
>
> Nothing has been billed and no revenue recognised, so there is no deferred balance to carry
> forward. Do not create a Cash Sale.
>
> | Column | Value |
> |---|---|
> | H Original Start Date | `{ContractStart}` |
> | I Original End Date | `{ContractEnd}` |
>
> Enter the full future billable value.

### Outcome: Neither

> **Neither — do not migrate this contract.**
> Scenario D (Migration Guide) / Scenario 4 (Subscriptions & Cash Sales).
>
> Billing and recognition both completed in the legacy system before go-live. Confirm the
> activity is represented in the cutover trial balance. Do not recreate legacy invoices or
> recognition schedules — that would double-count.

### Outcome: Exception (Scenario E)

> ⚠️ **Stop — this looks like the full contract value, not the remaining deferred balance.**
> Scenario E (Migration Guide).
>
> Entering the full contract value in Cash Sales double-counts revenue already recognised in
> legacy, and may also duplicate future terms that belong in a Subscription.
>
> Before proceeding: recalculate the deferred balance remaining at 01/09/2026, or obtain
> confirmation from the business owner. Split future unbilled terms into a Subscription.
>
> If the allocation cannot be supported from source data, log it as an exception:
> > "Record held for review because the source does not clearly identify the remaining deferred
> > revenue at go-live and/or the boundary between the already-billed term and the next
> > billable term."
>
> Exceptions require a **named business approver**.

### Outcome: Exception (overlap)

> ⚠️ **Period overlap — these figures cannot both be right.**
>
> The next billable term starts `{NextTermStart}`, which is on or before the end of the Cash
> Sale recognition period `{CashSaleRevEnd}`. That would recognise the same period twice.
>
> Confirm the boundary between the already-billed term and the next billable term before entering anything.

---

## Fallback

If the user's contract does not match any branch, route to the generative agent with the
context preserved, and prepend: *"This doesn't match a standard scenario — check the answer
below against the Migration Guide before acting on it."*
