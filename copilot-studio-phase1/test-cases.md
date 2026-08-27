# Phase 1 Test Cases

Run every case before releasing to data stewards. Cases 1–6 are drawn from the source
documents, so the expected answers are verifiable. Cases 7–12 are the traps.

**Pass criteria:** correct outcome, correct amounts and dates, both scenario identifiers cited,
arithmetic shown, no invented figures.

---

## TC-01 — Multi-year, current term billed (source: Subs & Cash Sales Scenario 1)

**Input**
> 2-year contract 01/03/2026–28/02/2028, $2,400 total, $100/month. Year 1 ($1,200) billed
> upfront in legacy. Year 2 not yet billed.

**Expected**
- Outcome: **Both**
- Scenario B (Guide) / 1 (Subs & Cash Sales)
- Cash Sale $600, rev rec 01/09/2026 – 28/02/2027
- Subscription start 01/03/2027, $1,200, end 28/02/2028
- Arithmetic shown: $1,200 − $600 (6 months Mar–Aug) = $600

---

## TC-02 — Fully billed upfront (source: Subs & Cash Sales Scenario 2)

**Input**
> 2-year contract 01/03/2026–28/02/2028, $2,400 billed upfront on 01/03/2026, $100/month.

**Expected**
- Outcome: **Cash Sale only**
- Scenario A (Guide) / 2 (Subs & Cash Sales)
- Cash Sale **$1,800**, rev rec 01/09/2026 – 28/02/2028
- No Subscription
- ⚠️ Must use $1,800, not the $1,900 in the source prose. If the user quotes $1,900, the agent
  should explain the source's table and prose disagree and the table is correct.

---

## TC-03 — Future contract (source: Subs & Cash Sales Scenario 3)

**Input**
> 1-year contract 01/09/2026–31/08/2027, $1,200 total. Nothing billed, nothing recognised.

**Expected**
- Outcome: **Subscription only**
- Scenario C (Guide) / 3 (Subs & Cash Sales)
- Subscription start 01/09/2026, $1,200, end 31/08/2027
- Explicitly: no Cash Sale, because there is no deferred balance

---

## TC-04 — Completed contract (source: Subs & Cash Sales Scenario 4)

**Input**
> 2-year contract 01/03/2024–28/02/2026, $2,400 billed upfront, fully recognised.

**Expected**
- Outcome: **Neither**
- Scenario D (Guide) / 4 (Subs & Cash Sales)
- Points to the cutover trial balance
- Warns that migrating it would double-count

---

## TC-05 — Annual term, ≤ 1 year (source: Subscriptions vs Cash Sales workbook, Scenario 1)

**Input**
> Contract 01/04/2026–31/03/2027. $12,000 billed April 2026, $1,000/month.
> $5,000 recognised by go-live.

**Expected**
- Outcome: **Cash Sale only**
- Cash Sale $7,000, rev rec 01/09/2026 – 31/03/2027
- No Subscription
- Arithmetic: $12,000 − $5,000 = $7,000 over 7 months (Sep-26 – Mar-27)

---

## TC-06 — Multi-year, annual billing (source: Subscriptions vs Cash Sales workbook, Scenario 2)

**Input**
> Contract 01/04/2026–31/03/2028. Year 1 $12,000 billed April 2026, $5,000 recognised by
> go-live. Year 2 $12,000 to be billed April 2027.

**Expected**
- Outcome: **Both**
- Cash Sale $7,000, rev rec 01/09/2026 – 31/03/2027
- Subscription start 01/04/2027, $12,000
- Confirms no overlap: recognition ends 31/03/2027, subscription starts 01/04/2027

---

## TC-07 — Scenario E trap ⚠️

**Input**
> Contract 01/03/2026–28/02/2028, total value $2,400. Put $2,400 in the cash sale, right?

**Expected**
- Outcome: **Exception — must NOT confirm $2,400**
- Explains this double-counts revenue already recognised in legacy
- Asks for revenue recognised through 31/08/2026 in order to recalculate
- Offers the standard exception note and mentions a named business approver

---

## TC-08 — Overlap trap ⚠️

**Input**
> Cash sale recognition runs 01/09/2026 to 31/03/2027 and the subscription starts 01/03/2027.

**Expected**
- Flags the **period overlap** as an error in the first sentence
- Explains March 2027 would be recognised twice
- Does not produce template values until the boundary is corrected

---

## TC-09 — Contradiction trap ⚠️

**Input**
> Contract ended 30/06/2026 but we still show $4,000 of deferred revenue on it.

**Expected**
- Outcome: **Exception / escalate**
- Names the contradiction: the contract ended before go-live but a deferred balance remains
- Does not force it into Scenario D
- Routes to the Functional Workstream Lead

---

## TC-10 — Missing facts

**Input**
> Customer Acme, $50,000 contract. Which template?

**Expected**
- Does **not** classify
- Asks for the missing facts one or two at a time, not as a wall of questions
- Does not guess dates or recognition figures

---

## TC-11 — Open item, no invented default ⚠️

**Input**
> What date should I put in the Cash Sale Date column?

**Expected**
- States this is an **unresolved open item** requiring production confirmation
- Mentions that contract start date was discussed as possibly acceptable but is not confirmed
- Routes to the Functional Workstream Lead
- Does **not** state a default as if it were settled

---

## TC-12 — Write refusal ⚠️

**Input**
> Great, go ahead and add that to the Cash Sales tab for me.

**Expected**
- Clearly states it cannot modify the workbook
- Offers the values in a copy-ready form instead
- Does not claim to have written anything

---

## Additional knowledge-recall checks

| # | Question | Must answer |
|---|---|---|
| K-1 | "How do I set up quarterly billing?" | Recurrence Frequency **Monthly** + Repeat Every **3**. There is no Quarterly option |
| K-2 | "Customer needs two billing accounts — anything to watch?" | Allowed when frequency or currency differs; must author a distinct **Name** (col I) or the External IDs collide |
| K-3 | "What rev rec option for a Recurring – Fixed line?" | Volaris \| Fixed Recurring Fee, plans on Revenue Arrangement Creation, allocation Normal |
| K-4 | "What goes in the Account column on Cash Sales?" | Leave blank — populated by the migration team |
| K-5 | "Which sheet do I fill first?" | Customers and items, then Billing Schedules, then Billing Accounts, then Subscriptions and Cash Sales |
| K-6 | "What's the max length of a Cash Sale number?" | 15 characters, alphanumeric, unique |
| K-7 | "Scenario A or Scenario 1 — same thing?" | No. Guide A = doc 2; Guide B = doc 1. Crossed |
| K-8 | "Should I recreate the legacy invoices?" | No — remaining value approach only |
| K-9 | "What's a Commit line's rev rec rule?" | Ambiguous — escalate |
| K-10 | "Why does my billing schedule say PREFIX NOT AVAILABLE FOR BU?" | The VBU has no Document Number Prefix configured; escalate before continuing |

---

## Regression note

Re-run TC-02, TC-07, TC-08 and TC-11 after **any** change to the knowledge sources. Those four
cover the failure modes that cost money: wrong deferred balance, full-value double count,
period overlap, and inventing an unconfirmed default.
