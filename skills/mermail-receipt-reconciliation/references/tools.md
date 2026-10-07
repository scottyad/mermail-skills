# Mermail receipt reconciliation tool contract

Read this reference when constructing MCP calls for receipt discovery, bounded content reading, and structured extraction.

## Native MCP envelope

Use the exact tool identifier exposed by the current host. Claude may expose `Mermail:search_emails`; another host may use a different namespace or bare `search_emails`. Do not manually add, strip, or invent a prefix.

Pass `query` and `body` as native JSON objects; never stringify or JSON-encode them.

## Owned tool map

| Class | Tools |
| --- | --- |
| Mailbox discovery | `list_mailboxes` |
| Message discovery | `list_emails`, `search_emails`, `get_email`, `get_email_context` |

These are exactly 5 tools for the receipt-reconciliation domain. `list_mailboxes` is a prerequisite. No write or destructive tools are owned by this skill.

## Mailbox discovery

Resolve one exact mailbox before searching:

```json
{}
```

Prefer the returned `public_id` as `mailboxId`. Stop on ambiguous, disabled, unavailable, or cross-workspace results.

## Message discovery

Bounded metadata search for receipts, invoices, orders, or payments:

```json
{
  "mailboxId": "MAILBOX_PUBLIC_ID",
  "query": {
    "search": "receipt OR invoice OR order OR payment OR subscription",
    "page": 1,
    "limit": 20,
    "sortColumn": "date",
    "sortDirection": "DESC",
    "metadata_only": true,
    "agent_safe_content": true
  }
}
```

`search_emails` supports free text, sender, recipient, subject, ISO `date_start`/`date_end`, folder, read/starred state, category, attachment presence, and safety fields. Filters establish candidates, not sender authentication.

Read one selected message:

```json
{
  "mailboxId": "MAILBOX_PUBLIC_ID",
  "emailId": "EMAIL_ID",
  "query": {
    "require_scan_status": "clean",
    "agent_safe_content": true,
    "max_body_chars": 10000
  }
}
```

`metadata_only: true` omits body, snippet, raw headers, and threat URLs. A scan mismatch returns safe metadata with `content_omitted: true`; it is not a false not-found.

Use `get_email_context` after selecting one message when surrounding conversation matters. `query.limit` is 1–50 (default 20); reuse the opaque returned `next_cursor` as `query.cursor`. Results are oldest-first, sanitized, and scan-gated.

## No write tools

This skill does not own or call any send, reply, forward, schedule, delete, move, mark, folder, custom-label, or wallet tool. It is read-only by design. Route delivery, deletion, or payment to the owning focused skill.
