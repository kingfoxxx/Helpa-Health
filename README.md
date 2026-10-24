# Helpa Health

> AI-assisted, WhatsApp-first mental health triage for Nigeria and, over time, wider African contexts.

I designed and built the Helpa Health MVP independently. This public repository explains the product and engineering decisions; the application code, prompts, credentials, and internal safety configuration remain private.

## The problem

For many people, the first barrier to mental health support is knowing where to begin. Distress may be expressed as exhaustion, worry, isolation, difficulty functioning, or culturally specific language—not as a clinical label.

Nigeria also has limited and unevenly distributed mental health capacity. The challenge is therefore not only creating more care, but building a reliable entry and routing layer around the care that exists.

Helpa is designed to help a person describe what they are experiencing, identify the level and type of concern, and move toward an appropriate next step. It supports triage; it does not diagnose or replace a clinician or emergency service.

## What I built

The MVP is a working conversational triage backend that:

- receives and verifies inbound WhatsApp messages;
- conducts a nine-part mental health check-in through ordinary conversation;
- uses an LLM to extract structured distress signals from free text;
- validates every model response against a strict schema;
- scores distress and assigns domain flags with deterministic rules;
- independently scans every message for explicit crisis language;
- permanently latches a safety override when the crisis threshold is met;
- routes the user toward self-help, monitoring, professional support, or crisis guidance;
- stores session state without persisting raw phone numbers; and
- safely retries failed message delivery without duplicating completed turns.

The product is intentionally split into two responsibilities: AI interprets language; deterministic software decides safety and routing.

## How it is built

### Message flow

~~~mermaid
flowchart TD
    A[User sends a WhatsApp message] --> B[Meta WhatsApp Cloud API]
    B --> C[FastAPI webhook<br/>signature verification and fast acknowledgement]
    C --> D[Python background turn processor]
    D --> E[Load session state<br/>Redis, then PostgreSQL, then memory fallback]
    E --> F[Deterministic crisis-language scan]

    F -->|Explicit crisis phrase| J[Latch safety override]
    F -->|No explicit phrase| G[LLM signal extraction<br/>Claude Haiku or OpenAI]
    G --> H[Strict JSON validation<br/>scores plus confidence]
    H -->|Invalid, unavailable, or low confidence| I[Ask the pending question directly]
    H -->|Valid| K[Deterministic Triage-9 engine<br/>score and domain flags]
    K --> L{Safety threshold reached?}
    L -->|Yes| J
    L -->|No| M[Ask next question or select care path]

    I --> N[Deterministic response templates]
    M --> N
    J --> O[Verified crisis guidance]
    N --> P[Reply through WhatsApp Cloud API]
    O --> P
    P --> Q[Persist state and metadata-only turn record]
~~~

### Backend and integration

The backend is written in **Python 3.12** with **FastAPI**. The webhook validates Meta's request signature, acknowledges the request quickly so WhatsApp does not retry unnecessarily, and hands the conversation turn to background processing.

Outbound replies use the **Meta WhatsApp Cloud API** through an asynchronous HTTP client.

### AI extraction

The default extraction provider is **Anthropic Claude Haiku**. **OpenAI** is supported as a configurable alternative behind the same provider interface.

The model does not return prose for the application to trust. It returns strict JSON containing nullable distress signals for the nine triage dimensions plus a confidence score. Pydantic rejects malformed JSON, extra fields, wrong types, and values outside the accepted range.

A validated extraction is passed to the rules engine. The model never chooses whether to escalate a user.

### Rules, scoring, and routing

The deterministic Triage-9 engine:

1. records validated answers;
2. calculates the core distress score from the first eight dimensions;
3. keeps domain-specific concerns visible instead of reducing everything to one number;
4. evaluates the ninth, safety-focused signal separately;
5. selects a stepped-support path; and
6. prevents a crisis-latched session from being downgraded later.

The output is a structured routing decision with risk level, urgency, care tier, and referral intent. This makes the result usable by future human-support and referral systems without asking another model to reinterpret the conversation.

### Data storage

- **Redis** keeps a fast, expiring copy of active session state.
- **PostgreSQL** stores durable session state and a metadata-only turn ledger.
- **SQLAlchemy async** provides the persistence layer, with **Alembic** migrations.
- An in-memory layer keeps the conversation available during local development or a storage outage.
- Phone numbers are converted to keyed HMAC hashes before storage; raw message content is not written to application logs or the turn ledger.

## Key engineering decisions

### 1. Safety is controlled by fixed rules

An LLM is useful for interpreting language, but probabilistic output should not own a life-critical decision. Helpa uses a deterministic crisis scanner before the model call and a separate rule-based safety threshold after structured extraction. Either path can activate the same latching crisis state.

### 2. The system fails closed when AI is unavailable

