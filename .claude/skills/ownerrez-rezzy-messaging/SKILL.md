---
name: ownerrez-rezzy-messaging
description: Diagnose and fix wrong, evasive, or off-policy replies from Rezzy AI, the guest-messaging assistant built into OwnerRez. Use this whenever someone mentions Rezzy, OwnerRez guest messaging, or Rezzy Directives/FAQs — and especially when they report that their vacation-rental AI told a guest something inaccurate, hedged with "I'll check on that and get back to you", quoted a price it shouldn't have, refused to quote one it should have, leaked info before booking, or pushed guests to Airbnb/Vrbo for something the host handles in-house. Also use when asked to change how Rezzy answers a particular kind of guest question, or to make it stop repeating a mistake.
---

# Fixing Rezzy AI guest replies

Rezzy is OwnerRez's built-in AI messaging assistant. It reads inbound guest messages across Airbnb, Vrbo, Booking.com, SMS, email and WhatsApp, and drafts or sends replies based on the host's own configuration.

Hosts arrive frustrated, usually saying some version of "Rezzy keeps doing X and I've tried telling it not to." Take that frustration seriously but not literally. Rezzy is unusually obedient — that is the root of most complaints about it. It follows the host's directives closely and reports the host's property data faithfully, including when that data is wrong or contradictory. The behavior the host wants to eliminate is, more often than not, something they configured themselves and have forgotten about.

So the highest-value thing this skill does is stop you from immediately editing a directive.

## Diagnose before you edit

Four different things produce a bad Rezzy reply. Only one of them is fixed by writing a better directive, so guessing wastes the host's time and often makes things worse by piling a fourth instruction on top of three that already conflict.

**1. Wrong or missing source data.** Rezzy said what your property record says. A listing description containing a stale price, a room labeled with ambiguous wording, an amenity with no detail recorded. This is the most common cause by a wide margin, and the fix is in the property data, not in Rezzy. If the data is merely *missing*, Rezzy hedges — which hosts read as evasiveness but is actually the safety behavior working correctly.

**2. Self-inflicted deferral.** The host wrote the hedge themselves, usually in several places at once. A directive that says "do not quote pricing, tell the guest the team will follow up," an FAQ with the same instruction, plus generic catch-alls like an "Unknown Answers" directive and a "last word" directive that both independently say *say we're checking*. Editing one changes nothing because the others still fire. Always count them before touching any.

**3. A hard guardrail.** Some behavior is baked into Rezzy and no directive overrides it. Recognize these fast so you can design around them instead of fighting them. See `references/platform-facts.md`.

**4. A genuine gap.** No directive covers the scenario, so Rezzy improvises or defers. This is the case where writing a new directive is the right move — and it's the least common of the four.

## The loop

**Read the real conversation first.** Open the thread in the Inbox and read what the guest actually asked and what Rezzy actually said. Hosts paraphrase, and the paraphrase usually drops the detail that explains the failure. Note whether the guest had a confirmed booking or was still inquiring — Rezzy behaves differently by booking stage, and so should your fix.

**Reproduce it in the Sandbox.** `Rezzy AI > Sandbox` runs the live configuration without contacting anyone. Set the same property, attach the same booking where relevant, and match the Thread Type to the real channel. An unattached Sandbox question tests something different from a real thread, because Rezzy loses the booking context it would normally have.

**Read the reasoning, not just the reply.** This is the step people skip and it's the one that saves the most time. Below each Sandbox answer, and in `Rezzy AI > Log`, Rezzy exposes its own reasoning: a Summary, per-topic rows naming the **Source** it relied on, and numeric **Certainty** and **Completeness** scores. The Source field will often name the exact directive or FAQ causing the behavior, which turns a guessing game into a lookup. Low Completeness with high Certainty is the signature of cause #1 — Rezzy is confident about what it knows and correctly flagging that it doesn't know enough.

**Search before you write.** Once you know the phrasing to hunt for, grep every directive and FAQ for it. If the host complains about "I'll check and get back to you," find every instruction that produces that sentence. Fixing one of four is indistinguishable from fixing none.

**Change the smallest correct thing**, then re-run the identical Sandbox test. Compare the reply *and* the Completeness score — a rise from ~0.75 to ~0.95 is good evidence the source gap actually closed rather than the wording just getting shuffled. Then try the adversarial version of the question, where the guest pushes on the exact thing you just constrained.

