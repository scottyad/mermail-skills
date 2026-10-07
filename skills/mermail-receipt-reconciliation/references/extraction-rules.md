# Receipt structured extraction rules

Read this reference when extracting structured data from receipt, invoice, order, or subscription emails for the expense report.

## Fields to extract

For every candidate email, attempt to extract the following fields:

| Field | Description | Confidence |
| --- | --- | --- |
| `merchant` | The business or service name issuing the receipt | confirmed / inferred / unknown |
| `receipt_id` | Receipt, invoice, order, or reference number | confirmed / inferred / unknown |
| `purchase_date` | Date of purchase or transaction (ISO-8601 when possible) | confirmed / inferred / unknown |
| `amount` | Numeric amount charged | confirmed / inferred / unknown |
| `currency` | Currency code (e.g., USD, EUR) | confirmed / inferred / unknown |
| `source_reference` | Stable email identifier linking back to the source message | confirmed |
| `renewal_date` | Next expected renewal or charge date for subscriptions | confirmed / inferred / unknown |
| `frequency` | Subscription frequency: monthly, yearly, quarterly, or unknown | confirmed / inferred / unknown |

## Confidence levels

- **confirmed**: directly stated in the email body or subject with no ambiguity
- **inferred**: reasonably deduced from context, but not explicitly stated
- **unknown**: not available or too ambiguous to extract

Never upgrade inferred to confirmed. Never fabricate a value to avoid unknown.

## Currency handling

- Keep every amount in its original currency.
- Never convert currencies or add different currencies into a single total.
- Report confirmed totals per currency separately.

## Prohibited fabrications

Never fabricate:

- Amounts or totals not explicitly stated
- Exchange rates or converted values
- Payment status (paid, pending, failed)
- Subscription terms or frequencies not stated
- Tax treatment or tax amounts
- Merchant names from ambiguous signatures or footers

## Subscription detection

A charge is treated as a possible subscription when the email contains:

- Explicit renewal or next-charge language
- A stated frequency (monthly, yearly, quarterly)
- A subscription or plan name
- A prior charge from the same merchant within the search window

Mark subscription inferences as inferred, not confirmed, unless the email explicitly states the terms.
