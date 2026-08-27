# Open Contracts Workbook — Field Guidance (Advisory)

Reference for `Volaris Open Contracts_V6.1 Production.xlsx`. In Phase 1 the agent **advises**
on these fields; it does not write to the workbook.

---

## Sheet dependency order

Records must be prepared in this order. Each step feeds the dropdowns of the next.

```
Start Here (select VBU)
   └─> 1 - CustomerTemplate  ──┐
   └─> 2 - NonInventorySaleItemTemplate ──┐
         └─> Billing Schedules ──> 3_Billing_Account ──> 4_Subscription
                                                    └──> Cash Sales
```

A customer that does not exist on `1 - CustomerTemplate` cannot be referenced by a Billing
Account, a Subscription, or a Cash Sale.

---

## Column classes — applies to every sheet

| Class | Rule |
|---|---|
| **Formula / system-generated** | Never type into these. They generate External IDs from other columns. Overwriting one breaks every downstream lookup and produces "Not Found". |
| **Dropdown-bound** | The value must come from the workbook's own list. A value that is not in the list will fail the load even though the cell accepts the text. |
| **Free entry** | The only fields a data steward authors. |

---

## `Start Here`

- Cell **A4** selects the VBU (company). **B4** is its internal ID.
- Every other sheet derives its Subsidiary column and its ID prefixes from A4.
- **One workbook per VBU.** Do not mix business units in one file.
- If the selected VBU has no Document Number Prefix configured, Billing Schedule names display
  **"PREFIX NOT AVAILABLE FOR BU"** and cannot be selected on Billing Accounts. Stop and
  escalate if this appears.

---

## `1 - CustomerTemplate`

| Column | Fill? | Rule |
|---|---|---|
| A Subsidiary, B Subsidiary Internal ID, C ExternalID | ❌ formula | Auto-generated |
| D Existing Customer ID | ✅ | Legacy ERP customer ID. Blank for new customers. Alphanumeric, max 80 chars |
| E CUSTOMER Company Name | ✅ **mandatory** | Max 83 chars. Blank if the customer is an individual |

Customers are already created through the Master Data template pack; they are not imported
again here. This sheet exists so subscriptions and cash sales can reference them.
**Copy the entries from the Master Data workbook exactly** — a mismatch silently produces
"Not Found" rather than an error.

---

## `2 - NonInventorySaleItemTemplate`

| Column | Fill? | Rule |
|---|---|---|
| A, B, C | ❌ formula | Auto-generated |
| D Item Name/Number | ✅ **mandatory** | As it should appear on transactions and item lists. Max 60 chars |

Represents the product or service being sold — SaaS licences, support packages, add-on modules.
Copy from the Master Data workbook exactly.

---

## `Billing Schedules`

Headers are on **row 5**; data begins on **row 6**.

| Column | Fill? | Rule |
|---|---|---|
| A Subsidiary, B External ID, C Prefixed Billing Schedule Name | ❌ formula | Auto-generated from the VBU prefix |
| D Type | ✅ | Default **Charge-Based** |
| E Name | ✅ **mandatory** | Must be unique across the sheet |
| F Recurrence Pattern | ✅ | *Anniversary Bill Date* (based on the billing account start date) or *Fixed Bill Date* (a specific predefined date) |
| G Recurrence Date | conditional | **Required only for Fixed Bill Date** schedules |
| H Recurrence Frequency | ✅ **mandatory** | Daily / Weekly / Monthly / Annually |
| I Repeat Every | ✅ | Number of intervals between bills |
| J Public | ✅ | Default **TRUE** — makes the schedule selectable by any eligible billing account |
| K Recurrence Payment Terms | | Terms for subsequent invoices |
| L In Arrears | | TRUE bills at the end of each period instead of the beginning |
| M Initial Amount, N Recurrence Count, O Initial Payment Terms | ❌ for this template | Standard billing schedules only — leave blank for Charge-Based |

**Frequency encoding — the most common error on this sheet:**

| Business term | Recurrence Frequency | Repeat Every |
|---|---|---|
| Monthly | Monthly | 1 |
| **Quarterly** | **Monthly** | **3** |
| **Twice per year** | **Monthly** | **6** |
| Annually | Annually | 1 |

There is no "Quarterly" option. Selecting Monthly + 3 is the only correct encoding.

---

## `3_Billing_Account`

