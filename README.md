# AI Email Assistant with Personalized Style Learning

**AgorAI Spring School 2026 — Hackonauts Team**  
Authors: Assia Bendaou & Douae Annasri | ENSIASD Taroudant

---

## What is it?

An AI email assistant that learns how **you** write and drafts replies in your exact voice — not a generic AI voice. Built with a multi-layer security pipeline and strict human-in-the-loop control.

---

## The Problem

Professionals spend **28% of their workweek** on email. Existing AI tools generate responses that sound robotic and impersonal. No tool learns your unique writing style.

---

## Key Features

**Personalization Engine**
- Analyzes your last 25 sent emails
- Extracts 17 linguistic metrics: tone, formality, sentence length, emoji usage, contractions, greeting patterns and more
- Injects 3 real samples of your writing into every prompt (few-shot learning)
- Builds a personal style profile stored in Google Sheets
- Updates automatically on schedule

**Security Pipeline (5 layers)**
- Prompt Injection Guard — scans every incoming email for malicious instructions
- Output Validator — blocks suspicious URLs, scripts, and prompt leakage
- AES-256-GCM Encryption — encrypts sender name, email, and body before storage
- HMAC-SHA256 Signing — digitally signs every processed email
- Append-Only Audit Log — immutable record in Google Sheets

**Human-in-the-Loop**
- AI drafts, you decide
- Nothing is ever sent automatically
- 100% human approval rate by design

**Performance**
- Draft generated in < 2 seconds
- Any language, auto-detected
- 70B parameter AI model (Groq llama-3.3-70b-versatile)

---

## System Architecture

**Workflow 1 — Style Learner** *(runs on schedule)*
```
Schedule Trigger → Gmail API (fetch 25 sent emails)
    ↓
Extract Content
    ↓
Analyze 17 Style Metrics
    ↓
Append/Update Email Row (Sheet1, matched by Email ID)
    ↓
Write Summary Row → Profile Sheet (17 columns)
```

**Workflow 2 — Draft Generator** *(triggers on new email)*
```
Gmail Trigger → Get Style Profile from Sheets
    ↓
Filter Email
    ↓
Injection Guard
    ↓
Is Safe? → No: Silent Drop | Yes: Continue
    ↓
Check if Reply Needed
    ↓
Build Personalized Prompt (+ 3 sample emails)
    ↓
Groq AI API (llama-3.3-70b-versatile)
    ↓
Decode HTML Entities
    ↓
Output Validator
    ↓
Encrypt & Sanitize PII (AES-256-GCM)
    ↓
Create Gmail Draft
    ↓
Merge → Hash & Sign (HMAC-SHA256)
    ↓
Append-Only Audit Log (Google Sheets)
```

---

## Technology Stack

| Tool | Role |
|---|---|
| n8n | Low-code workflow automation |
| Groq AI | llama-3.3-70b-versatile inference |
| Gmail API | Email input & draft creation |
| Google Sheets | Style profile & audit log storage |
| AES-256-GCM | PII encryption |
| HMAC-SHA256 | Tamper-proof digital signing |

---

## Style Profile — 17 Metrics

avg_word_count · avg_sentence_length · avg_formality · uses_emojis · uses_contractions · most_common_tone · most_common_content_type · sample_email_1 · sample_email_2 · sample_email_3 · total_emails_analyzed · greeting_style · closing_style · avg_paragraph_count · punctuation_style · response_length_preference · language

---

## Security — Audit Log Schema

| Column | Content |
|---|---|
| draft_id | Gmail draft ID |
| thread_id | Gmail thread ID |
| sender_email | AES-256-GCM encrypted |
| sender_name | AES-256-GCM encrypted |
| subject | Sanitized plain text |
| status | Processing status |
| signature | HMAC-SHA256 signature |
| payload_hash | SHA256 hash of full payload |
| signed_at | Signing timestamp |
| processed_at | Processing timestamp |

---

## Installation & Setup

**Prerequisites**
- n8n instance (self-hosted or cloud)
- Gmail account with API access
- Google Sheets API credentials
- Groq AI API key

**Step 1: Configure environment**

Copy `.env.example` and fill in your values:
```
ENCRYPTION_KEY=your_32_byte_hex_key_here
HASH_SECRET=your_32_byte_hex_key_here
GROQ_API_KEY=your_groq_api_key_here
```

**Step 2: Import Workflows**
1. Open n8n → Import from File
2. Import `workflows/workflow_1_style_learner.json`
3. Import `workflows/workflow_2_draft_generator.json`

**Step 3: Configure Credentials in n8n**
- Gmail OAuth2
- Google Sheets OAuth2
- Groq API key (HTTP Header Auth)

**Step 4: Run Style Learner**
- Activate Workflow 1
- It will run on schedule automatically
- Check your Profile sheet to confirm data is populated

**Step 5: Enable Draft Generator**
- Activate Workflow 2
- New incoming emails will trigger automatic draft creation
- Review drafts in Gmail before sending

---

## Project Structure
```
email_assistant/
├── workflows/
│   ├── workflow_1_style_learner.json
│   ├── workflow_2_draft_generator.json
├── docs/
│   ├── architecture_diagram.png
│   ├── poster.pdf
├── examples/
│   ├── email_received.txt
│   ├── ai_draft_generated.txt
│   ├── security_log.json
├── .env.example
├── docker-compose.yml
├── .gitignore
└── README.md
```

---

## Known Limitations

- Requires minimum 25 sent emails for accurate style learning
- Currently supports Gmail only
- Credentials must be configured manually per user

---

## Future Work

- RAG integration with company-specific data
- Feedback loop to learn from your edits
- Multi-user team support

---

## Hackathon Details

**Event:** AgorAI Spring School 2026  
**Team:** Hackonauts — Assia Bendaou & Douae Annasri  
**Institution:** ENSIASD Taroudant  
**Category:** AI-Powered Productivity Tools

---

## License

MIT License © 2026 Assia Bendaou & Douae Annasri

---

## Contact

- GitHub: https://github.com/ASSIABD/email_assistant
- Email: bendaouassia@gmail.com & annasridouae3@gmail.com

---

## Acknowledgments

AgorAI Spring School 2026 · ENSIASD · n8n Community · Groq AI
