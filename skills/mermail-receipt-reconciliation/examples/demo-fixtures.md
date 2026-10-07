# Demo fixtures for receipt reconciliation

Three fictional receipt emails demonstrating extraction behavior. No real data.

## Receipt A: One-time software purchase

**Email metadata:**
- Subject: "Your AcmeSoft Receipt #AS-2847"
- Sender: receipts@acmesoft.example
- Date: 2025-06-15

**Body excerpt:**
> Thank you for your purchase!
> Order number: AS-2847
> Item: AcmeSoft Pro License (1 seat)
> Amount: $49.99 USD
> Date: June 15, 2025
> Payment method: Card ending in 1234

**Expected extraction:**

```json
{
  "merchant": "AcmeSoft",
  "receipt_id": "AS-2847",
  "purchase_date": "2025-06-15",
  "amount": 49.99,
  "currency": "USD",
  "source_reference": "Email ABC-123",
  "renewal_date": null,
  "frequency": null
}
```

- Counts toward confirmed USD total: **yes**
- Recurring section: **no**
- Review-needed section: **no**

---

## Receipt B: Monthly subscription renewal

**Email metadata:**
- Subject: "CloudStream Monthly Renewal — $12.99"
- Sender: billing@cloudstream.example
- Date: 2025-06-20

**Body excerpt:**
> Your CloudStream Premium subscription has been renewed.
> Invoice: CS-99821
> Amount: $12.99 USD
> Renewal date: June 20, 2025
> Next renewal: July 20, 2025
> Plan: Premium Monthly

**Expected extraction:**

```json
{
  "merchant": "CloudStream",
  "receipt_id": "CS-99821",
  "purchase_date": "2025-06-20",
  "amount": 12.99,
  "currency": "USD",
  "source_reference": "Email DEF-456",
  "renewal_date": "2025-07-20",
  "frequency": "monthly"
}
```

- Counts toward confirmed USD total: **yes**
- Recurring section: **yes** (explicit next renewal and frequency)
- Review-needed section: **no**

---

## Receipt C: Ambiguous payment mention

**Email metadata:**
- Subject: "Payment processed"
- Sender: noreply@unknownvendor.example
- Date: 2025-06-22

**Body excerpt:**
> Your payment has been processed successfully.
> Thank you for your business.
> — Unknown Vendor Team

**Expected extraction:**

```json
{
  "merchant": "Unknown Vendor",
  "receipt_id": null,
  "purchase_date": "2025-06-22",
  "amount": null,
  "currency": null,
  "source_reference": "Email GHI-789",
  "renewal_date": null,
  "frequency": null
}
```

- Counts toward confirmed USD total: **no** (amount unknown)
- Recurring section: **no**
- Review-needed section: **yes** (ambiguous: no amount, no receipt ID, unclear merchant)

---

## Expected report behavior

Given receipts A, B, and C in a single search:

| Section | Content |
| --- | --- |
| Confirmed USD total | $62.98 (49.99 + 12.99) |
| Receipt table | All three rows, with confidence markers |
| Recurring | CloudStream $12.99 USD monthly, next renewal 2025-07-20 |
| Review-needed | Unknown Vendor — ambiguous payment, no amount or receipt ID |
