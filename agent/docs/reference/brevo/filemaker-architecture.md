# FileMaker Email Architecture

Goal:

Replace legacy FileMaker-side HTML generation and plugin-based sending with a Brevo API integration.

## Design Direction

FileMaker should do the least amount of email rendering possible. It should decide what needs to be sent, gather business data, send a structured API payload to Brevo, and store the result.

Brevo should own template rendering, HTML maintenance, delivery, and tracking.

## Proposed FileMaker Components

- Email Templates table
- Email Queue or Email Log table
- Email Events table, for a future webhook phase
- API settings or integration settings table
- Brevo API wrapper script
- Send queued email script
- Test Brevo request script

## Proposed Send Flow

1. Business workflow requests an email.
2. FileMaker creates an email queue or log record.
3. FileMaker builds a JSON payload using the mapped Brevo template ID and params.
4. FileMaker calls the Brevo transactional email endpoint.
5. FileMaker stores the response, status, and message ID.
6. A future webhook flow updates the record with delivery and engagement events.

## Open Decisions

- queue-first versus send-immediately behavior
- retry strategy
- error notification strategy
- whether template mapping is global or per company/brand
- how legacy email scripts are phased out
