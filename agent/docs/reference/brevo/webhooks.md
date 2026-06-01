# Webhooks

Future phase.

The first implementation focus is creating and sending emails. Webhooks can be added later to bring delivery and engagement events back into FileMaker.

## Likely Events

- delivered
- soft bounce
- hard bounce
- opened
- clicked
- spam
- unsubscribed

## Suggested FileMaker Destination

Webhook events will likely be stored in an Email Events table linked back to an Email Queue or Email Log record.

Useful event fields may include:

- Brevo message ID
- event type
- event timestamp
- recipient email
- link URL, for click events
- raw event JSON
- processed flag
- related email log ID

## Open Decisions

- inbound webhook endpoint
- authentication or signature verification
- event deduplication strategy
- whether opens and clicks update summary fields on the parent email log record
