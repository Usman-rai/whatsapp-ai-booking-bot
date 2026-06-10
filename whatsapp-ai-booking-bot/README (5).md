# 🦷 WhatsApp AI Appointment Booking Bot

> **24/7 AI receptionist for healthcare clinics — books appointments automatically via WhatsApp while the clinic is closed.**

![n8n](https://img.shields.io/badge/Built%20with-n8n-orange?style=flat-square)
![Gemini](https://img.shields.io/badge/AI-Google%20Gemini-blue?style=flat-square)
![WhatsApp](https://img.shields.io/badge/Channel-WhatsApp-green?style=flat-square)
![Airtable](https://img.shields.io/badge/Database-Airtable-red?style=flat-square)
![Google Calendar](https://img.shields.io/badge/Calendar-Google%20Calendar-yellow?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-purple?style=flat-square)

---

## 🎬 Live Demo

**Watch the bot book a real appointment in under 60 seconds:**

▶️ [Demo Video — Automated Appointment Booking](https://www.loom.com/share/0128215c3860448a82d3680a6d731f64)

**Live demo website:**

🌐 [BrightSmile Dental Melbourne](https://bright-smile-melbourne.base44.app) — click the WhatsApp button

---

## 📋 What This Does

Most healthcare clinics are closed 60+ hours per week. When patients try to book during evenings, weekends, or public holidays — their inquiry goes to voicemail and they book elsewhere.

This bot fixes that permanently.

**The patient experience in 3 messages:**

```
Patient:  "Hi, I'd like to book a cleaning Monday 10am. Name is Sarah."

Sarah AI: "Monday 10am works great, Sarah — confirm to book? 👍"

Patient:  "Yes please"

Sarah AI: "Booked! ✅ Cleaning Mon 26 May at 10:00 AM. See you then!"
```

Behind the scenes, the AI has:
- Checked Google Calendar for real availability
- Created the appointment event
- Saved the patient record to the database
- Sent a confirmation message

**All in under 30 seconds. Zero human involvement.**

---

## ✨ Features

- ✅ **24/7 availability** — responds at 2am Saturday, on public holidays, anytime
- ✅ **Real calendar integration** — checks and books into Google Calendar live
- ✅ **Patient memory** — recognizes returning patients, no repeat questions
- ✅ **Intent detection** — understands natural language, not just commands
- ✅ **Emergency triage** — identifies urgent dental cases, provides emergency number
- ✅ **Insurance Q&A** — answers questions about HBF, Bupa, Medibank, NIB, HCF
- ✅ **Pricing information** — quotes services and prices accurately
- ✅ **Conversation memory** — remembers last 8 messages of context
- ✅ **Database logging** — every conversation and booking saved automatically
- ✅ **Fallback AI models** — if primary model fails, secondary model activates
- ✅ **Dual-channel testing** — test via n8n chat panel without burning WhatsApp messages
- ✅ **Multi-trigger support** — works with Twilio sandbox and Meta WhatsApp API

---

## 🏗️ Architecture

```
                    ┌─────────────────────┐
                    │   WhatsApp Message   │
                    │  (Twilio / Meta API) │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │    Set Variables     │
                    │  Extract: message,   │
                    │  phone, session ID   │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │   Lookup Patient     │
                    │  (Airtable search)   │
                    │  New or returning?   │
                    └──────────┬──────────┘
                               │
              ┌────────────────▼────────────────┐
              │         AI Receptionist          │
              │      (LangChain Agent)           │
              │                                  │
              │  Tools available:                │
              │  ├── check_availability          │
              │  ├── book_appointment_calendar   │
              │  └── save_appointment_to_db      │
              │                                  │
              │  Models:                         │
              │  ├── Primary: Gemini 2.5 Flash   │
              │  └── Fallback: Groq Llama 70B    │
              └────────────────┬────────────────┘
                               │
              ┌────────────────▼────────────────┐
              │         Is WhatsApp?             │
              │    (Route chat vs WhatsApp)      │
              └────┬───────────────────┬────────┘
                   │                   │
      ┌────────────▼──┐         ┌──────▼──────────┐
      │  Log to        │         │  Chat trigger   │
      │  Airtable      │         │  ends here      │
      │  Conversations │         │  (shows reply   │
      └────────────┬──┘         │   in n8n panel) │
                   │            └─────────────────┘
      ┌────────────▼──┐
      │  Send Reply    │
      │  via Twilio    │
      └───────────────┘
```

---

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Automation** | [n8n](https://n8n.io) | Workflow orchestration |
| **AI Brain** | Google Gemini 2.5 Flash | Natural language understanding + tool calling |
| **AI Fallback** | Groq (Llama 3.3 70B) | Backup if primary model hits rate limit |
| **WhatsApp** | Twilio / Meta WhatsApp API | Receive and send WhatsApp messages |
| **Calendar** | Google Calendar API | Check availability + create appointments |
| **Database** | Airtable | Patient records, appointments, conversations |
| **Memory** | LangChain Buffer Window | Conversation context (last 8 messages) |
| **Demo Site** | Base44 | Frontend demo website |

---

## 📁 Repository Structure

```
whatsapp-dental-booking-bot/
│
├── README.md                          # You are here
├── workflow/
│   └── dental-bot-workflow.json       # Full n8n workflow export
├── prompts/
│   └── ai-receptionist-system-prompt.md   # Sarah AI system prompt
├── airtable/
│   └── airtable-schema.md             # Database schema
├── docs/
│   ├── setup-guide.md                 # Full setup instructions
│   ├── meta-whatsapp-setup.md         # Meta API setup guide
│   └── customization-guide.md         # How to adapt for new clinics
└── screenshots/
    ├── workflow-overview.png
    ├── conversation-demo.png
    └── calendar-booking.png
```

---

## ⚡ Quick Start

### Prerequisites

- n8n account (cloud or self-hosted)
- Google account (Calendar + Gmail)
- Airtable account (free tier)
- Twilio account (free sandbox) OR Meta WhatsApp Business API
- Google Gemini API key (free — [get it here](https://aistudio.google.com/apikey))

### Setup

**1. Import the workflow**
```
1. Open your n8n instance
2. Click the + button → Import from file
3. Select workflow/dental-bot-workflow.json
4. Workflow imports with all nodes
```

**2. Add your credentials**

In n8n credentials, add:
- Google Gemini API key
- Groq API key (free at groq.com)
- Airtable personal access token
- Google Calendar OAuth
- Twilio Account SID + Auth Token

**3. Set up Airtable**

Create a base with 3 tables:

| Table | Key Fields |
|-------|-----------|
| **Patients** | Phone Number, Name, Email, Last Visit |
| **Appointments** | Service, Date, Time, Status, Patient (linked), Booking Source |
| **Conversations** | Patient Phone, Message Text, AI Response, Timestamp, Direction |

**4. Customize for your clinic**

Open the AI Receptionist node → Options → System Message.

Update these sections for your clinic:
```
CLINIC: Your Clinic Name
ADDRESS: Your Address
HOURS: Your opening hours
PHONE: Your emergency phone
SERVICES: Your services and prices
INSURANCE: Your accepted insurers
```

**5. Connect WhatsApp**

Option A — Twilio Sandbox (instant, for testing):
```
1. Sign up at twilio.com
2. Activate WhatsApp sandbox
3. Add Twilio webhook URL from n8n to Twilio console
4. Text "join [your-code]" to the sandbox number to start testing
```

Option B — Meta WhatsApp API (for production):
```
1. Create Meta Developer account
2. Create Business app → Add WhatsApp product
3. Get Phone Number ID + Permanent Access Token
4. Register n8n webhook in Meta Developer Portal
5. Subscribe to "messages" webhook field
```

**6. Activate and test**
```
1. Toggle workflow to Active
2. Open "Test in Chat" trigger in n8n
3. Send: "Hi, I'd like to book a cleaning Monday 10am"
4. Bot should reply with availability confirmation
5. Reply "Yes" → check Google Calendar for new event
```

---

## 🗂️ Airtable Schema

### Patients Table
```
Phone Number    (Phone)        — primary identifier
Name            (Single line)  — patient full name
Email           (Email)        — patient email
Last Visit      (Date)         — most recent appointment
Visit Count     (Number)       — total appointments booked
Notes           (Long text)    — special requirements
```

### Appointments Table
```
Service Requested   (Single select) — Cleaning, Filling, Whitening, etc.
Requested Date      (Date)          — appointment date
Requested Time      (Single line)   — time in 10:00 AM format
Status              (Single select) — Confirmed, Cancelled, Completed
Booking Source      (Single select) — WhatsApp Bot, Phone, Walk-in
Patient             (Link)          — linked to Patients table
Notes               (Long text)     — special notes from AI
```

### Conversations Table
```
Patient Phone   (Phone)        — who sent the message
Message Text    (Long text)    — what patient said
AI Response     (Long text)    — what Sarah replied
Timestamp       (DateTime)     — when it happened
Direction       (Single select)— Inbound / Outbound
```

---

## 🤖 AI System Prompt

The AI receptionist is named **Sarah** and handles:

- Appointment booking (primary function)
- Service and pricing inquiries
- Insurance questions
- Emergency dental triage
- General FAQ

The prompt enforces:
- Maximum 2-sentence replies (WhatsApp style)
- Decisive behavior (picks a time when patient says "any time")
- Correct calendar interpretation (empty = free, not busy)
- 3-message booking flow
- Australian tone ("G'day", "No worries", "Cheers")

Full system prompt: [`prompts/ai-receptionist-system-prompt.md`](prompts/ai-receptionist-system-prompt.md)

---

## 📊 Performance

| Metric | Value |
|--------|-------|
| Average booking completion | 3 messages |
| Response time | < 5 seconds |
| Tokens per conversation | ~1,200 (Gemini 2.5 Flash) |
| Daily API capacity (free) | 1,500 requests |
| Concurrent conversations | Unlimited (each session isolated) |

**Industry data:**
- 78% of patients book with the first clinic that responds
- Response rate drops from 78% → 3% after 24 hours
- Average dental clinic is closed 60+ hours per week

---

## 💰 Business Case

For a typical Australian dental clinic:

```
Recovered bookings per month:     4-10
Average patient value:            AUD $500 per visit
Monthly revenue recovered:        AUD $2,000-5,000
Patient lifetime value:           AUD $2,500-5,000

Setup cost for clinic:            AUD $1,500 (one time)
Monthly maintenance:              AUD $200-300
API costs (Twilio + Gemini):      AUD $10-30/month

ROI for clinic:                   Positive from month 1
```

---

## 🔧 Customization

This bot is designed to be adapted for any healthcare clinic.

**To deploy for a new client:**

1. Duplicate the n8n workflow
2. Create new Airtable base with the schema above
3. Update the system prompt with clinic-specific info:
   - Clinic name, address, phone
   - Opening hours
   - Services and prices
   - Insurance accepted
4. Connect client's Google Calendar
5. Set up client's Twilio or Meta WhatsApp number
6. Test 10 conversations
7. Hand over

**Works for:**
- Dental clinics
- Physiotherapy
- Podiatry
- Osteopathy
- GP clinics
- Psychology practices
- Veterinary clinics

---

## 🚧 Roadmap

- [ ] Voice message support (WhatsApp audio → Whisper transcription → AI response)
- [ ] SMS appointment reminders (24h + 2h before)
- [ ] Post-appointment Google review request
- [ ] RAG knowledge base (clinic uploads their own docs)
- [ ] Multi-clinic support (one workflow, unlimited clients)
- [ ] Cliniko API integration (Australian clinic management software)
- [ ] Instagram DM support
- [ ] Sentiment analysis + escalation to human

---

## 📄 License

MIT License — free to use, modify, and deploy commercially.

---

## 👨‍💻 About The Builder

Built by **Usman Farooq** — AI automation specialist focused on healthcare clinics in Australia.

- 🌐 [LinkedIn](https://linkedin.com/in/usman-rai)
- 💻 [GitHub](https://github.com/Usman-rai)
- 📧 raiusmanr517@gmail.com
- 💬 WhatsApp: +92 329 4462757

**Available for:**
- Custom deployment for your clinic
- Adapting this system for new healthcare niches
- n8n automation consulting

---

## ⭐ If This Helped You

Give the repo a star — it helps other developers find it and supports continued development.

**Have a dental or healthcare clinic in Australia?** [Get in touch](https://wa.me/923294462757) — I'll set this up for you in 3 days.
