# Helpa Health

> Helping people move from silent distress to the right next step.

Helpa Health is building an AI-assisted, WhatsApp-first mental health triage experience for Nigeria, with the long-term ambition of serving communities across Africa.

This is a public project overview. The product source code, safety logic, prompts, infrastructure configuration, and private research remain in a separate private repository.

## The problem

For many people, the hardest part of getting mental health support is not knowing where to begin.

Someone may know that they are struggling but not know whether what they feel is temporary stress, something that needs professional attention, or an urgent safety concern. Stigma, cost, uncertainty, limited awareness, and uneven access to mental health professionals make that first step even harder.

At the same time, scarce care capacity is often difficult to navigate. People who need light support may have no accessible starting point, while people who need urgent help may not be identified and routed quickly enough.

Helpa Health is focused on that missing first layer: helping people describe what they are experiencing, understand the seriousness of their distress, and reach an appropriate next step.

## What we are building

Helpa is not intended to replace a therapist, psychiatrist, emergency service, or trusted human relationship. It is designed to become a safe front door into mental health support.

Through a familiar conversational channel, Helpa aims to:

- make it easier to begin talking about emotional distress;
- turn a person's own words into structured, reviewable signals;
- identify important areas of concern without presenting a diagnosis;
- recognize when safety may be at risk;
- suggest a level of support that matches the person's needs; and
- help people move toward self-help, continued monitoring, community support, professional care, or urgent help.

The experience is being designed around WhatsApp because access should meet people in a channel they already understand, rather than asking them to find, download, and learn another platform before they can ask for help.

## How AI helps

Mental health does not always arrive in neat clinical language. People speak through stories, slang, short replies, uncertainty, humour, silence, and culturally specific expressions of distress.

AI can help interpret that free-form conversation and organize it into consistent signals. This can make a structured check-in feel less like a form and more like a conversation, while allowing the service to support more people than a fully manual first-contact process could.

Helpa's approach gives AI a deliberately limited role:

- AI helps understand language and extract possible distress signals.
- Structured rules calculate scores and determine care pathways.
- Safety-sensitive decisions are not left to a model's improvisation.
- Unclear or low-confidence interpretations lead to clarification, not guessing.
- Crisis guidance must remain available even when an AI provider is unavailable.

The aim is not an all-knowing chatbot. It is a carefully governed partnership between conversational AI, explicit safety rules, clinical review, and human care networks.

## The Triage-9 approach

Helpa's early triage model uses a brief nine-part conversational check-in called **Triage-9**.

The check-in looks across several dimensions of wellbeing, including emotional distress, anxiety, stress, day-to-day functioning, difficult experiences, social connection, and personal safety.

A typical journey is:

**Conversation → structured signals → distress picture → safety check → appropriate next step**

### Distress scoring

Responses are organized into an overall picture of current distress. The score is not a diagnosis and is not meant to label a person. It is a way to estimate the intensity of support that may be appropriate.

Helpa also keeps important areas of concern visible instead of reducing everything to one number. Two people with a similar overall score may need different support because their experiences and risks are different.

### Safety comes first

The safety portion of the check-in is treated differently from ordinary scoring. A possible crisis should interrupt the usual flow and move the conversation toward immediate, verified support.

This safety boundary is intentionally designed to be deterministic and conservative. AI may help recognize language, but explicit rules—not a model's opinion—control escalation.

### Stepped support

The resulting pathway can range from:

- practical self-guided support;
- continued check-ins and monitoring;
- encouragement to speak with a counsellor or other professional;
- prioritized human support; or
- immediate crisis resources.

These pathways are intended to connect people to the least intensive support that can safely meet their needs, while escalating quickly when risk is higher.

## Closing the gap in Nigeria and Africa

Africa's mental health challenge is not only about the number of professionals. It is also about the missing connective infrastructure around the care that already exists: early identification, trusted entry points, consistent triage, appropriate routing, follow-up, and feedback.

Helpa's long-term vision is to help build that connective layer.

A WhatsApp-first entry point can lower the practical barrier to starting a conversation. Structured triage can help limited human capacity focus where it is most needed. Partnerships with counsellors, clinics, NGOs, universities, employers, community organizations, and public programmes could turn a digital first step into a real path toward care.

This cannot be achieved by copying a model built for another context. Language, trust, family and community relationships, stigma, available services, and expressions of distress vary across countries and cultures. Local clinical review and cultural adaptation are requirements, not optional polish.

## The innovative perspective

Helpa is not simply trying to build another wellness chatbot.

The larger idea is to create a mental health navigation layer: infrastructure that helps people enter the system, helps care providers understand the level and type of need, and helps institutions see where support capacity is missing.

The conversational experience is the front door. The deeper innovation is the structure behind it:

- meeting people in a familiar channel;
- combining AI accessibility with rule-based safety;
- separating distress detection from diagnosis;
- routing people according to need rather than treating every case the same;
- supporting task-sharing without pretending that technology replaces professionals; and
- designing for the realities of African care systems from the beginning.

## Planned nine-person learning pilot

The first pilot is intentionally small: **nine participants**.

Its purpose is to learn, not to make clinical-effectiveness claims. The pilot will explore questions such as:

- Does the conversation feel understandable and respectful?
- Are the questions easy to answer in natural language?
- Where do people hesitate, disengage, or feel misunderstood?
- Does the resulting guidance feel clear and actionable?
- Which Nigerian expressions of distress need better recognition?
- What would make people trust—or distrust—the experience?
- Where must a human become involved sooner?

Feedback from this pilot will be used to refine language, conversation flow, safety testing, and future research plans. A nine-person pilot cannot validate a clinical instrument or prove improved health outcomes; those require larger studies, clinical oversight, and appropriate ethical review.

## Principles guiding the work

- **Triage, not diagnosis.**
- **Safety before engagement.**
- **Clarify rather than guess.**
- **Collect and expose as little sensitive information as possible.**
- **Require meaningful consent before any future data handoff.**
- **Use current, verified crisis resources.**
- **Design with local communities, not merely for them.**
- **Keep clinical and legal judgment with qualified humans.**
- **Be honest about what has and has not yet been validated.**

## Current stage

Helpa Health is currently an MVP under active development. The core concept, conversational triage flow, distress structuring, safety boundaries, and routing approach are being developed and tested privately.

Before a real-world launch, the project requires continued clinical-advisor review, cultural and linguistic review, privacy and legal review, verified care partnerships, and responsible pilot validation.

## Public and private boundaries

This repository exists so the idea, mission, product direction, and learning journey can be discussed openly without exposing private implementation details.

It intentionally contains:

- this public overview; and
- no production source code, private prompts, credentials, user information, or internal safety configurations.

## Collaboration

Helpa Health welcomes thoughtful conversations with Nigerian and African mental health professionals, researchers, crisis-support organizations, community leaders, patient advocates, responsible-AI practitioners, and institutions interested in improving access to care.

If this mission overlaps with your work, please open a GitHub issue to start a conversation.

---

**Important:** Helpa Health is under development and is not currently a substitute for professional medical advice, diagnosis, treatment, or emergency services. If you or someone else may be in immediate danger, contact local emergency services or a verified crisis-support service in your location.