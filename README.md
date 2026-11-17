# CloseBooks

**AI-assisted month-end close for CPA firms.** Upload a client's bank statement, let Claude suggest an account from the client's chart of accounts for every transaction, review only the exceptions, and export a QuickBooks-style CSV.

**Live demo:** https://closebooks-app.vercel.app/demo (no account needed, runs on sample data)

![CloseBooks landing page](docs/landing.jpg)

## The problem

Small accounting firms spend a large share of every month-end close on manual transaction categorization: reading cryptic bank descriptions like `AMZN*MKT*7H3K9` or `GOOG*ADS-9284` and mapping each one to the right account. It is repetitive, error-prone and hard to scale, and many small firms still do it by hand.

## What CloseBooks does

```
Bank statement (CSV / PDF)
        │
        ▼
   1. Parser ──────────── turns messy bank exports into clean transactions
        │
        ▼
   2. Firm rules ──────── vendor rules saved from past corrections are applied first
        │
        ▼
   3. AI categorizer ──── Claude suggests an account from the client's chart,
        │                 with a confidence score and its reasoning
        ▼
   4. COA validator ───── every suggestion must resolve to a real account
        │                 in the client's chart of accounts
        ▼
   5. Review queue ────── low-confidence or invalid rows wait for a human
        │                 (bulk approve, inline edit, undo, audit trail)
        ▼
   6. Exporter ────────── QuickBooks-style CSV, standard CSV, printable close report
```

![Review queue](docs/review-queue.jpg)

## Design decisions

- **Human review before export.** Rows below the auto-approve threshold (93% confidence by default), or that don't map to the client's chart, are held for review. Export is blocked until every row is approved and valid. The AI proposes and the accountant decides.
- **Firm memory.** When a reviewer corrects a category, it can be saved as a vendor rule (e.g. `"DoorDash*" → Meals`). Rules run before the AI on later closes, and recent corrections are passed to the model as hints, so the system gets better for each firm.
- **Trust layer.** Chart-of-accounts validation catches suggestions that look plausible but don't exist in the client's books, a common failure mode for LLM categorizers.
- **Firm-scoped data.** Signed-in firm workspaces with row-level security scoping client data to the firm, plus session timeouts after inactivity.

## Features

- CSV and PDF bank statement import
- Claude-powered categorization with confidence scores and reasoning
- Chart-of-accounts validation
- Saved vendor rules and correction hints
- Exception review queue with bulk approve and undo
- QuickBooks-style and standard CSV export, printable close report
- Multi-client firm workspace
- Stripe subscription billing and a 14-day trial

## Tech

Web app deployed on Vercel · Anthropic Claude API for categorization · Stripe for billing · database row-level security for firm isolation

## Status

Early-stage product, live as a demo. No paying customers yet. Co-founded by Sergey Gruzd and Ilia Duda.

## Source code

The production codebase is kept private. This repository documents the product, the architecture and the design decisions. Happy to walk through the code in an interview or at a hackathon.