| Column | Fill? | Rule |
|---|---|---|
| A Customer External ID, B Billing A/C ExternalID, C Existing Customer ID, D Subsidiary | ❌ formula | Auto-generated |
| E Customer | ✅ **mandatory** | From the customer list. This name appears on the invoice |
| F Start Date | ✅ **mandatory** | Service start date of the first subscription on this billing account — the original date the relationship started |
| G Billing Schedule | ✅ **mandatory** | Must already exist (from the Billing Schedules sheet or NetSuite) |
| H Currency | ✅ **mandatory** | Must already exist in NetSuite |
| I Name | conditional — see below | Max 50 chars |
| J Customer Default | | TRUE if this is the default billing account for the customer |
| K Invoice Form | | Invoice transaction form used |
| L Class | | Must already exist in NetSuite |
| M Department | ✅ **mandatory** | Must already exist in NetSuite |
| N Location | | Must already exist in NetSuite |
| O Use Off-Cycle Invoice For Advance Charges | | TRUE allows off-cycle invoices for advance charges |

**Uniqueness rule:** a Billing Account must be unique per customer **unless the billing
frequency or currency differs**, in which case multiple billing accounts for the same customer
are acceptable. The Billing A/C External ID must be unique on the tab.

⚠️ Because the External ID is generated from the customer plus the name, two billing accounts
for the same customer with **column I left blank** will generate identical External IDs and the
load will fail. Whenever a customer needs a second billing account, author a distinguishing
**Name** in column I.

---

## `4_Subscription`

Read this sheet as two blocks. A contract with three products becomes **three rows**.

### Block 1 — subscription header (columns C–Q). Repeat identically on every row of the same subscription.

| Column | Fill? | Rule |
|---|---|---|
| A ExternalID, B Subsidiary | ❌ formula | Auto-generated |
| C Subscription Name | ✅ **mandatory** | Max 50 chars. **Must be unique.** Repeat on all lines of the same subscription |
| D Subscription Line Number | ✅ **mandatory** | Identifies each billable line. First line is 1, then 2, 3, … Unique within the subscription |
| E Customer | ✅ **mandatory** | From the customer list |
| F Billing Account External Id | ✅ **mandatory** | From the billing account list |
| G Initial Term | ✅ **mandatory** | Subscription term |
| H Original Start Date | ✅ **mandatory** | ⚠️ See warning below |
| I Original End Date | ✅ **mandatory** | End of the future billable term supported by the source contract |
| J Co-Term With Subscription | | External ID of another subscription whose end date this must align to |
| K Default Renewal Method | | Only if Initial Term is NOT Evergreen |
| L Default Renewal Term | | Only if Initial Term is NOT Evergreen |
| M Default Renewal Transaction Type | | Only if renewal method is *Create New Subscription* |
| N Automatically Initiate Renewal Process | | TRUE to generate renewals automatically |
| O uplift line number, P uplift % renewal uplift, Q advance renewal period number | | Optional renewal configuration |

> ⚠️ **"Original Start Date" is a misleading label.** For a migrated contract this is the
> **first day of the next future period for which billing and recognition are both pending** —
> *not* the legacy contract start date. In Example 1, the contract starts 01/03/2026 but the
> Subscription Original Start Date is **01/03/2027**. It must never be earlier than the Cash
> Sale's Revenue Recognition End Date.

> ⚠️ **Subscription Amount = future billable value only.** Exclude anything already invoiced in
> the legacy system.

### Block 2 — subscription line (columns R–AQ). One set per item.

