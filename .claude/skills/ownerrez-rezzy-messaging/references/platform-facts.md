# Rezzy platform facts

How Rezzy actually works underneath. Read this when a behavior looks impossible to change, or before touching messaging settings.

## Contents

- [What Rezzy reads](#what-rezzy-reads)
- [Booking stages and a misleading label](#booking-stages-and-a-misleading-label)
- [Hard guardrails no directive overrides](#hard-guardrails-no-directive-overrides)
- [Settings that override each other](#settings-that-override-each-other)
- [Channel rules before and after booking](#channel-rules-before-and-after-booking)
- [Reading the reasoning log](#reading-the-reasoning-log)
- [Using the Sandbox properly](#using-the-sandbox-properly)

## What Rezzy reads

Rezzy answers from a bounded set of sources. Knowing which ones are in scope tells you where a fix belongs.

**In scope:**
- Property information — description, headline, accommodations, amenities, guest instructions
- Rooms data — bedrooms, bathrooms, beds, and the free-text note on each
- FAQs — both per-property and shared across properties
- Directives — the host's standing instructions
- Live booking and availability data — dates, payments, balances, security deposits, rental agreement status, review status
- Rates and quotes, including pricing for modifications to an existing reservation

**Not in scope — a frequent and costly misunderstanding:**
- **Templates and Triggers.** These send scheduled and event-driven messages to the same guests, but Rezzy cannot read them. A fact that exists only inside a template is invisible to Rezzy, so a guest asking about it gets a hedge. If a phone number, fee or instruction needs to be available to Rezzy, it has to live in an FAQ, a directive, or property data — even if it already appears in a template.

This cuts both ways. Because Templates and Triggers are independent, they can contradict Rezzy. A trigger promising one early check-in fee while a directive states another will produce two different numbers to the same guest. When a complaint involves pricing or policy, check both sides.

## Booking stages and a misleading label

Directives and FAQs are scoped to booking stages. There are seven: **Pre Booking, Pre Arrival, Mid Stay, Post Departure, Canceled, Pending, Other.**

The list view collapses a full selection into the range label **"Pre Booking - Other"**, which reads like a narrow two-stage scope but actually means *all seven selected*. People misread this and conclude their directive is mis-scoped when it's fine — or, worse, conclude it's fine when it's genuinely restricted. Open the edit dialog and look at the checkboxes before drawing conclusions.

Stage scope is a genuine structural control, not just an organizing convenience. Content scoped away from Pre Booking is unavailable to Rezzy while a guest is still inquiring, no matter what any directive says. That makes it the most durable way to prevent premature disclosure of vendor contacts, door codes, addresses or anything else that should follow a confirmed booking.

Directives also carry property scope and a drag-to-reorder priority. When two directives genuinely conflict, prioritization is available — but resolving the conflict in the text is almost always better than relying on ordering, because ordering is invisible in day-to-day use and easy to disturb.

## Hard guardrails no directive overrides

Recognize these quickly. Time spent writing directives against them is wasted, and hosts often assume the AI is being stubborn when it's actually structurally constrained.

**Rezzy cannot create or modify bookings.** It will quote a change accurately, then create a Task for a human to execute it. There is no way to make it add a night or move a date itself. This matters because hosts often want "Rezzy handles extensions end to end" — the achievable version is *Rezzy quotes it accurately, commits to the process, and creates a task*, which captures nearly all the value.

**Forbidden SMS topics produce a fixed canned reply.** Regulated categories on SMS trigger the literal response *"Let me check on that and get back to you"* plus a usage-limitations link, and a Task. No directive changes this wording. Detection has been tightened over time, so a host reporting that Rezzy "suddenly started saying this" on SMS specifically may be hitting this rather than anything they changed. Note the tell: this affects SMS threads, not channel threads.

**Act as Host is unavailable under automatic replies.** See below.

## Settings that override each other

Two override relationships cause most of the "I changed the setting and nothing happened" reports.

**Messaging Mode constrains Escalation Behavior.** Modes are Suggest Manually, Create Drafts, and Reply Automatically. Escalation options include Act as Host, Refer to Host, Refer to Team, Share Direct Host Contact Info, and No Response. Selecting **Reply Automatically disables Act as Host** and forces Refer to Host — the radio button is greyed out in the UI. A host who turned on auto-reply has, without necessarily realizing it, opted into deferring language whenever Rezzy can't resolve something.

Worth framing honestly for the host: this is a real trade-off, not a bug. But it's usually not the thing to change. Escalation only fires when Rezzy *can't* resolve a question, so closing the underlying data or directive gap reduces how often it triggers at all, and preserves auto-reply.

**Schedules override the main settings inside their windows.** Each schedule carries its own Messaging Mode, Escalation Behavior, Conversation Ending, Signature, Tone, Language and property scope. Editing the top-level settings changes nothing during an active schedule window. When a host insists their settings changes had no effect, enumerate the schedules before believing the settings are the problem.

Also present in settings, and worth checking when disclosure timing is the complaint: **OTA Contact Sharing** — Share all after booking / Never share on OTA channels / Phone only after booking.

## Channel rules before and after booking

Rezzy applies Airbnb and Vrbo rules on sharing contact details and off-platform links automatically, and the rules genuinely differ by stage. Before a reservation is confirmed, sharing contact information or direct-booking links is prohibited. After confirmation the restriction relaxes, particularly when the guest is the one asking.

Two practical consequences:

Post-confirmation redirects to "contact Airbnb" are **not** required by platform rules, so a host who wants upsells handled in-house is asking for something legitimate. Rezzy may still volunteer platform-flavored language on channel bookings — describing a date change as an alteration the platform will process, or mentioning a platform service fee — because it recognizes the reservation's origin. Conceptual instructions rarely stop this. A word-level ban does.

Pre-confirmation, the restriction is real and worth respecting. But hosts generally don't want Rezzy narrating it to guests. Stating the process warmly ("we'll send those details once your booking is confirmed") lands better than explaining what a platform does or doesn't permit, and banning the relevant words is the way to get there.

## Reading the reasoning log

`Rezzy AI > Log` records every message processed, with the reasoning behind it. Sandbox answers show the same panel inline. Useful fields:

- **Source** — names the directive or FAQ relied on. This is the single most useful field for diagnosis; it converts "why did it say that" into a lookup.
- **Certainty** and **Completeness** (0–1) — read them together. High Certainty with low Completeness means Rezzy is confident in what it has and correctly signalling that the data doesn't cover the question. That combination points at a data gap, not a directive problem.
- **Response Needed**, **Sentiment**, **Summary**, **Topics** — topic rows include the detected request type and the action taken, such as creating a follow-up task.

Completeness doubles as a regression metric. Capture it before your change and after; a real fix moves it, while a cosmetic rewording leaves it flat.

When several guest messages arrive together, Rezzy coalesces them and responds once to the latest, so only that message carries the log link.

## Using the Sandbox properly

The Sandbox runs live configuration without contacting guests, which makes it the right place to both reproduce a failure and verify a fix.

Set three things: **property**, **booking** (optional but important), and **Thread Type** — Channel, Email, SMS, WhatsApp, or Sandbox.

Attach a real booking whenever the complaint involves anything reservation-specific. Without one, Rezzy has no dates, guest or payment context and will reasonably ask for it, which looks like a different failure than the one being investigated. Thread Type matters for the same reason: channel-rule behavior and SMS-specific restrictions only appear on the matching type, so testing an Airbnb complaint on a Sandbox thread can hide the very thing you're chasing.

Two mechanical notes: changing the property re-renders the form and clears the question box, so set property and thread type first and type the question last. Answers commonly take 20–40 seconds — a blank result usually means it's still running, not that it failed.