If the provider times out, has no API key, returns malformed JSON, or violates the schema, Helpa records no guessed score. It asks the current triage question directly so the conversation can continue without AI. The deterministic crisis scanner remains active even when every model provider is down.

### 3. Low confidence produces a follow-up, not an assumption

A model response below the confidence threshold is discarded for ordinary scoring. The user is asked a clear follow-up question instead. Safety-positive signals are treated conservatively so uncertainty cannot silently suppress a possible risk.

### 4. Crisis routing cannot be downgraded

Crisis and non-crisis decisions use different validated data types. A crisis-latched state cannot be represented as a lighter care path, which makes an accidental downgrade structurally invalid rather than merely discouraged by a comment.

### 5. Delivery favors a second attempt over silence

A WhatsApp message is marked as processed only after the reply attempt completes. If delivery fails, the retry remains eligible. This deliberately accepts the small risk of a duplicate reply rather than losing a response, especially during a crisis turn.

## Testing the AI and safety paths

The test strategy checks both model interpretation and the deterministic boundaries around it:

- **Schema and provider tests** simulate missing keys, unknown providers, malformed output, refusals, timeouts, and low-confidence results.
- **Extraction tests** verify that free text becomes the expected structured signals and that uncertain values are not silently accepted.
- **Adversarial safety tests** cover indirect self-harm language, obfuscation, Nigerian English, Nigerian Pidgin, and an initial Yoruba phrase set.
- **Corpus-based recall evaluation** runs a labeled crisis-language dataset through the deterministic scanner. The scanner-owned subset has a 100% recall merge gate.
- **Golden conversation transcripts** test full multi-turn behavior and protect against regressions in question order, scoring, and crisis interruption.
- **Invariant tests** prove that an activated safety override stays latched and can never route into a lower-risk response.
- **Persistence and delivery tests** cover Redis/PostgreSQL fallback behavior, webhook retries, and duplicate-message handling.

The local-language corpus is engineer-drafted and is not presented as clinically or culturally complete. It requires review and expansion by Nigerian clinicians and fluent local-language speakers; a green automated test proves coverage of the corpus, not coverage of every real-world expression.

## The Triage-9 approach

Triage-9 is a brief conversational check-in across emotional distress, anxiety, stress, functioning, difficult experiences, social connection, and personal safety.

Responses produce an overall distress picture plus separate domain flags. The result is not a diagnosis. It estimates the level of support that may be appropriate:

- self-guided support;
- continued monitoring;
- counselling or professional support;
- prioritized human attention; or
- immediate crisis guidance.

The safety signal sits outside the ordinary total. A possible crisis interrupts the standard flow instead of being averaged into a lower overall score.

## How AI helps

People rarely describe distress in questionnaire language. AI lets Helpa interpret natural conversation while preserving a consistent structured output for the rules engine.

The model's role is deliberately narrow: understand language and propose signals. It cannot set policy, lower a safety state, select a helpline, or decide that a person is safe. This separation makes provider changes possible without changing the product's safety behavior.

## Why this is different

Helpa is not designed as another general-purpose wellness chatbot. It is an attempt to build the missing navigation layer around scarce mental health capacity:

- a familiar WhatsApp entry point;
- structured triage without pretending to diagnose;
- AI-assisted understanding with deterministic safety;
- routing based on level and type of need;
- a future bridge to counsellors, clinics, NGOs, universities, employers, and community programmes; and
- design assumptions that begin with Nigerian access patterns and language rather than adding localization later.

The conversation is the front door. The longer-term product is the infrastructure that identifies, structures, routes, and follows up on need.

## Early pilot feedback

Nine early responses have been collected for the initial product-discovery exercise. This is exploratory feedback, not clinical validation or evidence of improved health outcomes.

The response analysis is still being documented. Rather than inventing conclusions, this public overview will be updated with the questions asked, the strongest recurring findings, and the product changes linked to those findings once that analysis is complete.

Clinical validation will require a larger study, qualified oversight, appropriate ethical review, and culturally representative participation.

## Current status

The MVP backend, triage flow, structured AI extraction, deterministic scoring, safety override, persistence layers, and automated safety tests have been built privately.

Work still required before a real-world launch includes clinical-advisor review, broader Nigerian language and cultural review, verified care partnerships, privacy and legal review, production monitoring, and responsible pilot validation.

## Repository boundary

This public repository contains the project overview only. It does not contain production source code, prompts, credentials, user data, or internal safety configuration.

If you are a Nigerian or African mental health professional, researcher, crisis-support organization, patient advocate, or responsible-AI practitioner interested in this work, please open a GitHub issue.

---

**Important:** Helpa Health is under development and is not a substitute for professional medical advice, diagnosis, treatment, or emergency services. If someone may be in immediate danger, contact local emergency services or a verified crisis-support service in their location.