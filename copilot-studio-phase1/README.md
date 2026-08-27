# Phase 1 — Open Contracts Migration Advisor

A **read-only** Copilot Studio agent that helps Volaris Wave 2 data stewards decide whether an
in-flight contract migrates as a Cash Sale, a Subscription, both, or neither — and explains how
to complete the Open Contracts template.

No write access, no connectors, no Power Automate. That is the point of this phase: it removes
most of the rework risk with none of the load risk, and it is where you find out whether the
rules are actually consistent before you automate them.

---

## What's in here

```
copilot-studio-phase1/
├── README.md                      ← you are here; build steps
├── agent-instructions.md          ← paste into Copilot Studio → Instructions
├── knowledge/
│   ├── 01-scenario-classification.md    ← the decision rules (authoritative)
│   ├── 02-worked-examples.md            ← the six calibration examples
│   ├── 03-template-field-guidance.md    ← every sheet, every column
│   ├── 04-revenue-recognition-rules.md  ← rev rec rule mapping
│   └── 05-open-items-and-known-issues.md ← contradictions, open decisions, glossary
├── topics/
│   └── classify-contract.md       ← deterministic classifier topic spec
└── test-cases.md                  ← 12 test cases + 10 recall checks
```

---

## Why a rewritten knowledge pack instead of the original files

**Upload the authored markdown as primary knowledge. Keep the originals as secondary.**

Three reasons:

1. **The scenario tables flatten into noise.** The monthly timeline tables in
   *Volaris – Subscriptions & Cash Sales* extract as bare cell sequences —
   `Mar-26 / $1,200 / $100 / $1,100 / - / - / - / -` — with no column headers attached to the
   values. A grounded agent reading that will produce confident, wrong numbers.
2. **The two `.xlsx` reference files ground badly.** `Subscriptions vs Cash Sales.xlsx` is a
   month-by-month grid whose meaning lives in cell adjacency, not in text.
3. **The sources contradict each other**, and knowledge retrieval has no way to arbitrate.
   Crossed scenario numbering, a $1,900/$1,800 arithmetic conflict, and "unbilled" used to
   describe a billed-but-deferred amount. The authored pack resolves each one explicitly and
   records the discrepancy.

The originals still belong in the agent — for provenance, and so a steward can be pointed at
the source document. They just should not be the only grounding.

---

## Build steps

### 1. Create the agent

Copilot Studio → **Create** → **New agent** → name it **Open Contracts Migration Advisor**.
Use the description in `agent-instructions.md`.

### 2. Paste the instructions

Copy the fenced block from `agent-instructions.md` into **Overview → Instructions**.

### 3. Add knowledge

Upload the five files from `knowledge/`.

> **Check file-type support in your tenant first.** If `.md` upload isn't accepted, convert to
> `.docx` or `.pdf` — the content is what matters, not the container. The more robust route is a
> SharePoint document library added as a knowledge source, which also gives you version control
> and lets you update the pack without touching the agent.

Then add the original documents as secondary knowledge:
`Open Contracts _ Subscription  Cash Sales Migration Guide.docx`,
`Volaris - Subscriptions & Cash Sales.docx`,
`Volaris - Production Cutover & Data Migration Playbook 1.0.docx`,
`FAQ.docx`.

### 4. Turn off general knowledge

**Settings → Generative AI → disable general knowledge / web search.** This agent must answer
only from the migration documents. Leaving world knowledge on is how you get plausible NetSuite
advice that contradicts the project's approach.

### 5. Build the classifier topic

Create a topic from `topics/classify-contract.md`. Trigger phrases, variables, node flow,
Power Fx and message templates are all specified there.

The generative agent handles "how do I…" questions. This topic handles "what do I do with this
contract?" — because that answer has to be identical every time the same facts are supplied.

### 6. Test

Work through `test-cases.md`. **TC-02, TC-07, TC-08 and TC-11 are the ones that matter** — they
cover wrong deferred balance, full-value double counting, period overlap, and inventing an
unconfirmed default.

### 7. Publish narrowly

Publish to a private Teams channel for the Wave 2 data stewards. Authenticate with Entra ID and
restrict to that group.

---

## Definition of done for Phase 1

- [ ] All 12 test cases pass
- [ ] All 10 knowledge recall checks pass
- [ ] Agent refuses to state a Cash Sale transaction date default (TC-11)
- [ ] Agent refuses to write to the workbook (TC-12)
- [ ] Agent never produces a figure it wasn't given, and always shows arithmetic
- [ ] Two data stewards have run five real contracts through it and agreed with the outcome
- [ ] The three open items in `05-open-items-and-known-issues.md` are logged with owners

That last one is the real deliverable of this phase. Phase 2 writes to a spreadsheet — you do
not want to automate a rule that is still under discussion.

---

## Open items to resolve before Phase 2

| # | Item | Owner |
|---|---|---|
| 1 | Cash Sale invoice/transaction date default (Cash Sales col H) | Functional Workstream Lead |
| 2 | Billing Schedules migration method — currently "TBD" on the Start Here tab | Data Workstream Lead |
| 3 | Contract end date convention — last service day vs. the day after | Functional Workstream Lead |

---

## What Phase 1 deliberately does not do

- Write to `Volaris Open Contracts_V6.1 Production.xlsx`
- Read customer or item lists from the Master Data workbook
- Validate against NetSuite
- Track which contracts have been processed

Those are Phases 2–5. Phase 1 is worth shipping on its own: the classification decision is where
the expensive errors happen, and it needs no write access to fix.
