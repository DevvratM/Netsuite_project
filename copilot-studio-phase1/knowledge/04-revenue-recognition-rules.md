# Revenue Recognition Rules Reference

Source: `Revenue Recognition Rules Explained.xlsx`. Used to advise on the
**REVENUE RECOGNITION OPTION** field (column AA) of the `4_Subscription` sheet.

---

## SuiteBilling rev rec settings — the mapping to use

Select the revenue recognition rule from the **subscription line type**. Do not ask the data
steward to choose a rule directly; derive it.

| Subscription Line Type | Revenue Recognition Rule | Create Revenue Plans On | Allocation Type |
|---|---|---|---|
| Recurring – Adjustable | Volaris \| Adjustable Recurring Fee | Subscription Events | Normal |
| Recurring – Fixed | Volaris \| Fixed Recurring Fee | Revenue Arrangement Creation | Normal |
| One-Time | Volaris \| Fixed Recurring Fee | Revenue Arrangement Creation | Normal |
| Usage | Volaris \| Usage | Subscription Events | Exclude |
| Prepaid | Volaris \| Usage | Subscription Events | Exclude |
| Commit | Volaris \| Adjustable Recurring Fee **or** Volaris \| Fixed Recurring Fee | Subscription Events | Normal or Exclude |
| Overage | Volaris \| Usage | Subscription Events | Exclude |

**Commit lines are the only ambiguous case** — they can take either rule and either allocation
type. Escalate a Commit line rather than guessing.

---

## Full rule catalogue

| Rule | Create Revenue Plans On | Recognition Method | Rev Rec Start Source | Rev Rec End Source |
|---|---|---|---|---|
| Volaris – 12 Month Plan | Revenue Arrangement Creation | Straight-line, by even periods | Revenue Element Start Date | Term in Months |
| Volaris – Adjustable Recurring Fee | Subscription Events | Straight-line, by even periods | Subscription Event Start Date | Subscription Event End Date |
| Volaris – Adjustable Recurring Fee – MultiYear | Revenue Arrangement Creation | Straight-line, by even periods | Revenue Element Start Date | Revenue Element End Date |
| Volaris – Adjustable Recurring Fee – prorate first & last period | Subscription Events | Straight-line, prorate first & last period | Subscription Event Start Date | Subscription Event End Date |
| Volaris – Adjustable Recurring Fee – Using Exact Days | Subscription Events | Straight-line, using exact days | Subscription Event Start Date | Subscription Event End Date |
| Volaris – At Billing | Billing | Straight-line, by even periods | Event Date | Event Date |
| Volaris – At Fulfillment | Fulfillment | Straight-line, by even periods | Event Date | Event Date |
| Volaris – Event-Percent Complete | Project Progress | Straight-line, by even periods | Event Date | Event Date |
| Volaris – Fixed Recurring Fee | Revenue Arrangement Creation | Straight-line, by even periods | Revenue Element Start Date | Revenue Element End Date |
| Volaris – Fixed Recurring Fee – 21% Carve Out | Revenue Arrangement Creation | Straight-line, by even periods | Revenue Element Start Date | Revenue Element End Date |
| Volaris – Fixed Recurring Fee – prorate first & last period | Revenue Arrangement Creation | Straight-line, prorate first & last period | Revenue Element Start Date | Revenue Element End Date |
| Volaris – Fixed Recurring Fee – Using Exact Days | Revenue Arrangement Creation | Straight-line, using exact days | Revenue Element Start Date | Revenue Element End Date |
| Volaris – Ratable | Revenue Arrangement Creation | Straight-line, by even periods | Revenue Element Start Date | Revenue Element End Date |
| Volaris – Ratable using Exact Days | Revenue Arrangement Creation | Straight-line, using exact days | Revenue Element Start Date | Revenue Element End Date |
| Volaris – Usage | Subscription Events | Straight-line, by even periods | Event Date | Event Date |
| Project Revenue Recognition Rule | Project Revenue Event | Straight-line, by even periods | Event Date | Event Date |

All rules use *Rev Rec Amount Source Type: Event-Percent based on amount*, except
**At Fulfillment**, which uses *Event-Percent based on quantity*.

All rules use *Rev Rec End Date Change Impact: Update Remaining Periods Only* and
*Reforecast Method: Remaining Periods*, except **Volaris – Usage** (Reforecast: Next Period)
and **Project Revenue Recognition Rule** (Change Impact: Update All Periods).

Rules marked "SuiteBilling Only" apply to subscriptions, not to transactions or projects.

---

## For migrated Cash Sales

Cash Sales do not use a subscription line rev rec option. NetSuite generates the Revenue
Arrangement and Revenue Recognition Plan automatically from the transaction data and the item
configuration, using the **Revenue Start Date (Custom)** and **Revenue End Date (Custom)**
entered on the Cash Sales sheet.

The appropriate Cash Sale item is determined by the migration team from the combination of the
**Deferred Revenue Account** and **Revenue (income) Account** supplied in the template.
