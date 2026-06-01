# Transactional Email

Primary endpoint:

```text
POST /v3/smtp/email
```

Purpose:

Send transactional emails from FileMaker through Brevo.

Preferred FileMaker payload approach:

- identify the internal email type
- use a Brevo `templateId` where possible
- send recipient details
- send template `params` for merge data
- include tags or metadata so responses and later events can be linked back to FileMaker records

FileMaker should avoid generating full HTML unless a specific email type genuinely requires it.

## FileMaker Responsibilities

- decide which email should be sent
- collect and validate merge data
- create an email queue or log record
- call the Brevo API
- store the API response status and message identifier

## Brevo Responsibilities

- own the HTML template
- render template merge values
- send the email
- handle delivery tracking
- expose delivery, open, and click events for a future webhook phase

## Open Decisions

- API key storage location
- standard sender identity
- whether all messages use templates, or whether a small number use direct content
- common tags or metadata format
- retry behavior after temporary API failure
