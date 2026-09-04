# Airtable Schema — Chleo Hair Studio CRM

Base: `Chleo Hair Studio CRM` (`appE1hAG2NEl8YdkX`)

Derived from the fields actually written/read by the n8n workflows and the field
headers in the sample exports in this folder. Types are inferred from usage, not
pulled from the Airtable API schema endpoint.

## Clients (`tblgm9o0PoKnBmcMM`)

| Field | Type | Notes |
|---|---|---|
| Name | Single line text | |
| Client ID | Number | |
| Phone | Phone number | WhatsApp/SMS identity |
| Email | Email | |
| Last Visit Date | Date | |
| Last Purchase Date | Date | |
| Last Purchase Category | Single line text | |
| Preferred Service | Single line text | |
| Client Notes | Long text | |
| Appointments | Link to Appointments | |
| Product Reminders | Checkbox | opt-in for reorder reminders |
| Booking Reminder Channel(s) | Multiple select | e.g. Email, WhatsApp |
| Last Rebooking Reminder Sent | Date | dedupe stamp, written by Use Case 3 |
| Rebooking Reminders Sent Count | Number | |
| Last Reorder Reminder Sent | Date | dedupe stamp, written by Use Case 3 |

## Appointments (`tblyiMDqAUH1dCxaq`)

| Field | Type | Notes |
|---|---|---|
| Service | Single line text | |
| Client | Link to Clients | |
| Start Time | Date/time (ISO) | |
| Status | Single select | e.g. Confirmed, Cancelled, Completed |
| Calendly Event URI | Single line text | Google Calendar event reference, despite the field name |
| Created Via | Single select | e.g. WhatsApp, Website |
| Session ID | Single line text | website chat session |
| Execution ID | Single line text | n8n execution reference |
| 24h Reminder Sent | Checkbox/Date | dedupe stamp |
| 12h Reminder Sent | Checkbox/Date | dedupe stamp |

## Error Log (`tbldVvRqZMKi1y7Eh`)

| Field | Type | Notes |
|---|---|---|
| Workflow | Single line text | |
| Error Message | Long text | |
| Failed Node | Single line text | |
| Execution URL | URL | link to the failed n8n execution |
| Logged At | Date/time | |

Table was empty at export time — no failures have been logged yet.

## Sample data

`airtable_export_Clients.csv`, `airtable_export_Appointments.csv`, and
`airtable_export_Error Log.csv` in this folder are point-in-time exports.
Client names, phone numbers, and emails are synthetic test data, not real
salon clients.
