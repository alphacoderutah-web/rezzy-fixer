# Writing directives that hold

Read this before writing or rewriting a directive.

## Contents

- [Anatomy](#anatomy)
- [The shape that works](#the-shape-that-works)
- [Worked example: stop deferring, start closing](#worked-example-stop-deferring-start-closing)
- [Worked example: suppress a phrase Rezzy insists on](#worked-example-suppress-a-phrase-rezzy-insists-on)
- [Worked example: disclosure that depends on booking stage](#worked-example-disclosure-that-depends-on-booking-stage)
- [Anti-patterns](#anti-patterns)
- [Before you finish](#before-you-finish)

## Anatomy

A directive is a standing instruction — an SOP. Each has a **scenario title**, a **body**, **booking stage** scope, **property** scope, and a drag-order priority. Directives are never shown to guests; they shape what Rezzy says and what it may do without asking.

Write them the way you'd brief a capable new employee who has never met your guests: state the situation, the policy, the exact wording where it matters, and what to do when reality doesn't match the script.

## The shape that works

Directives that survive contact with real guests tend to share a structure:

1. **A trigger sentence** — when this applies, in the guest's vocabulary rather than yours. Guests say "can we get in early," not "same-day early arrival request."
2. **Classification, when the scenario forks.** Requests that look similar often need different answers — arriving earlier *in the day* versus arriving a *day earlier* are different transactions. Naming the categories explicitly stops Rezzy from blending them.
3. **The policy, with real numbers**, for each branch.
4. **The exact sentence to use**, where the answer is knowable in advance.
5. **What to do when it isn't** — the honest fallback, plus creating a task.

Explain *why* a constraint exists where it isn't obvious. Rezzy generalizes better from a stated reason than from a bare prohibition, and so does the next human who reads the directive.

## Worked example: stop deferring, start closing

A host's automated campaign invited guests to ask about extending their stay. Their directive then said:

> Do not calculate, quote, estimate, or promise the price of an additional night. Tell the guest that the team will confirm availability, pricing, and next steps.
> Use this response: "Thanks for checking. We may be able to add that night. I'll have our team confirm availability and pricing, then send you the next steps."

Rezzy followed it exactly, every time. The host experienced this as Rezzy being evasive; the log showed high certainty and named this directive as the Source. Every interested guest stalled until a human intervened.

The fix wasn't a better hedge — it was permitting the answer. Rezzy can quote modifications to existing reservations, so the rewrite told it to check availability, quote a real total, state how payment is handled, ask for confirmation, and create a task for the human step it genuinely cannot do. Completeness on the reproduction case moved from 0.75 to 0.95.

**The transferable lesson:** when a host says the AI won't answer, check whether they told it not to. Look for prohibitions that outlived the reason they were added — often written defensively before the host trusted the tool.

## Worked example: suppress a phrase Rezzy insists on

Same host, next problem. With quoting enabled, Rezzy produced a correct price and then added:

> Airbnb will add their service fee on top when the alteration is processed.

Accurate-sounding, and unwanted — the host handles these charges directly. The reservation genuinely originated on Airbnb, so this wasn't a hallucination; Rezzy was applying channel logic.

A conceptual instruction failed:

> Never mention Airbnb or Vrbo service fees, platform alterations, or how a booking site processes changes.

The same rule as a lexical constraint held:

> When replying to any request covered by this directive, the words "Airbnb" and "Vrbo" must not appear anywhere in your reply. Do not name any booking platform. Do not reference a platform service fee, an alteration being reviewed or processed, or anything the platform will do.

Paired with a mandated sentence for the replacement behavior:

> Then say exactly this: "We'll add that night to your reservation and charge the card we have on file. Nothing needs to be done on your end."

It then held even when a guest asked point-blank whether they should request the change in the Airbnb app.

**The transferable lesson:** when a conceptual instruction has already failed once, don't rewrite it more emphatically — convert it into a word list. Rezzy can reliably check output against specific words in a way it can't against a category.

## Worked example: disclosure that depends on booking stage

A host wanted a rental vendor's phone number given to booked guests but withheld from inquiries, without Rezzy explaining the reason to the guest.

Three mechanisms combined:

- **Directive branches on booking status** — confirmed booking gets the number, inquiry gets "we'll send their details once your booking is confirmed."
- **A word ban on the explanation** — "Airbnb", "Vrbo", "platform", "policy", "rules", "not allowed", "not permitted" excluded from the reply, so the restriction reads as ordinary process rather than an apology about someone else's rules.
- **Stage scoping on the FAQ holding the number** — Pre Arrival, Mid Stay and Post Departure checked; Pre Booking left unchecked.

That last piece is what makes it durable. Even if the directive were later reworded or overridden, the number is structurally unavailable at the inquiry stage.

**The transferable lesson:** for anything that must not leak, pair the instruction with scope. Instructions are guidance; scope is a wall.

## Anti-patterns

**Overlapping deferral instructions.** The single most common cause of "Rezzy won't answer." Generic directives — an unknown-answers rule, a last-word rule — quietly instruct hedging across every scenario. Combined with a scenario-specific hedge, the host edits one and sees no change. Search all directives *and* FAQs for the offending phrase before editing anything, and add explicit carve-outs to the generic ones so they don't swallow scenarios you've deliberately answered.

**Prohibition-only directives.** A list of things not to say, with no replacement, leaves Rezzy to invent filler — usually a hedge. Every "don't" needs a paired "instead, say."

**Directives asking for scheduling.** Instructions like "wait 60 seconds before replying" or "follow up the next day at 10am" do nothing. Rezzy reacts to inbound messages; timed and outbound sends belong to Triggers and Schedules. These sit inertly in the directive list looking like configuration, and hosts believe they're working. Flag them and move the intent to the right feature.

**Ambiguous vocabulary.** A property whose rooms were labeled "Main level" and "Upstairs" produced a genuinely harmful answer: told that the kitchen and a bedroom were on "the main level," a guest travelling with someone with mobility limitations was assured they could stay entirely on one floor. The kitchen was up a flight of stairs. Nothing in the directive was wrong — the vocabulary was. Prefer wording that can't be misread: "ground floor (entry level)" and "second floor."

**Fixing the symptom in the wrong layer.** If the cause is stale data, a directive that instructs around it leaves the bad data live for every other question and channel. Fix data in the data.

## Before you finish

- Re-run the original failing question in the Sandbox, unchanged.
- Run the adversarial version — the guest pushing on exactly what you constrained.
- Compare Completeness before and after.
- Confirm no *other* directive or FAQ still contradicts the new one.
- If the change involves a price or policy, check Templates and Triggers for a stale copy of the old number.
