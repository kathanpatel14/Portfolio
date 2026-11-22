# Lead Qualification & Workflow Automation (n8n)

A personal portfolio project demonstrating an automated lead intake process using **Tally Forms**, **n8n**, and **Google Sheets**.

## Overview

A lead submits a Tally form, which sends a `POST` request to an n8n webhook. The workflow validates submitted information, classifies eligible leads by budget, converts service dropdown IDs into readable labels, and appends accepted records to Google Sheets. The workflow is configured to send HTTP responses back to the webhook caller.

**Technologies:** n8n · Tally · Google Sheets · Webhooks · JSON

## Workflow design

```text
Tally Form
   |
   v
n8n Webhook (POST)
   |
   v
Validate lead (IF) ------ Invalid -----> HTTP 400 response
   |
  Valid
   v
Classify lead (Switch)
   |         |         |
  Hot       Warm      Cold
   |         |         |
   +---- Edit Fields --+
             |
             v
      Google Sheets
        Append Row
             |
             v
      HTTP 200 response
```

## Features

- Receives form submissions through a webhook.
- Uses conditional rules to validate required lead information.
- Classifies leads as **Hot**, **Warm**, or **Cold** based on budget.
- Maps Tally dropdown option IDs to human-readable service names.
- Saves accepted leads and their classifications in Google Sheets.
- Provides success and validation-error webhook responses.

## Qualification rules

| Budget | Classification |
|---|---|
| €5,000 or more | Hot |
| €2,000 to below €5,000 | Warm |
| Above €0 to below €2,000 | Cold |
| Missing, invalid, or non-positive | Reject |

## Example input

This simplified example illustrates the normalized lead fields; it is **not** the raw Tally webhook format.

```json
{
  "name": "Sarah Miller",
  "email": "sarah@example.com",
  "company": "Miller Fashion",
  "budget": 5000,
  "service": "SEO",
  "message": "We need help improving our search rankings."
}
```

## Spreadsheet output

Accepted leads are recorded with these columns:

`Timestamp` · `Name` · `Email` · `Company` · `Budget` · `Service` · `Message` · `Lead Status`

## Example success response

```json
{
  "success": true,
  "message": "Lead saved successfully",
  "classification": "Hot"
}
```

## Acceptance tests to verify

- [ ] Budget €5,000 → Hot
- [ ] Budget €3,000 → Warm
- [ ] Budget €1,000 → Cold
- [ ] Missing email → rejected
- [ ] Invalid email → rejected
- [ ] Zero or negative budget → rejected
- [ ] Text budget → rejected
- [ ] Unsupported service → rejected
- [ ] Missing optional company or message → accepted
- [ ] Spreadsheet failure does not produce a false success response

*These are testing targets, not claims that all cases have passed.*

## Reproducing the workflow

1. Build or import the n8n workflow.
2. Configure your own Google Sheets credentials and spreadsheet.
3. Set up a Tally form with matching fields and service dropdown options.
4. Configure the production webhook URL in Tally.
5. Submit fictional test leads and inspect n8n executions and Google Sheets.

A sanitized workflow export and screenshots may be added later. The live webhook URL and credentials are intentionally not included.

## Future enhancements

- Deduplicate repeated webhook deliveries using a Tally submission ID.
- Return field-specific validation errors.
- Notify the sales team about Hot leads.

## Security

Never commit real customer data, credentials, private webhook URLs, or unredacted execution logs. Use fictional leads in public screenshots.

**Project type:** Personal learning and portfolio project.