## Techniques that hold

Rezzy responds to some kinds of instruction far more reliably than others. These are worth knowing before you write anything.

**Ban words, not concepts.** Conceptual instructions like "don't mention platform fees" or "don't explain our policy reasons" fail regularly. The same rule written as a lexical constraint holds: *the words "Airbnb", "Vrbo", "platform" and "policy" must not appear anywhere in your reply.* Rezzy can check its own output against a word list in a way it can't against an abstraction. Reach for this whenever a conceptual instruction has already failed once.

**Give the exact sentence.** Rezzy reproduces mandated scripts almost verbatim. That's why bad scripts are so sticky — and it makes a well-written script the most reliable tool available. When the desired answer is knowable in advance, write it out and tell Rezzy to use it.

**Scope as a structural guard.** Directives and FAQs are scoped to booking stages, so you can make information *unreachable* rather than merely discouraged. If a vendor's phone number must not go out before booking, put it in an FAQ scoped to Pre Arrival and Mid Stay and leave Pre Booking unchecked. That survives a directive being overridden or reworded later, which a "please don't share this" instruction does not.

**Say what to do, not only what to avoid.** A directive built entirely from prohibitions leaves Rezzy with nothing to say, and it fills the silence with a hedge. Pair every restriction with the replacement behavior.

More patterns, worked examples and anti-patterns: `references/directive-patterns.md`.

## Guardrails when making changes

Rezzy configuration is live. Auto-reply may be on, so edits can reach real guests within minutes. A few rules keep this safe.

**Never invent facts about the property.** Floor layouts, stair counts, step-free access, bed sizes, occupancy limits, fees. If the host hasn't told you and the data doesn't say, ask. Accessibility answers deserve particular care: telling a guest with mobility limitations that they can avoid stairs, when they can't, is worse than any hedge Rezzy would have produced on its own. A missing fact is a question for the host, never a gap to fill with a plausible guess.

**Separate data cleanup from commercial decisions.** Removing a third-party vendor's stale price is cleanup. Changing the host's own fee, occupancy cap, or a headline selling point is a business decision — surface it and let them choose, even when they've given broad authority to proceed. The distinction is whether you're correcting a fact or setting policy.

**Treat listing descriptions as published, not internal.** Property descriptions syndicate to Airbnb, Vrbo, Booking.com and often dozens of smaller channels. Correcting one is routine; rewriting marketing claims across many fields is not. When a small requested fix turns out to span many fields, say so and get confirmation before proceeding.

**Verify every save.** OwnerRez's editors have real quirks: select-all in a text field can append instead of replacing, and a Save button sitting below the fold can be missed entirely while the page still looks edited. Both fail silently and leave contradictory config live. After saving, reload the page and re-read the field. Never rely on the value you set still being in the DOM as proof it persisted.

**Check the outbound side for contradictions.** Templates and Triggers are *not* Rezzy data sources — Rezzy can't read them — but they message the same guests. A template promising one fee while a directive states another produces a guest-visible contradiction that looks exactly like a Rezzy bug and isn't. When a complaint involves a price or a policy, check both.

## Where things live

Everything is in the OwnerRez web UI. **There is no API for Rezzy configuration**, and messaging endpoints are unavailable to Personal Access Tokens, so browser access to the host's account is required. Don't promise an API-based fix.

| Area | Path |
|---|---|
| Settings, schedules | `Rezzy AI > Overview` |
| Shared FAQs (all properties) | `Rezzy AI > FAQs` |
| Directives | `Rezzy AI > Directives` |
| Safe testing | `Rezzy AI > Sandbox` |
| Reasoning logs | `Rezzy AI > Log` |
| Per-property FAQs | `Properties > [property] > FAQs` |
| Room/bed/floor data | `Properties > [property] > Rooms` |
| Listing copy | `Properties > [property] > Description` |

## Reference files

Read these when the situation calls for them rather than upfront.

- **`references/platform-facts.md`** — how Rezzy's data sources rank, the seven booking stages and a UI label that misleads people, the hard guardrails no directive beats, and the settings that silently override each other. Read this when a behavior seems impossible to change, or before touching Messaging Mode, Escalation, or Schedules.

- **`references/directive-patterns.md`** — the anatomy of a directive, worked before/after examples drawn from real fixes, and the anti-patterns that quietly waste a host's effort. Read this before writing or rewriting any directive.
