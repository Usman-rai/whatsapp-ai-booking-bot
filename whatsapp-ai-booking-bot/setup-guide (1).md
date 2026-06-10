# Setup Guide — WhatsApp AI Booking Bot

## Time Required: 2-3 hours
## Difficulty: Intermediate (requires n8n and API experience)

---

## Step 1: Import n8n Workflow (10 min)

1. Download `workflow/dental-bot-workflow.json` from this repo
2. Open your n8n instance
3. Click **"+"** → **"Import from file"**
4. Select the downloaded JSON file
5. Workflow appears with 13 nodes

---

## Step 2: Set Up Airtable (20 min)

1. Create a free account at [airtable.com](https://airtable.com)
2. Create a new base called **"Dental Clinic AI Assistant"**
3. Create 3 tables with these exact field names:

### Patients Table
| Field Name | Field Type |
|-----------|-----------|
| Phone Number | Phone |
| Name | Single line text |
| Email | Email |
| Last Visit | Date |
| Visit Count | Number |
| Notes | Long text |

### Appointments Table
| Field Name | Field Type | Options |
|-----------|-----------|---------|
| Service Requested | Single select | Cleaning, Filling, Whitening, Denture Consult, Dentures, Emergency, Consultation |
| Requested Date | Date | — |
| Requested Time | Single line text | — |
| Status | Single select | Confirmed, Cancelled, Completed, No-show |
| Booking Source | Single select | WhatsApp Bot, Phone, Walk-in, Online |
| Notes | Long text | — |

### Conversations Table
| Field Name | Field Type | Options |
|-----------|-----------|---------|
| Patient Phone | Phone | — |
| Message Text | Long text | — |
| AI Response | Long text | — |
| Timestamp | Date (include time) | — |
| Direction | Single select | Inbound, Outbound |

4. Get your Airtable **Personal Access Token**:
   - airtable.com → Account → Developer Hub → Personal Access Tokens
   - Create token with scopes: `data.records:read`, `data.records:write`
   - Copy the token (starts with `pat...`)

5. Get your **Base ID** and **Table IDs**:
   - Open your base → Help → API documentation
   - Base ID starts with `app...`
   - Table IDs start with `tbl...`

---

## Step 3: Get API Keys (15 min)

### Google Gemini (Primary AI — Free)
1. Go to [aistudio.google.com/apikey](https://aistudio.google.com/apikey)
2. Sign in with Google
3. Click **"Create API key"** → **"Create in new project"**
4. Copy the key (starts with `AIza...`)
5. Free tier: 1,500 requests/day

### Groq (Fallback AI — Free)
1. Go to [groq.com](https://groq.com) → Sign up
2. API Keys → Create API Key
3. Copy the key (starts with `gsk_...`)
4. Free tier: 12,000 tokens/minute

### Twilio (WhatsApp Sandbox — Free Testing)
1. Go to [twilio.com](https://twilio.com) → Sign up
2. Console → Messaging → Try it out → Send a WhatsApp message
3. Note your **Account SID** and **Auth Token**
4. Sandbox number: `+14155238886`
5. WhatsApp the sandbox to join: `whatsapp:+14155238886`

---

## Step 4: Add Credentials in n8n (20 min)

In n8n: **Settings → Credentials → Add credential**

Add these one by one:

**Google Gemini API:**
- Type: Google Gemini (PaLM) API
- API Key: paste your Gemini key

**Groq API:**
- Type: Groq API
- API Key: paste your Groq key

**Airtable:**
- Type: Airtable Token API
- Access Token: paste your Airtable token

**Google Calendar OAuth:**
- Type: Google Calendar OAuth2
- Follow OAuth flow to connect your Google account

**Twilio:**
- Type: Twilio API
- Account SID: your Twilio SID
- Auth Token: your Twilio token

---

## Step 5: Configure The Workflow (20 min)

### Update Airtable Node IDs

In each Airtable node, update:
- **Base ID:** your base ID (from Step 2)
- **Table:** select from dropdown

Nodes to update:
- `Lookup Patient` → Patients table
- `save_appointment_to_database` → Appointments table
- `Log Conversation` → Conversations table

### Update Google Calendar

In calendar tool nodes, update:
- **Calendar:** select your Google Calendar from dropdown

### Update System Prompt

In `AI Receptionist` node → Options → System Message:
- Replace all `[PLACEHOLDER]` text with your clinic's real info
- See `prompts/ai-receptionist-system-prompt.md` for the template

---

## Step 6: Set Up Twilio Webhook (10 min)

1. Activate the workflow in n8n (top right toggle → ON)
2. Click **Twilio Trigger** node → copy the **Production URL**
3. Go to Twilio Console → Messaging → Sandbox settings
4. Paste n8n URL in **"When a message comes in"** field
5. Save

---

## Step 7: Test The Bot (15 min)

### Test 1: Chat Panel (No Twilio needed)
1. Click **"Test in Chat"** node → **"Open chat"**
2. Type: `Hi, I'd like a cleaning Monday 10am, name is Sarah`
3. Bot should check calendar and suggest a time
4. Reply: `Yes book it`
5. Check Google Calendar for the new event ✅
6. Check Airtable Appointments table for the new row ✅

### Test 2: Real WhatsApp
1. Make sure workflow is ACTIVE
2. WhatsApp the sandbox number: `+14155238886`
3. Send the same test message
4. Bot should reply within 5 seconds ✅

### Test 3: Edge Cases
Test these scenarios to make sure everything works:
- "Any time tomorrow" → bot should pick a specific time
- "I have an emergency, my tooth is cracking" → bot should offer same-day
- "Do you accept Bupa?" → bot should answer from the prompt
- "What does a cleaning cost?" → bot should give the price

---

## Step 8: Deploy For A Client (30 min)

When a client pays and you're ready to deploy:

1. **Duplicate the workflow** in n8n
2. **Rename** it to `[Client Name] - WhatsApp Bot`
3. **Create a new Airtable base** for the client (don't use your demo one)
4. **Update all Airtable IDs** in the new workflow
5. **Update the system prompt** with the client's clinic info
6. **Connect client's Google Calendar** (they share access with you)
7. **Set up client's Twilio account:**
   - Client creates their own Twilio account
   - Client buys a WhatsApp-enabled number (~$5/month)
   - Client gives you Account SID + Auth Token
   - Update Twilio credential in n8n
8. **Update Twilio webhook** with new workflow's webhook URL
9. **Test 10 conversations** before handing over
10. **Record a Loom** showing it working for the client's handover

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Bot doesn't respond | Check workflow is ACTIVE + Twilio webhook URL is correct |
| Calendar shows "fully booked" when it's empty | Empty `[]` = FREE — check system prompt has this instruction |
| Airtable save fails with "columns.mappingMode" error | Rebuild save node manually in UI — do NOT update via code |
| Groq rate limit error | System prompt is too long — use the shorter prompt in this repo |
| Bot asks same question twice | Increase `contextWindowLength` in Conversation Memory node to 10 |
| Twilio messages stop after 5/day | Twilio sandbox limit — upgrade to paid or switch to Meta API |

---

## Common Customizations

**Change clinic hours:**
Update the `HOURS` line in the system prompt.

**Add a new service:**
Add to the `SERVICES` section in the system prompt. The AI will automatically include it in responses and booking tool calls.

**Change the AI's name:**
Replace "Sarah" with any name in the system prompt first line.

**Add a new language:**
Add to the system prompt: `LANGUAGES: English, [Language] (available [days])`

**Disable a tool:**
Remove the tool from the AI Receptionist's tool list (click the tool → disconnect).

---

## Need Help?

- 📧 Email: raiusmanr517@gmail.com
- 💬 WhatsApp: +92 329 4462757
- 💼 [LinkedIn](https://linkedin.com/in/usman-rai)

I offer paid setup and customization services for clinics that want this deployed professionally.
