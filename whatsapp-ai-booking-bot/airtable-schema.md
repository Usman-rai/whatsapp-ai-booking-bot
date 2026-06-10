# Airtable Schema — WhatsApp AI Booking Bot

## Base Name: Dental Clinic AI Assistant

---

## Table 1: Patients

| Field Name | Field Type | Notes |
|-----------|-----------|-------|
| Phone Number | Phone | Primary identifier — used to look up returning patients |
| Name | Single line text | Patient full name |
| Email | Email | Optional — not always collected |
| Last Visit | Date | Auto-updated when appointment is confirmed |
| Visit Count | Number | Total confirmed appointments |
| Notes | Long text | Special requirements, medical notes |

---

## Table 2: Appointments

| Field Name | Field Type | Options |
|-----------|-----------|---------|
| Service Requested | Single select | Cleaning, Filling, Whitening, Denture Consult, Dentures, Emergency, Consultation, Checkup |
| Requested Date | Date | YYYY-MM-DD format |
| Requested Time | Single line text | 10:00 AM format |
| Status | Single select | Confirmed, Cancelled, Completed, No-show, Pending |
| Booking Source | Single select | WhatsApp Bot, Phone, Walk-in, Online Form |
| Notes | Long text | AI-generated notes from the conversation |
| Appointment ID | Auto number | Auto-generated, do not fill manually |
| Created At | Created time | Automatic |

---

## Table 3: Conversations

| Field Name | Field Type | Options |
|-----------|-----------|---------|
| Patient Phone | Phone | Who sent the message |
| Message Text | Long text | What the patient said |
| AI Response | Long text | What Sarah replied |
| Timestamp | Date (include time) | When it happened |
| Direction | Single select | Inbound, Outbound |
| Message ID | Single line text | Twilio/Meta message ID for deduplication |

---

## Important Notes

**On the Appointments table:**
- Enable **Typecast** option on all Airtable nodes that write to this table
- This converts date strings, time strings, and select values automatically
- Without Typecast, you will get "field type mismatch" errors

**On the save_appointment_to_database tool node:**
- This node MUST be configured manually in the n8n UI
- Do NOT update it via SDK/code — the columns mapping will corrupt
- If it breaks, delete and recreate the node from scratch in the UI
- Set Mapping Column Mode to "Map Each Column Manually" BEFORE adding fields

**On the Patients table:**
- The Phone Number field must be the primary field (first field)
- Format: +61412345678 (international format without spaces)
- The Lookup Patient node searches by this field

---

## How Tables Connect

```
Conversations (many)
     ↓ Patient Phone
Patients (one)
     ↓ (optional link)
Appointments (many)
```

The AI agent does NOT use linked record fields — it saves everything as plain text/select values. Linked records can be added manually later for advanced reporting.

---

## Getting Your IDs

After creating the base:

1. Go to airtable.com/[your-base]
2. Click Help → API Documentation
3. Base ID: shown at the top (starts with `app`)
4. Table IDs: shown for each table (starts with `tbl`)

Add these to your n8n Airtable nodes.
