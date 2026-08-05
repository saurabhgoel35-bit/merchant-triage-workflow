# Merchant Triage Classifier (n8n)

An LLM-based support-request classifier built in n8n, self-hosted. It reads an
incoming merchant support request and labels it **Urgent** or **Not urgent**,
then either records the result or files a ticket.

## Design
- **Two entry points:** a live path (form submission → classify → file ticket)
  and an eval path (labelled test case → classify → score against expected).
- **Structured-output guardrails:** the classifier is constrained to a fixed
  label schema so downstream steps never receive free text.
- **Evaluation harness:** 25 labelled cases, including deliberately adversarial
  ones — polite phrasing on real incidents, alarming phrasing on trivial
  requests, keyword traps.

## What the evals caught
On one case — a merchant going live in four days with failing sandbox tests —
the classifier disagreed with my label. On review, the classifier was right and
my prompt was underspecified: it had no rule for deadlines. I added an explicit
go-live rule rather than "fixing" the model. The eval surfaced a gap in my own
policy, not the model's reasoning.

## Files
- `merchant-triage-workflow.json` — the n8n workflow
- `merchant_triage_evals.csv` — the 25-case evaluation set
