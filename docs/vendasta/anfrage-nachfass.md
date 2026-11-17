# Anfrage an Vendasta: Appliance an die AI Workforce anbinden

Status: Entwurf, **nicht gesendet**. Liegt als Gmail-Entwurf im Thread "AI Workforce Conversation API" (Thread 1a1a6ed416f0e71c). Empfänger wie am 04.11.2026: Jehan Zouak, Dustin Walker, Josh Baker.

Betreff: Re: AI Workforce Conversation API

Hi Josh, Dustin, Jehan,

following up on my note of 4 November with concrete requirements. In short: we need an API that lets our in-room smart speaker talk to our AI Employee as one more channel, next to phone, web chat, SMS, email, Instagram and Facebook.

WHERE WE ARE
We already run the AI Voice Receptionist in Germany in a live hotel, and it works well. But it is a phone channel. A caller has to dial a number. That does not work for a device in a hotel room: the device is not a phone, it must not dial a number, and it knows the room and the stay instead of a caller ID. A phone number is the wrong identity and the wrong transport for this. We need an API.

WHAT WE HAVE
- A small speaker for hotel rooms (open source and OEM hardware). The wake word is detected on the device. Only after the wake word, audio goes to our EU backend. The guest borrows it at reception, and it is bound to the room and the stay.
- Our AI Employee is live in hotels with custom capabilities that call our own API. We can handle speech to text and text to speech ourselves, so we can send text or audio, whichever you support.

WHAT WE WANT
The speaker should behave like any other channel: same Knowledge Base, same capabilities and tools, the conversation logged on the contact in Conversations and CRM, the guest recognised, and handover to the team when needed.

THE PATHS WE SEE (please tell us which of these exist)
A. Text API: send a message to a given AI Employee in the context of a contact or conversation and receive the reply (synchronous, streamed or by webhook). We do speech ourselves.
B. Custom channel in Conversations: register an external channel (for example "In-room device") with inbound and outbound messages by API or webhook, shown in the Inbox, and assignable to an AI Employee like SMS or WhatsApp.
C. Voice API: connect an external audio stream to the AI Voice Receptionist by WebRTC, WebSocket or SIP, without dialing a public number, with our own session identity and context.
D. Anything else, for example a public Agent API, an MCP endpoint for the AI Employee, or an embeddable realtime voice SDK.
Dialing the Conversations number from the device would work technically, but we want to avoid it: cost per minute, narrow band audio, no barge-in and identification only by number.

OUR QUESTIONS
1. Is there an API today to send a user message to a specific AI Employee and get the reply, outside of the web chat widget? What is the auth model, are there rate limits, is streaming supported?
2. Can we create a custom channel in Conversations so that messages appear in the Inbox, the contact is created or matched, and the AI Employee answers there? How does the AI know the channel is a spoken, in-room channel (channel specific instructions)?
3. Voice without a phone number: can an external audio source connect to the AI Voice Receptionist through an API? Can we pass a contact ID or custom attributes (room, stay dates, language) as context at the start of the session, so the CRM lookup does not depend on a phone number? Is barge-in supported, and what latency do you target?
4. Tools: will our existing Custom Capabilities and tool headers work unchanged on such a channel? Can the channel or device ID be a variable in tool calls?
5. Editions and pricing: which edition does the channel or API need, and how is it priced (per message, per minute)? Can it be rolled out through Blueprints to several accounts?
6. Data protection: where are audio and text processed, is an EU region available, what is retained, and which subprocessors are involved? Our guests are in the EU and the device must not feel like eavesdropping.
7. Timeline: is any of this planned, and can we join an early access program? We can offer a live pilot hotel with a running AI Employee and detailed feedback.

If none of these paths exist or are planned, a short note would help us plan accordingly. Could we also schedule a 30 minute call next week?

Thank you very much and kind regards
Thomas
