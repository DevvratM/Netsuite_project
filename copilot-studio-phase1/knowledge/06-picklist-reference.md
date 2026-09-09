# Open Contracts Workbook — Valid Picklist Values

Every value the workbook's dropdowns accept, extracted from `Volaris Open Contracts_V6.1
Production.xlsx` (`Lists data`, `Lists data contracts`).

**A value not on these lists will fail the load, even though the cell accepts the text.**
When advising on a dropdown-bound field, quote a value from here verbatim — never paraphrase,
re-case, or pluralise it.

> These lists are a point-in-time extract. If a steward reports a value that is in the workbook
> but not here, believe the workbook and flag the discrepancy — do not tell them their value is invalid.

---

## Short picklists

### Recurrence Frequency (Billing Schedules col H)

- Daily
- Weekly
- Monthly
- Annually

### Recurrence Pattern (Billing Schedules col F)

- Anniversary Bill Date
- Fixed Bill Date

### Billing Schedule Type (Billing Schedules col D)

- Charge-Based
- Fixed Bid, Interval
- Standard
- Time and Materials

### Standard Billing Schedules (pre-existing in NetSuite)

- Monthly
- Annual Anniversary
- Quarterly
- Six Monthly
- Monthly Anniversary

### Subscription Term (Subscription col G and L)

- 1 Year
- Custom Term
- Evergreen Term

### Subscription Line Type (Subscription col T)

- Recurring
- One Time
- Usage

### Billing Mode (Subscription col U)

- In Advance
- In Arrears

### Charge Frequency (Subscription col AE and AH)

- One Time
- Weekly
- Monthly
- Annually

### Revenue Recognition Option (Subscription col AA)

- Over Subscription term
- One Time

### Include In Renewal Subscription (Subscription col Z)

- Always
- Never
- With a different subscription plan
- With the same subscription plan

### Default Renewal Method (Subscription col K)

- Create New Subscription
- Extend Existing Subscription

### Default Renewal Term (Subscription col L)

- 12 Months
- Evergreen

### Default Renewal Transaction Type (Subscription col M)

- Sales Order
- Opportunity
- Estimate
- Do Not Create Sales Transaction

### Price Plan Type (Subscription col AM)

- Tiered
- Volume

### Pricing Option (Subscription col AO)

- Fixed Amount
- Rate

### Region (Cash Sales col Y)

- APAC
- CALA
- EMEA
- North America

### Boolean

- True
- False

### Currency

- Canadian Dollar
- US Dollar
- British pound
- Euro
- Australian Dollar
- New Zealand Dollar
- Indian Rupee
- Brazilian Real
- Swedish Krona
- United Arab Emirates Dirham
- South African Rand
- Botswana Pula
- Nigerian Naira
- Kenyan Shilling
- Singapore Dollar
- Czech Koruna
- Hong Kong Dollar
- Norwegian Krone
- Indonesian Rupiah
- Malaysian Ringgit
- Russian Ruble
- Chinese Yuan
- Israeli Sheqel
- Dominican Peso
- Mexican Peso

---

## Payment Terms

Used on Billing Schedules columns K (Recurrence Payment Terms) and O (Initial Payment Terms).

`0`, `1% 10 Net 30`, `10D`, `14D`, `15D`, `16D`, `2% 10 Net 30`, `21D`, `28D`, `30`, `30D`, `40D`, `45D`, `50% DP, 50% AD`, `50%DP, 50% NET30`, `50%PREPAY, 50%NET30`, `50D`, `60 NPR`, `60D`, `7D`, `8D`, `90D`, `DUE UPON RECEIPT OF INVOICE`, `Due on receipt`, `END OF MONTH`, `END OF MONTH + 30`, `END OF MONTH + 45`, `END OF MONTH + 60`, `Net 10`, `Net 120`, `Net 14`, `Net 15`, `Net 20`, `Net 21`, `Net 25`, `Net 28`, `Net 30`, `Net 40`, `Net 45`, `Net 5`, `Net 60`, `Net 64`, `Net 7`, `Net 75`, `Net 90`, `Prepayment`

---

## Department

Used on Billing Account col M (mandatory), Subscription col X (mandatory), Cash Sales col K.
Values are a mix of bare numeric codes and `code-name` strings. Use the exact form shown.

`0`, `101`, `102`, `103`, `104`, `105`, `109-Corporate Cost`, `109-M&A`, `181-Admin`, `182-Sales & Marketing`, `183-Development`, `184-Professional Services`, `185-Maintenance`, `202 - Warehouse`, `202-Production`, `211`, `212`, `213`, `214`, `215`, `221`, `222`, `223`, `224`, `225`, `300`, `301`, `302`, `303`, `304`, `305`, `401-Admin`, `402-Sales & Marketing`, `403-Development`, `404-Professional Services`, `405-Maintenance`, `441-Admin`, `442-Sales & Marketing`, `443-Development`, `444-Professional Services`, `445-Maintenance`, `500`, `501`, `502`, `503`, `504`, `505`, `509`, `571-Admin`, `572-Sales & Marketing`, `573-Development`, `574-Professional Services`, `575-Maintenance`, `601-Admin`, `602-Sales & Marketing`, `603-Development`, `604-Professional Services`, `605-Maintenance`, `641`, `642`, `643`, `644`, `645`, `648`, `649`, `691`, `692`, `693`, `694`, `695`, `699`, `700`, `701-Volaris Risk`, `704`, `705`, `800`, `801`, `802`, `803`, `804`, `805`, `811-Volaris IT`, `821-Volaris Legal`, `829- Volaris Legal M&A`, `829-Volaris Legal M&A`, `831-Volaris M&A`, `851-Volaris Tax`, `859-Volaris Tax - M&A`, `900`, `901`, `909`, `911- Volaris HR`, `911-Admin`, `911-Volaris HR`, `912-Sales & Marketing`, `913-Development`, `914-Professional Services`, `915-Maintenance`, `931-Volaris HR NA`, `941-Admin`, `951-Admin`, `952-Sales & Marketing`, `952-Volaris KM`, `953-Development`, `954-Professional Services`, `955-Maintenance`, `961-Admin`, `961-Volaris Strategy`, `962-Sales & Marketing`, `962-Volaris S&M`, `963-Development`, `964-Professional Services`, `965-Maintenance`, `971-Admin`, `971-Volaris Corporate`, `972-Sales & Marketing`, `973-Development`, `974-Professional Services`, `975-Maintenance`, `979-Volaris Corporate M&A`, `980-Admin`, `981-Admin`, `981-Volaris Finance`, `998-Volaris Internal Audit`, `999`

---

## Subsidiary prefixes — READ THIS BEFORE STARTING A VBU

The Billing Schedules sheet builds its External ID and its Prefixed Billing Schedule Name from
the subsidiary's **Document Number Prefix**. A subsidiary with no prefix produces
**"PREFIX NOT AVAILABLE FOR BU"**, and its billing schedules cannot be selected on Billing Accounts.

**Only 66 of the 219 subsidiaries in the workbook have a prefix configured.**
The other 145 will hit this error.

If a steward selects a VBU that is not in the table below, stop and escalate to the Data
Workstream Lead to have a prefix configured before any billing schedule work begins.

| Subsidiary | Prefix |
|---|---|
| APRAIS Ltd | `DEC-APR` |
| Accent Technologies Inc | `ACC` |
| Advanced Business Link | `ABL` |
| Aislelabs Inc | `AIS-INC` |
| Aislelabs Inc FZ LLC | `AIS-FZ` |
| Aislelabs Information Technology Services LLC | `AIS-ITS` |
| Aislelabs Limited | `AIS-LTD` |
| AssetWorks - Facilities | `AWF-FAC` |
| AssetWorks Australia | `AWF-AU` |
| AssetWorks Canada Inc. | `AWC-CAD` |
| Assetworks Inc | `AW-INC` |
| Assetworks UK | `AW-UK` |
| Bravura Security Inc. | `BRA` |
| Calhoun Group | `BRU-CAL` |
| Carl Bruce Portfolio Corporate | `BRU-CORP` |
| Clarity Group Inc. | `CLA` |
| Constellation Solutions Inc SBC | `INT-CSI` |
| Curbstone | `INT-CUR` |
| Decideware Consolidated | `DEC-CON` |
| Decideware Development Pty Ltd | `DEC-DEV` |
| Decideware Inc | `DEC-INC` |
| Dispatching Solutions Inc | `DSI` |
| E-Surveillance Limited | `SUR-ESU` |
| FacilityForce | `FF` |
| GoAssetWorks Limited | `AWF-GAW` |
| Healthcare Group Corporate | `TUR-HEA` |
| Helm Operations Software Inc. | `HEL` |
| Hospitality Group Corporate | `TUR-HOS` |
| InfoManager Inc | `INF` |
| Lightbox Analytics Ltd | `AW-LBX` |
| MarTech Group Corporate | `TUR-MAR` |
| Medaptus Solutions, Inc. | `MED` |
| Multiforce Systems | `MLF` |
| Orion Software Inc | `ORI` |
| Parlance Corporation | `PAR` |
| Red River Software | `RED` |
| Rental Result UK | `WYN-UK` |
| Rental Result US | `WYN-US` |
| RouteVision Belgium | `ROU-BEL` |
| RouteVision Netherland | `ROU-NED` |
| RouteVision Services | `ROU-SER` |
| Security Group Corporate | `BRU-SEC` |
| SoftChalk | `AWF-SC` |
| Squirrel Systems US Inc | `SQU-US` |
| Squirrel Systems of Canada | `SQU-CAD` |
| SureView Holdings Limited | `SUR-HLD` |
| SureView Sytems Limited | `SUR-LTD` |
| SureView Sytems, Inc. | `SUR-INC` |
| Topaz | `TPZ` |
| Tribute Inc | `TRI` |
| Turner Portfolio Corporate | `TUR-CRP` |
| VERSTRA | `VOL-VER` |
| VOLARIS GROUP | `VOL-GRP` |
| VOLARIS SOFTWARE INC. | `VOL-SWR` |
| Vision Group | `ROU-GRP` |
| Volaris Dutch Holdco | `VOL-DUT` |
| Volaris Group Australia Pty Ltd. | `VOL-AUS` |
| Volaris Group Canada Holdings Inc | `VOL-CAD` |
| Volaris India | `VOL-IND` |
| Volaris NA Holdco ULC | `VOL-NA` |
| Volaris US HoldCo Inc | `VOL-US` |
| WebCheckout | `AWF-WCK` |
| Wennsoft Inc | `WEN` |
| Windward Software Systems Inc | `WIN` |
| Wynne InTempo | `WYN-INT` |
| Wynne Systems Inc | `WYN-SYS` |

> Data note: `Decideware Inc` appears in the prefix table but not in the subsidiary list.

---

## Lists that are NOT static

These dropdowns are generated from the workbook's own sheets as the steward fills them in.
There is no fixed list of valid values, and this agent cannot verify them:

| Dropdown | Generated from |
|---|---|
| Customer | `1 - CustomerTemplate` column E |
| ITEM - External ID | `2 - NonInventorySaleItemTemplate` column C |
| Billing Account External Id | `3_Billing_Account` column B |
| Co-Term With Subscription | `4_Subscription` column A |
| Billing Schedule (Billing Account col G) | the standard list above **plus** custom rows from the `Billing Schedules` sheet |

If asked whether a specific customer or item exists, say you cannot verify it and point the
steward to the Master Data workbook. Do not guess, and do not offer a similar-looking name.

---

## Accounts — not listed here

Cash Sales column O (Revenue Account) and column P (Deferred Revenue Account) draw from the
workbook's `CoA data` sheet, which holds over 1,600 accounts. It is too large to reproduce and
changes independently. Tell the steward to select from the dropdown in the workbook, and
remind them that the O + P combination determines which Cash Sale item NetSuite uses.
