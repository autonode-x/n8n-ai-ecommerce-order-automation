# E-commerce Order Intake & Confirmation

> **Architecture:** See [ARCHITECTURE.md](ARCHITECTURE.md) for the infographic, detailed flow, and trust boundaries.

An n8n workflow template for validating a basic order payload, checking an order ID for duplicates, storing an order record in Google Sheets, and emailing a receipt-of-request confirmation. It reports persistence, lookup, and email outcomes through HTTP responses and Slack alerts.

> **Security status:** The source node is named “Authenticated Order Webhook,” but the export does **not** show authentication configured. This is a release blocker: an unauthenticated caller could submit arbitrary order details and trigger customer-facing email. Use only behind a trusted, authenticated commerce platform integration.

## Project snapshot

- **Category:** n8n automation, order intake, CRM/operations workflow
- **Workflow size:** 21 nodes, 14 connection groups in the supplied export
- **Integrations:** Google Sheets, Gmail, Slack, n8n webhook/respond nodes
- **What it confirms:** Receipt of an order request only
- **What it does not do:** Process payment, verify captured funds, reserve inventory, authorize fulfillment, or validate a client-supplied price against a catalogue

## Workflow canvas preview

![n8n editor view: e-commerce order intake workflow](assets/workflow-canvas.png)

> This editor screenshot is illustrative only; it does not prove secure configuration, live credentials, or production readiness.

## What it does

1. Receives a POST order payload containing order ID, customer email, product name, quantity, and price.
2. Normalizes input and validates field types/lengths, a basic email format, quantity range, and positive price range.
3. Looks up the order ID in Google Sheets; duplicate orders return an existing-order response without sending a second confirmation.
4. Saves a new order record, then emails an order-received acknowledgement to the submitted email address.
5. Marks confirmation success/failure in Sheets and sends PII-minimized Slack alerts for operational status.
6. Returns appropriate invalid, duplicate, success, manual-check, or persistence-error responses.

## Request fields

| Field | Required | Validation in source template |
|---|---:|---|
| `order_id` | Yes | String matching 3–80 letters, digits, `_`, or `-` |
| `customer_email` | Yes | Basic email syntax, max 254 characters |
| `product_name` | Yes | String, 1–200 characters |
| `quantity` | Yes | Integer 1–1,000 |
| `price` | Yes | Positive amount, up to 100,000,000 and two decimal places |

**Important:** Never trust a client-submitted price as authoritative. Resolve product/price from a trusted server-side catalogue, and verify order identity and amount before saving or sending a confirmation.

## Import and configure

1. Import `workflow.json` into a test n8n workspace.
2. **Before activation, require a signed webhook/HMAC or equivalent strong authentication from the commerce platform.** Add rate limits, request-size caps, timestamp/replay protection, and abuse monitoring. The JSON intentionally does not include credentials.
3. Bind least-privilege Google Sheets, Gmail, and Slack credentials from n8n's credential manager.
4. Configure your own private Sheet/tab and private Slack channel placeholders. Restrict Sheet sharing and retention.
5. Replace caller-provided price handling with a trusted product/price lookup and persist the authoritative amount, currency, taxes, and a stable order version/idempotency key.
6. Verify the request source and intended customer email before sending. Do not use this workflow as a public email relay.
7. Test invalid values, duplicate IDs, concurrent duplicates, sheet lookup/save failure, email failure, and manual retry behavior with fake customers only.
8. Keep demos limited to synthetic records. Do not show real customer addresses, order IDs, Sheets, Slack, Gmail, or n8n execution data.

## Security and privacy

The sanitized public JSON has credential bindings, Sheet identifiers/URLs, webhook IDs, and the original n8n instance identifier removed. The webhook path was changed and the node labeled with an authentication warning. This does not revoke any previously exposed credential or fix the live/original workflow.

- **Webhook is not authenticated in source.** A node name is not a security control. Do not expose it publicly in its current state.
- **Email abuse / fake orders:** Without a trusted source signature, an attacker could submit fake orders or target arbitrary email addresses with misleading receipt emails. Verify webhook signatures and authorize the order/customer in a trusted source system.
- **Price integrity:** `price` comes from the request and is used to calculate the emailed total. Replace it with server-side catalogue data and validate totals before any customer communication.
- **Idempotency and concurrency:** Lookup-then-write reduces duplicates but may race under simultaneous calls. Use an atomic unique constraint/lock; do not rely only on a user-chosen order ID.
- **Data minimization:** Customer email and order/product information are personal/commercial data. Keep Sheets and execution logs private with least privilege and a defined retention schedule.
- **Slack data:** The alert omits customer email but includes order ID. Treat that identifier as private and restrict channel access.
- **Honest receipt wording:** A receipt email does not imply payment, order acceptance, inventory availability, shipment, or fulfillment. The template says those are not confirmed.
- **Credentials:** Store keys/tokens only in n8n's credential manager. Rotate any secret that was previously exposed and review repository history.

## Known limitations

- The order record mapping stores order ID, customer email, product, and quantity; the visible mapping does not persist the request price/total. Add authoritative amount/currency/tax fields before using as a source of record.
- Product price is submitted by the caller in the current template and must not be trusted.
- No payment, inventory, tax, shipping, fulfillment, refund, or cancellation system is connected.
- The workflow uses Google Sheets and may need stronger concurrency/transaction controls under load.
- Confirmation email delivery can fail after the order row is saved; check the stored state and Gmail before manual resend.

## Suggested freelancing portfolio description

> Built an n8n order-intake prototype with payload validation, duplicate-order detection, Google Sheets persistence, receipt email, and explicit error/manual-check paths. The confirmation distinguishes request receipt from payment and fulfillment. The sanitized portfolio template documents required signed-webhook authentication, trusted server-side pricing, concurrency-safe idempotency, and customer-data protections.

## License

Publish only if you have permission to redistribute the workflow and any embedded templates. Review n8n, service, and third-party node terms before publication.


## Synthetic request and response examples

- [Sample request](sample/request.json)
- [Sample successful response](sample/response.json)

These examples use synthetic demonstration data and show the successful route only. HTTP status codes are returned separately by the workflow. Configure authentication and credentials in your own n8n instance before activation.
