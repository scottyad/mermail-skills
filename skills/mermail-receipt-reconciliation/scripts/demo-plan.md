# Demo video runbook: Mermail Receipt Reconciliation

Target length: 3 minutes 30 seconds  
Screen recording rules: No real inboxes, names, addresses, wallets, API keys, or seed phrases.

---

## 0:00–0:20 — Problem introduction

- Open a terminal or IDE
- Narrate: "Receipts, invoices, and subscription emails pile up in inboxes. Summarizing them for expense reports is tedious and error-prone. This skill automates the discovery and extraction."
- Show a Mermail inbox with the three demo fixture emails visible in the list

## 0:20–0:45 — MCP connection

- Show the Mermail MCP server connected (e.g., `openclaw mcp list` or Codex `/mcp`)
- Narrate: "The skill runs through the standard Mermail MCP connection. No additional credentials are needed."
- Highlight the `mermail` server status as connected

## 0:45–1:10 — Test receipts in demo mailbox

- Open the demo mailbox and show the three fixture emails:
  - Receipt A: AcmeSoft, $49.99 USD
  - Receipt B: CloudStream, $12.99 USD, next renewal October 25, 2026
  - Receipt C: Payment notice — ambiguous, excluded from total
- Narrate: "Here are three test receipts: a one-time purchase, a subscription renewal, and an ambiguous payment notice."

## 1:10–2:10 — Invoke the skill

- Type the prompt: "Find receipts from the last 30 days and produce an expense report."
- Show the agent selecting the `mermail-receipt-reconciliation` skill
- Show the MCP calls in sequence:
  1. `list_mailboxes` — resolve mailbox
  2. `search_emails` — bounded search with receipt/invoice/order/payment terms
  3. `get_email` — read selected bodies with scan-clean and body cap
- Narrate: "The skill searches with bounded metadata, selects exact emails, reads them safely, and extracts structured data."

## 2:10–2:50 — Review the structured report

- Show the generated Markdown report:
  - Query scope: last 30 days
  - Confirmed USD total: $62.98 (AcmeSoft $49.99 + CloudStream $12.99)
  - Receipt table with merchant, date, amount, currency, reference, type, and source
  - Recurring section: CloudStream $12.99 USD monthly, next renewal October 25, 2026
  - Review-needed section: Payment notice — ambiguous, no amount or receipt ID, excluded from total
- Narrate: "Every row links back to its source email. The report separates confirmed totals, recurring charges, and items needing human review."

## 2:50–3:15 — SKILL.md safety rules and live MCP test

- Open `SKILL.md` in the editor
- Scroll to the Write Safety section
- Highlight:
  - "read-only by default"
  - "draft email only after explicit user confirmation"
  - "never send/delete/pay/transfer/sign"
  - "redact payment card details, OTPs, credentials"
- Narrate: "The skill never sends, pays, or deletes. It only drafts after explicit confirmation. The live Mermail MCP read-only test passed with these exact results."

## 3:15–3:30 — Read-only statement

- Return to the report output
- Narrate: "Mermail Receipt Reconciliation: read-only discovery and structured extraction from your inbox. Safe, auditable, and human-reviewed."
- Fade out

---

## Post-production checklist

- [ ] No real personal data visible
- [ ] No API keys, tokens, or credentials visible
- [ ] Demo mailbox uses fictional data only
- [ ] MCP calls shown are real calls against the demo environment
- [ ] Report output matches the extraction rules
