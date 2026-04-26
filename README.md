# 🦷 Riley — AI Voice Receptionist for Clove Dental
### Production-Ready · Built on Vapi · Voice + Chat · Structured Output

> *"Voice AI is not just LLM + speech. It requires strong normalization, validation, and flow control to behave like a real system."*

---

## 🎥 Demo

> 📹 **[Watch Voice Demo on YouTube](https://youtu.be/QprvdWT5qEE)**  
> 💬 Works in both **voice call** and **chat** mode

[![Demo Video](https://img.youtube.com/vi/QprvdWT5qEE/maxresdefault.jpg)](https://youtu.be/QprvdWT5qEE)

---

## 📌 What Is This?

**Riley** is a production-ready AI voice receptionist built for **Clove Dental** — one of India's largest dental clinic chains. It handles end-to-end appointment booking over phone/chat, replacing the front desk for scheduling workflows.

The core engineering challenge: **messy human input**. People mispronounce cities, say phone numbers in fragments ("double 4", "oh"), and give vague dates ("tomorrow morning"). Riley normalizes all of it — silently, reliably, without hallucinating — and converts every call into clean, backend-ready structured data.

---

## ✨ Capabilities

| Feature | Detail |
|---|---|
| 🗓️ End-to-end booking | Treatment → City → Name → Phone → Date → Confirm |
| 📞 Phone normalization | `"double 4"` → `44`, `"oh"` → `0`, `"nine eight..."` → `9876543210` |
| 📅 Date normalization | `"tomorrow 8 AM"` → `30 March, 08:00` · `"20 fourth"` → `24` |
| 🏙️ City validation | 8 canonical cities · fuzzy match + confirmation before lock |
| 🛡️ No-hallucination policy | Never guesses missing digits, names, or dates — always re-asks |
| 🚫 Domain guardrails | Politely refuses all non-dental queries |
| 📋 Structured output | Every call → clean JSON sent silently to backend |
| 🔁 Single confirmation | Confirms each field once — no loops, no repetition |

---

## 🧠 System Architecture

```
Caller (Voice / Chat)
        │
        ▼
  ┌─────────────┐
  │  Vapi (Riley)│  ← LLM-powered conversational layer
  └──────┬──────┘
         │
   ┌─────┴──────────────────────────────────┐
   │           Normalization Pipeline        │
   │  ┌──────────────┐  ┌─────────────────┐ │
   │  │Phone Normalizer│  │ Date Normalizer │ │
   │  │"double 4"→44  │  │"tomorrow"→30Mar │ │
   │  └──────────────┘  └─────────────────┘ │
   │  ┌──────────────┐  ┌─────────────────┐ │
   │  │City Validator │  │  Name Lock      │ │
   │  │fuzzy→canonical│  │ exact reuse only│ │
   │  └──────────────┘  └─────────────────┘ │
   └─────────────────────────────────────────┘
         │
         ▼
   ┌─────────────┐
   │  Validation  │  ← no proceed until all fields clean
   │  Layer       │
   └──────┬───────┘
          │
          ▼
   ┌─────────────────────────────────────┐
   │         Structured Output (JSON)     │
   │  {                                   │
   │    "full_name": "Priya Sharma",      │
   │    "phone_number": "9876543210",     │
   │    "city": "Ahmedabad",              │
   │    "treatment": "Root Canal",        │
   │    "appointment_date": "2025-03-30", │
   │    "appointment_time": "09:00",      │
   │    "status": "confirmed"             │
   │  }                                   │
   └─────────────────────────────────────┘
```

---

## 🔑 Key Engineering Decisions

### 1. Phone Number Normalization
Human voice inputs for phone numbers are notoriously noisy. Riley handles:

| Spoken Input | Normalized Output |
|---|---|
| `"double 4"` | `44` |
| `"triple 9"` | `999` |
| `"oh"` / `"o"` / `"naro"` | `0` |
| `"nine eight double seven oh four"` | `987704...` |

**Hard rule:** During confirmation, Riley *only* speaks normalized digits — never echoes raw words like "narrow" or "oh" back to the caller.

---

### 2. Date & Time Normalization

| Spoken Input | Normalized Output |
|---|---|
| `"tomorrow 8 AM"` | `30 March, 08:00` |
| `"20 fourth March"` | `24 March` |
| `"day after tomorrow"` | `31 March` |
| `"next Monday"` | Resolved to absolute date |

**Hard rule:** Dates are always output as `DD Month` (e.g., `30 March`). Ordinal words like "thirtieth of March" are strictly forbidden — they cause TTS mispronunciation.

---

### 3. City Canonical Lock
Allowed cities: **Mumbai · Pune · Ahmedabad · Bangalore · Delhi NCR · Surat · Vadodara · Nashik**

Once confirmed, the city name is **locked** — Riley never re-predicts or re-spells it. This prevents TTS from generating phonetic approximations like "Ahembada" or "Amadabad".

---

### 4. No-Hallucination Validation
Riley operates under a strict **never-guess** policy:
- Missing phone digits → re-ask, never infer
- Unclear city → confirm with closest match before storing
- Ambiguous date → clarify before normalizing
- Off-topic query → deflect, never fabricate an answer

---

### 5. Booking Flow (Strict Order)
```
1. Treatment type
2. City (from allowed list)
3. Full name
4. Phone number
5. Email (optional)
6. Date + Time (accepted together if provided)
7. Final confirmation summary
```
Fields are never reordered. Confirmation happens once — no loops.

---

## 📋 Structured Output Schema

Every completed conversation silently produces:

```json
{
  "full_name": "Priya Sharma",
  "phone_number": "9876543210",
  "city": "Ahmedabad",
  "treatment": "Root Canal",
  "appointment_date": "2025-03-30",
  "appointment_time": "09:00",
  "email": "priya@example.com",
  "status": "confirmed"
}
```

This output is backend-ready for:
- CRM / patient management systems
- Calendar & scheduling APIs
- SMS / WhatsApp confirmation triggers
- Database pipelines

---

## 💬 Sample Conversations

### ✅ Phone Normalization
```
Riley: What's your phone number?
User:  Nine eight seven six five four three two one naro
Riley: I heard 9 8 7 6 5 4 3 2 1 0. Is that correct?
User:  Yes
Riley: Perfect, thank you.
```

### ✅ Date Normalization
```
Riley: What date and time works for you?
User:  Tomorrow 8 AM
Riley: That would be 30 March at 8 AM. Does that work?
User:  Yes
```

### ✅ City Fuzzy Match
```
Riley: Which city are you in?
User:  Ahmedba
Riley: Did you mean Ahmedabad?
User:  Yes
Riley: Got it — Ahmedabad.
```

### 🚫 Domain Guardrail
```
User:  Can you help me find a restaurant nearby?
Riley: I'm here to help with Clove Dental appointments and services.
       How may I assist you?
```

### ✅ Final Confirmation
```
Riley: Just to confirm — booking for Priya Sharma at Clove Dental Ahmedabad
       on 30 March at 9 AM for Root Canal. Is that correct?
User:  Yes
Riley: Your appointment is confirmed. You'll receive details shortly.
```

---

## 🛠️ Tech Stack

| Layer | Tool |
|---|---|
| Voice AI Platform | [Vapi](https://vapi.ai) |
| Assistant Name | Riley |
| Conversation Model | LLM via Vapi |
| Normalization | Custom prompt engineering (rule-based) |
| Output | Structured JSON from conversation logs |
| Modes Supported | Voice call + Chat |

---

## 📁 Repository Structure

```
clove-dental-voice-agent/
│
├── README.md
│
├── prompts/
│   └── system_prompt.md          # Full Riley system prompt
│
├── sample_outputs/
│   ├── booking_confirmed.json     # Standard booking output
│   ├── phone_normalization.json   # Edge case: "double 4", "oh"
│   └── date_normalization.json    # Edge case: "tomorrow 8 AM"
│
├── demo/
│   └── README.md                  # Link to demo video
│
└── docs/
    └── architecture.md            # Flow diagram and design notes
```

---

## 📐 Normalization Rules (Quick Reference)

### Phone Digit Map
```
zero, oh, o, naro, narrow → 0
one                        → 1
two, to, too               → 2
three                      → 3
four, for                  → 4
five                       → 5
six                        → 6
seven                      → 7
eight, ate                 → 8
nine                       → 9

double X → XX   (e.g., double 4 → 44)
triple X → XXX  (e.g., triple 9 → 999)
```

### Date Format Rule
```
Always output: DD Month
Example:       30 March, 5 April, 24 March

Never output:  thirtieth of March
               March 30th
               20 fourth March
```

---

## 🔍 Key Learnings

1. **Voice AI ≠ LLM + Speech** — normalization, validation, and state control are what make it actually work in production.

2. **Phone numbers are the hardest field** — people say them in dozens of formats. A strict digit-mapping layer is non-negotiable.

3. **TTS breaks on ambiguous text** — date formats like "thirtieth of March" cause mispronunciation. `DD Month` format solves this cleanly.

4. **City name locking prevents hallucination** — without a lock rule, the model re-generates city names on each turn, introducing phonetic drift.

5. **Structured output is the real product** — the agent's value isn't just conversation; it's that every call produces clean, usable data.

---

## 🚀 This Pattern Fits

- Medical / dental clinic receptionists
- Restaurant or salon booking agents
- Logistics pickup scheduling
- Any domain: **collect → validate → confirm → log**

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

> ⭐ If this helped you understand production Voice AI, give it a star!
