# Before RMINGI — The Dangerous State

This is what the payment system looks like **before** RMINGI runs.
The code works. The tests pass. But intention is invisible.

---

## The Codebase (Simplified)

### Payment Model

```python
class Payment:
    STATES = ['draft', 'pending', 'paid', 'failed', 'refunded']
    
    def submit(self):
        self.status = 'pending'
        self.submitted_at = now()
        # No comment explaining what "pending" means
        # No link to a webhook, manual approval, or retry policy
    
    def mark_paid(self):
        self.status = 'paid'
        self.paid_at = now()
```

**What's dangerous:** Three AI models have worked on this file. None of them
documented whether `pending` means "waiting for Stripe webhook," "waiting for
manual accounting approval," or "safe to auto-retry after timeout."

---

### Retry Cron Job

```python
# Added by AI-2 during "performance optimization sprint"
@cron(every='6h')
def retry_stale_payments():
    stale = Payment.query.filter(
        status='pending',
        submitted_at < now() - hours(6)
    )
    for payment in stale:
        payment.submit()  # Re-submits to payment gateway
```

**What's dangerous:** AI-2 assumed `pending` means "gateway timeout — safe to retry."
If `pending` actually means "waiting for manual accounting approval," this cron
is re-charging cards that a human hasn't reviewed yet.

---

### Soft Delete

```python
class User:
    def delete_account(self):
        self.deleted_at = now()
        self.is_active = False
        # Records stay in the database forever
        # No archival job. No hard-delete job. No retention period.
```

**What's dangerous:** Is data kept for account recovery (3-day undo window)?
Legal compliance (7-year retention)? Or did AI-1 just use the framework's
default soft-delete pattern without thinking about it?

---

### Refund Endpoint

```python
@app.route('/admin/refund', methods=['POST'])
@require_role('admin')
def process_refund(payment_id):
    payment = Payment.get(payment_id)
    payment.status = 'refunded'
    stripe.refund(payment.charge_id)
    return {'status': 'refunded'}
```

**What's dangerous:** This is admin-only today. But is that a permanent business
rule ("refunds always require human approval") or a temporary limitation
("we haven't built the automated refund path yet")?

---

## What's Missing

None of these files contain:
- A comment explaining **why** the code works this way
- An intention record that a future AI can read
- Any documentation separating "deliberate business rule" from "AI's best guess"

**This is the exact state where RMINGI is needed.**
