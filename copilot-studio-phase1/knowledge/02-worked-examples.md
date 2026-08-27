# Open Contract Migration — Worked Examples

Every example assumes **go-live 1 September 2026**, straight-line (ratable) recognition,
and the remaining value approach. Legacy recognises through **31 August 2026**.

These examples are the calibration set. When classifying a real contract, find the example
it most resembles and follow the same arithmetic.

---

## Example 1 — Multi-year, current term billed, future term unbilled
### (Migration Guide **Scenario B** = Subs & Cash Sales doc **Scenario 1**) → **BOTH**

**Facts**
- Contract term: 2 years, 01/03/2026 – 28/02/2028
- Total contract value $2,400 = $100/month
- Year 1 ($1,200) billed upfront in legacy using annual billing
- Year 2 ($1,200) not yet billed; will be billed from NetSuite after go-live

**Arithmetic**
- Year 1 covers Mar-26 – Feb-27
- Recognised in legacy: Mar, Apr, May, Jun, Jul, Aug 2026 = 6 months × $100 = **$600**
- Remaining deferred at go-live = $1,200 − $600 = **$600**

**Output**
| Object | Values |
|---|---|
| Cash Sale | Amount **$600**, dated 01/09/2026, Rev Rec Start **01/09/2026**, Rev Rec End **28/02/2027** |
| Subscription | Start **01/03/2027**, value **$1,200**, covering Year 2 (Mar-27 – Feb-28) |

The Cash Sale recognises $100/month Sep-26 → Feb-27. The Subscription bills and recognises
$100/month Mar-27 → Feb-28. **No overlap.**

---

## Example 2 — Fully billed upfront, recognition in progress
### (Migration Guide **Scenario A** = Subs & Cash Sales doc **Scenario 2**) → **CASH SALE ONLY**

**Facts**
- Contract term: 2 years, 01/03/2026 – 28/02/2028
- Total contract value $2,400 = $100/month
- Entire $2,400 billed upfront in legacy on 01/03/2026
- No future billing required in NetSuite

**Arithmetic**
- Recognised in legacy: Mar – Aug 2026 = 6 months × $100 = **$600**
- Remaining deferred at go-live = $2,400 − $600 = **$1,800**

**Output**
| Object | Values |
|---|---|
| Cash Sale | Amount **$1,800**, dated 01/09/2026, Rev Rec Start **01/09/2026**, Rev Rec End **28/02/2028** |
| Subscription | **None** — the whole contract is already billed |

> ⚠️ **Source discrepancy.** The prose of the *Subscriptions & Cash Sales* document states
> "5 months ($500) recognised" and a $1,900 remaining balance for this example, while its own
> monthly table shows 6 months ($600) recognised and a $1,800 balance. The table is correct and
> consistent with the recognition timing convention (legacy recognises through 31 August 2026).
> **Use $1,800.** Do not quote the $1,900 figure. See `05-open-items-and-known-issues.md`.

---

## Example 3 — Contract starts on or after go-live
### (Migration Guide **Scenario C** = Subs & Cash Sales doc **Scenario 3**) → **SUBSCRIPTION ONLY**

**Facts**
- Contract term: 1 year, 01/09/2026 – 31/08/2027
- Total contract value $1,200 = $100/month
- No billing has occurred in legacy; no revenue recognised

**Output**
| Object | Values |
|---|---|
| Subscription | Start **01/09/2026**, full contract value **$1,200**, term through 31/08/2027 |
| Cash Sale | **None** — there is no deferred revenue balance to migrate |

SuiteBilling generates the recurring charges and invoices; ARM recognises revenue over the term.

---

## Example 4 — Contract completed before go-live
### (Migration Guide **Scenario D** = Subs & Cash Sales doc **Scenario 4**) → **NEITHER**

**Facts**
- Contract term: 2 years, 01/03/2024 – 28/02/2026 (ended before go-live)
- $2,400 billed upfront in legacy on 01/03/2024
- Fully recognised by go-live; no deferred balance, no future billing

**Output**

Nothing is migrated. No Cash Sale, no Subscription. Excluding the contract prevents duplicate
billing and duplicate revenue recognition. Confirm the completed activity is represented in the
cutover trial balance.

---

## Example 5 — Annual term, billed upfront, term ≤ 1 year
### (*Subscriptions vs Cash Sales* workbook, Scenario 1) → **CASH SALE ONLY**

**Facts**
- Contract 01/04/2026 – 31/03/2027 (source states end as 01/04/2027 — see convention note below)
- $12,000 billed in April 2026, $1,000/month
- Recognised in legacy: Apr, May, Jun, Jul, Aug 2026 = 5 months × $1,000 = **$5,000**

**Arithmetic**
- Remaining deferred at go-live = $12,000 − $5,000 = **$7,000**
- Remaining service months: Sep-26 → Mar-27 = 7 months × $1,000 ✓

**Output**
| Object | Values |
|---|---|
| Cash Sale | Amount **$7,000**, Rev Rec Start **01/09/2026**, Rev Rec End **31/03/2027** |
| Subscription | **None** — the full term is already billed |

> ⚠️ **Source wording error.** The workbook annotates this $7,000 as "this 7000 portion that's
> unbilled becomes cash sales." That is wrong terminology. The $7,000 is **billed but not yet
> recognised** — i.e. deferred revenue. Cash Sales carry *billed-and-deferred* amounts.
> *Unbilled* amounts go to Subscriptions. Never describe a Cash Sale amount as "unbilled."

---

## Example 6 — Multi-year, annual billing, term > 1 year
### (*Subscriptions vs Cash Sales* workbook, Scenario 2) → **BOTH**

**Facts**
- Contract 01/04/2026 – 31/03/2028 (source states end as 01/04/2028)
- Year 1 $12,000 billed April 2026, $1,000/month; $5,000 recognised by go-live
- Year 2 $12,000 to be billed April 2027 — unbilled and unrecognised at go-live

**Output**
| Object | Values |
|---|---|
| Cash Sale | Amount **$7,000**, Rev Rec Start **01/09/2026**, Rev Rec End **31/03/2027** |
| Subscription | Start **01/04/2027**, value **$12,000**, covering Year 2 |

The Cash Sale recognition window ends the day before the Subscription starts. **No overlap.**

---

## Contract end date convention

The *Subscriptions vs Cash Sales* workbook states contract end dates as the **first day after**
the last day of service (e.g. "01/04/2027" for a term whose last service month is March 2027).

For template entry, the **Revenue Recognition End Date must be the last day of service**
(31/03/2027 in that example), not the following day. Entering the following day adds a day of
recognition and can create a one-day overlap with the Subscription start.

Confirm the convention used in each source extract before entering dates.

---

## Quick calibration table

| If the contract… | Then |
|---|---|
| ended before 01/09/2026 and is fully recognised | Neither |
| starts on/after 01/09/2026 and is unbilled | Subscription only |
| is billed for its entire remaining term, with deferred revenue left | Cash Sale only |
| has a billed current term plus an unbilled future term | Both — split at the term boundary |
| has an amount that equals total contract value | Exception — recalculate before proceeding |
