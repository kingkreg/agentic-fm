# Brevo Templates

Preferred approach:

Brevo owns the email HTML and template rendering. FileMaker stores the business intent and merge data, not complete generated HTML.

## Suggested FileMaker Mapping

FileMaker may need a template mapping table with fields such as:

- internal email type
- Brevo template ID
- template name
- active flag
- required params
- optional default sender
- optional test recipient
- notes

## Template Params

Template params should be treated as a contract between FileMaker and Brevo.

Example params:

```json
{
  "customer_name": "Example Customer",
  "invoice_number": "INV-1001",
  "invoice_total": "120.00",
  "payment_url": "https://example.com/pay/INV-1001"
}
```

## Open Decisions

- naming convention for param keys
- whether param requirements are stored in FileMaker
- whether test-mode sends use the same template IDs
- who owns template changes in Brevo
