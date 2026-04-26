# 🦷 AI Voice Receptionist — Clove Dental
### Built with Vapi (Riley) · Production-Ready · Voice + Chat

> **"Voice AI is not just LLM + speech. It requires strong normalization, validation, and flow control to behave like a real system."**

---

## 📌 Overview

A production-ready AI Voice Receptionist built for **Clove Dental** using **Vapi (Riley)** — handling end-to-end appointment booking over voice and chat, with strict validation, natural conversation flow, and structured backend-ready output.

This agent replaces the front desk for appointment scheduling — handling messy human inputs (mispronunciations, partial phone numbers, inconsistent dates) while keeping the conversation natural and reliable.

---

## 🎥 Demo

> 📹 **[Watch the Voice Demo Video]** *(add your video link here)*
> 
> 💬 Works in both **voice** and **chat** mode

---

## ✨ What It Can Do

| Capability | Description |
|---|---|
| 🗓️ End-to-end booking | treatment → city → contact → schedule — full flow |
| 📞 Phone normalization | Handles "double 4", "oh" (for zero), partial numbers |
| 📅 Date normalization | "tomorrow 8 AM" → `30 March, 9:00 AM` |
| 🛡️ Strict validation | No hallucination, no guessing — re-asks until clean |
| 🚫 Domain guardrails | Refuses unrelated queries gracefully |
| 📋 Structured output | Every call → clean JSON: name, phone, city, date, treatment, status |

---

## 🧠 How It Works

```
User (Voice/Chat)
       │
       ▼
  Vapi (Riley) ──── Conversation Flow & Turn Management
       │
       ├── Phone Normalization Engine
       │     └── "double 4" → "44", "oh" → "0"
       │
       ├── Date Normalization Engine
       │     └── "tomorrow 8 AM" → absolute datetime
       │
       ├── Validation Layer
       │     └── re-prompts on invalid inputs, no hallucination
       │
       └── Domain Guardrails
             └── off-topic queries → polite refusal
       │
       ▼
  Structured Output (JSON)
  {
    "full_name": "...",
    "phone_number": "...",
    "city": "...",
    "appointment_datetime": "...",
    "treatment": "...",
    "status": "confirmed"
  }
```

---

## 🔑 Key Engineering Decisions

### 1. Phone Number Normalization
Human voice inputs for phone numbers are notoriously noisy. The agent handles:
- **Word-based digits:** "double 4" → `44`, "triple 9" → `999`
- **Letter substitution:** "oh" → `0`, "I" → `1`
- **Partial inputs:** Re-asks for missing digits without losing context
- **Format validation:** Confirms 10-digit Indian mobile numbers before proceeding

### 2. Date & Time Normalization
Converts natural language time references into absolute datetimes:
- `"tomorrow"` → resolves to next calendar date
- `"this Friday"` → resolves to exact date
- `"morning"` → defaults to `9:00 AM` unless specified
- All times anchored to IST (Asia/Kolkata)

### 3. Strict No-Hallucination Policy
The agent **never assumes or guesses** — if a field is unclear:
- It re-asks with a specific, targeted clarification prompt
- It does not proceed until all required fields are validated
- Out-of-scope queries receive a polite deflection

### 4. Conversation Flow Control
Unlike simple chatbots, this agent manages **multi-turn state**:
- Tracks which fields are collected vs. pending
- Handles interruptions and corrections mid-conversation
- Confirms the full booking summary before finalizing

---

## 📋 Structured Output Schema

Every completed call is converted into backend-ready structured data:

```json
{
  "full_name": "Priya Sharma",
  "phone_number": "9876543210",
  "city": "Mumbai",
  "treatment": "Root Canal",
  "appointment_date": "2025-03-30",
  "appointment_time": "09:00",
  "status": "confirmed"
}
```

This output can be directly consumed by:
- CRM systems
- Calendar / scheduling APIs
- Database pipelines
- Notification services (SMS, WhatsApp)

---

## 🛠️ Tech Stack

| Layer | Tool |
|---|---|
| Voice AI Platform | [Vapi](https://vapi.ai) (Riley assistant) |
| Conversation Model | LLM via Vapi |
| Normalization Logic | Custom prompt engineering + validation rules |
| Output Format | Structured JSON from conversation logs |
| Modes | Voice call + Chat |

---

## 📁 Repository Structure

```
clove-dental-voice-agent/
│
├── README.md                    # This file
├── demo/
│   └── demo_video.mp4           # Voice demo recording (or link)
│
├── prompts/
│   ├── system_prompt.md         # Full agent system prompt
│   ├── normalization_rules.md   # Phone & date normalization logic
│   └── guardrails.md            # Domain restriction rules
│
├── sample_outputs/
│   ├── sample_booking_1.json    # Example structured output
│   ├── sample_booking_2.json    # Edge case: phone normalization
│   └── sample_booking_3.json    # Edge case: date normalization
│
└── docs/
    └── architecture.md          # Flow diagram and design notes
```

---

## 🧪 Sample Conversations

### ✅ Phone Normalization
```
Agent: What's your phone number?
User:  My number is nine eight double seven oh four three two one six
Agent: Got it — 9877043216. Is that correct?
User:  Yes
```

### ✅ Date Normalization
```
Agent: When would you like your appointment?
User:  Tomorrow morning
Agent: I've got tomorrow, 30th March at 9:00 AM. Does that work?
User:  Make it 11
Agent: Updated to 30th March at 11:00 AM. Confirmed!
```

### 🚫 Domain Guardrail
```
User:  Can you help me order food?
Agent: I'm Riley, Clove Dental's appointment assistant. 
       I can only help with dental appointment bookings. 
       Would you like to schedule a visit?
```

---

## 🔍 Key Learnings

1. **Voice AI ≠ LLM + Speech** — You need normalization, validation, and flow control to handle real human inputs reliably.

2. **Phone numbers are hard** — People say them in dozens of ways. A robust normalization layer is non-negotiable for any voice booking system.

3. **Structured output is the real value** — The agent isn't just a chatbot. Every call produces backend-ready data, making it actually usable in production pipelines.

4. **Guardrails prevent scope creep** — Without strict domain restrictions, voice agents get pulled into all sorts of unrelated conversations.

---

## 🚀 Use Cases This Pattern Fits

- Dental / medical clinic receptionists
- Restaurant reservation systems  
- Salon & spa booking agents
- Any domain requiring: collect info → validate → confirm → log

---

## 👩‍💻 Author

**Sakshi Malhotra** — Data Scientist · AI Engineer  
📧 sakshi.malhotra.connect@gmail.com  
🔗 [LinkedIn](https://linkedin.com/in/sakshi-malhotra18) · [GitHub](https://github.com/SakshiMalhotra18)  
🌐 [Portfolio](https://portfolio-gray-one-77.vercel.app/)

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

> ⭐ If this project helped you understand Voice AI, give it a star!