| Column | Fill? | Rule |
|---|---|---|
| R Subscription Plan Line Number | ✅ **mandatory** | Start at line 2 for a new subscription |
| S ITEM - External ID | ✅ **mandatory** | From the non-inventory sale item list |
| T LINE TYPE | ✅ **mandatory** | One Time / Recurring / Usage |
| U BILLING MODE | ✅ **mandatory** | In Advance / In Arrears |
| V PRORATE START DATE | ✅ **mandatory** | TRUE to prorate when service starts mid-period |
| W PRORATE END DATE | ✅ **mandatory** | TRUE to prorate when service ends mid-period |
| X DEPARTMENT | ✅ **mandatory** | Must exist in NetSuite |
| Y LOCATION | ✅ **mandatory** | Must exist in NetSuite |
| Z INCLUDE IN RENEWAL SUBSCRIPTION | ✅ **mandatory** | TRUE if this line renews with the subscription |
| AA REVENUE RECOGNITION OPTION | ✅ **mandatory** | See mapping in `04-revenue-recognition-rules.md` |
| AB Discount | | Default discount % |
| AC Included Quantity Multiplier | ❌ | Leave blank |
| AD Quantity | ✅ **mandatory** | Not used for Usage line types |
| AE Charge Frequency | ✅ **mandatory** | *One Time* only available when the plan line type is One Time |
| AF Repeat Every | ✅ **mandatory** | One Time → 0; Weekly → 1, 2, 4; Monthly → 1, 2, 3, 6; Annually → 1, 2, 3 |
| AG overage discount, AH overage frequency, AI overage repeat every | | Usage/overage only |
| AJ Include | ✅ **mandatory** | TRUE if the overage price plan applies |
| AK Price Plan : Currency | ✅ **mandatory** | |
| AL Price Plan : Included Quantity | | Usage line types only |
| AM Price Plan : Type | ✅ **mandatory** | *Tiered* charges each tier at its own price; *Volume* charges all quantity at the highest tier reached |
| AN Price Plan : Above/Starting Quantity | ✅ **mandatory** | Volume → *starting* quantity (inclusive); Tiered → *above* quantity (exclusive) |
| AO Price Plan : Pricing Option | ✅ **mandatory** | *Rate* multiplies quantity by value; *Fixed Amount* charges the value regardless of quantity |
| AP Price Plan : Value | ✅ **mandatory** | Max 2 decimal places |
| AQ First Bill Date | | May differ from the subscription start date depending on contract terms or billing alignment |

---

## `Cash Sales`

| Column | Fill? | Rule |
|---|---|---|
| A Subsidiary Internal ID, B Subsidiary Trim, C Customer External ID | ❌ formula | Auto-generated |
| D External Id | ❌ formula | **Depends on column E.** Stays "Enter Cash Sale #" until E is populated |
| E Cash Sale # | ✅ **mandatory** | Unique per transaction. Alphanumeric, **max 15 characters** |
| F Order Reference Number | | Sales order number from the current ERP |
| G Customer Company Name | ✅ **mandatory** | Must be selected from the customer list |
| H Date | ✅ **mandatory** | Date the invoice was created (mm/dd/yyyy) — ⚠️ **project default not yet confirmed** |
| I Posting Period | ✅ **mandatory** | Format `mmm yyyy`. The period **must be OPEN** |
| J Account | ✅ **mandatory field — LEAVE BLANK** | Populated by the migration team |
| K Department | | |
| L Class | | |
| M PO # | | Customer purchase order number |
| N Memo | | Max 999 characters |
| O Revenue Account | ✅ **mandatory** | Must exist in NetSuite before import |
| P Deferred Revenue Account | ✅ **mandatory** | Must exist in NetSuite before import |
| Q Migration Item | ✅ **mandatory field — LEAVE BLANK** | Determined by the migration team |
| R Quantity | ❌ formula | Always 1 |
| S Currency | ✅ **mandatory** | NetSuite only recognises currency names in the format provided |
| T **Amount deferred to date** | ✅ **mandatory** | ⚠️ See warning below |
| U Description | | Max 999 characters |
| V Revenue Start Date (Custom) | ✅ **mandatory** | **01/09/2026** for all migrated remaining-value Cash Sales |
| W Revenue End Date (Custom) | ✅ **mandatory** | Original end date of the already-billed term. For a multi-year agreement this is normally the day before the next billing term starts |
| X End User | | Optional custom field |
| Y Region | | Optional custom field |
| Z Disable Avalara Tax | ❌ formula | Always TRUE |

> ⚠️ **Column T is the highest-risk field in the workbook.** Enter the exact deferred revenue
> remaining at go-live for the already-billed period. Not the original contract value. Not the
> amount already recognised. An amount equal to the total contract value is Scenario E and must
> be recalculated before submission.

**Account pairing:** the combination of Revenue Account (O) and Deferred Revenue Account (P)
determines which Cash Sale item NetSuite uses. The bank account is determined by the
subsidiary–currency combination. Neither is free text.

---

## Reference sheets — read only

`Lists data`, `Lists data contracts`, and `CoA data` supply every dropdown in the workbook.
Never edit them.

---

## Workbook handling

Before opening the file: **File → Options → Advanced → "When calculating this workbook" →
confirm "Use 1904 date system" is NOT checked.** If it is checked, every date in the workbook
shifts by four years and a day.
