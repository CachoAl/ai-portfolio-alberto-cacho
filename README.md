# AI Automation Portfolio — Finance Operations

**Five recurring finance-operations processes automated end to end, with human-in-the-loop governance designed in from the start.**

🔗 **Live portfolio → [albertocacho.github.io/ai-automation-portfolio](https://albertocacho.github.io/ai-automation-portfolio)**
💼 [LinkedIn](https://www.linkedin.com/in/albertocacho/) · ✉️ alberto.lcacho@hotmail.com

---

## What this is

I lead end-to-end finance operations for a B2B unit of roughly **2,500 active enterprise accounts** — forecasting, aging analysis, recovery strategy, and the automation behind all of it.

This repository hosts an interactive portfolio of the AI-powered tools I built to run that work. It is not a collection of prompts. Each tool is a **precise written procedure** — data source, field names, routing rules, exceptions, and what to do when something is missing — that an AI agent executes from start to finish.

The test I hold each one to: *if a new analyst read the document, they could do the job.*

### Headline results

| Metric | Result |
|---|---|
| Overdue balances across the business unit | **−93%** |
| Forecast accuracy | **+45%** |
| AR communication cycle | **2.5 weeks → 5 days** |
| Enterprise accounts contacted per sitting | **100+**, monthly |
| Open AR reconciled during a pipeline migration | **~$20M**, field by field |
| Customer-facing outputs released unreviewed | **0** |

---

## The five processes

| # | Process | Cadence | What it replaced | Core guardrail |
|---|---|---|---|---|
| 01 | **Monthly AR Communications** | Monthly | A week of manual template selection, invoice-table copy-paste and recipient cleanup across 100+ accounts | Balances must reconcile to the cent before a draft exists |
| 02 | **Forecast Tracking** | Continuous | A spreadsheet that showed *which* accounts were open but never *why* | Never drafts or sends on its own — follow-ups require a click |
| 03 | **Weekly Deactivation Report** | Weekly | Rebuilding two ranked, owner-grouped reports by hand and retyping last week's commentary | Never invents commentary; stops and asks when a classification is unknown |
| 04 | **Ticket Triage & Distribution** | Scheduled weekly | A support queue that aged quietly, read one ticket at a time | A scheduled run **prepares everything and stops** — no closures without approval |
| 05 | **Manual Tax Invoices (AU GST)** | On demand | An infrequent, high-consequence manual calculation | No customer tax registration number → no document at all |

**Also delivered:** deployment of an AI voice agent for outbound collection calls on the high-volume, low-balance segment, as a formal AI Integration Analyst — integrated with the ticketing system so every call outcome produced a traceable follow-up and a defined human escalation path.

---

## Architecture: when a skill becomes a system

A single procedure handles a lot. It runs out at a specific point — **the moment the output of this run becomes an input to the next one.** Two of the five processes crossed that line and became multi-stage systems with state that outlives the session.

```
START OF MONTH — outreach
┌──────────────┐   ┌───────────────┐   ┌──────────────┐   ┌───────────────┐
│  AI: build   │──▶│ AI: route +   │──▶│ 🧑 HUMAN GATE │──▶│ STATE:        │
│  dataset     │   │ render drafts │   │ review & send│   │ day-1 ledger  │
└──────────────┘   └───────────────┘   └──────────────┘   └───────┬───────┘
                                                                  │
ALL MONTH — tracking                                              │
┌───────────────┐   ┌────────────────┐   ┌──────────────┐   ┌─────▼───────┐
│ STATE:        │──▶│ AI: read the   │──▶│ STATE:       │──▶│ 🧑 HUMAN    │
│ day-1 ledger  │   │ customer's own │   │ snapshots +  │   │ dashboard   │
│ (read back)   │   │ email replies  │   │ diagnosis    │   │ button      │
└───────────────┘   └────────────────┘   └──────────────┘   └─────────────┘
```

The day-1 ledger is written by one stage and read by another **a month later**. That handoff is the difference between a tool and a system — and collections is a month-over-month job, so most of the work eventually needs that shape.

Supporting state includes run snapshots (so trajectory is visible, not just today's snapshot), an owner cache that learns and asks rather than guessing, and an immutable archive of every source file — necessary because the upstream data is a daily full refresh with no history.

---

## How I build

1. **Define the problem and the goal** — find what costs the most time or repeats the most; define what success looks like; scope to one problem.
2. **Map the manual process** — write down how the job is done today with no AI, naming the decision made at each step.
3. **Decide where AI belongs** — "copy A into B" is plain automation; "read, summarise, classify, decide, write" is where a model earns its place.
4. **Design the data flow** — what triggers the run, what instruction executes, where the result is allowed to land.
5. **Prototype and test the extremes** — easy cases, hard cases, broken cases. Consistent output, not output that worked once.
6. **Put a human in the loop** — build the approval pause in deliberately; never bolt it on afterwards.
7. **Turn every correction into a written rule** — with the real case attached: the account, the date, the number. The tools get more correct with use instead of decaying.

---

## Governance: the limits, written down

A tool that stops and asks is worth more than one that is confidently wrong. Every procedure carries explicit stopping conditions:

- **Nothing customer-facing sends itself.** Every email, invoice and ticket resolution waits for a human.
- **Never chase a credit balance.** Net-negative accounts are excluded from collection outreach regardless of status.
- **Some accounts are never contacted automatically — and are still reported every month,** including with an explicit all-clear. A watchlist that only speaks up when something breaks is one you stop trusting.
- **Never invent commentary.** A new account shows an empty cell; blank is the honest answer.
- **Never guess a classification.** Unknown owner means the run pauses and asks.
- **A scheduled run prepares and stops.** It never closes, reassigns or comments without explicit approval.
- **No customer tax number, no document.** Not a warning — no output.
- **Never report a number it can't prove.** Implausible source reads and failed reconciliations abort the report and explain why.

---

## Engineering judgment

Rules that exist because something went wrong first, and are now permanent:

- **A truncated read looks like a report.** Reading a large source directly returned roughly one row in fifty, with no error raised. The tools now read a filtered mirror and abort when the row count is implausible.
- **Pre-built summary views can quietly overstate.** One inflated attainment by more than thirty percentage points by including cash outside scope. The check is inverted on purpose: if my totals match that view, I read the wrong one.
- **Don't trust a pre-computed age field.** A warehouse aging column was calculated at period close, not as of today — six weeks stale across every row. Deriving it from the due date reproduced the correct value exactly across 1,000+ documents.
- **Reconcile a new pipeline before trusting it.** Replacing a manual export with a direct query, diffed across 101 accounts and ~$20M in open AR: 25 of 27 fields matched exactly, totals matched to the cent, and the one known difference is documented.
- **Compute configuration is a design choice.** The same query: over 100 seconds on one warehouse setup, 6 seconds on another.
- **Beating a target isn't automatically good news.** In one month, roughly a third of an over-collection came from accounts forecast at zero — a forecasting signal, not a collections win.

---

## Capabilities

**AI & automation design** — agentic workflow design · procedure and instruction design with explicit rule precedence · human-in-the-loop governance and approval gates · multi-stage systems with persistent state · scheduled agents that prepare without executing · degraded-mode fallbacks

**Data & controls** — SQL against a cloud data warehouse (invoice-level modelling, window functions) · pipeline reconciliation and field-by-field validation · pre-output validation gates · source-of-truth definition including explicit "never read this" lists · anonymisation and confidentiality handling in reporting

**Finance operations** — B2B collections and AR at enterprise scale · aging analysis, dunning strategy and escalation design · credit memo and cash-application diagnosis · forecast attainment tracking and variance explanation · indirect tax documentation compliance (AU GST) · workload distribution across a team

**Tools** — Snowflake · NetSuite · Jira · Salesforce-based case systems · Google Workspace and Gmail API automation · Excel automation · self-contained HTML dashboards and reporting

---

## Repository contents

| File | Description |
|---|---|
| `index.html` | The full interactive portfolio. Single file, zero dependencies, no build step. |
| `README.md` | This document. |

**Technical notes:** vanilla HTML, CSS and JavaScript in one file. No frameworks, no package manager, no build pipeline. Scroll-driven reveals and count-up animations use `IntersectionObserver` and `requestAnimationFrame`; the visual layer uses CSS `backdrop-filter`, layered gradients and SVG. Fully responsive, respects `prefers-reduced-motion`, and includes print styles so the page exports cleanly to PDF.

---

## A note on confidentiality

Everything in this portfolio is **anonymised**. It contains no client identities, no colleague names, no internal system or table names, and no ticket references. Amounts appear as ranges or proportions, and all interface examples use synthetic data. The structures, validation logic and governance rules shown are the real ones.

---

## Contact

**Alberto Cacho** — Sr. Collector & B2B Finance Operations Lead · Mexico City
Focused on FP&A and Finance Strategy & Operations roles.

[LinkedIn](https://www.linkedin.com/in/albertocacho/) · alberto.lcacho@hotmail.com
