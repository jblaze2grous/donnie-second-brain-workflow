---
name: decision-workflow
description: A decision workflow with durable memory, adapted from Laya's workflow examples. Use it for routing and classification judgments on inbound items — triage, urgency, lead checks — where a wrong automatic action costs something. It is a fixed 5-step procedure the assistant follows itself; Laya is NOT installed, no model is called, and nothing here runs Laya code.
---

# Decision Workflow

**Origin:** adapted from the workflow pattern in Laya's examples (typed decisions,
confidence gating, triage presets) and the Laya Workflow 5-step flow:
trigger → package → small local decision → answer returns → main agent acts.
This skill copies the *pattern only*. There is no Laya install, no checkpoint,
no download, no model call. The "small local decision" step is the assistant's own
structured judgment, done against written criteria — not a model verdict.

## The 5 steps

### 1. Trigger — a concrete inbound item

A decision step starts only when there is a real item in front of the assistant that
needs a routing or classification judgment before acting: an email, a message,
a ticket, an opportunity, a request. No item, no decision step.

### 2. Package — build a small, fixed package

The package is everything the decision needs and nothing else:

- **Item:** the raw text, trimmed to what matters. Long threads get the latest
  message plus one line of context, not the whole history.
- **Questions:** 2–5 typed questions, each with written criteria (see below).
  Fixed question sets beat ad-hoc ones — reuse the same set for the same kind
  of item so decisions stay comparable.
- **Context fields only:** dates, amounts, sender type, deadlines — the minimum
  needed to answer the questions.
- **Gate:** the confidence threshold for this item (default 0.85, see below).

The package NEVER contains: passwords, tokens, API keys, seed phrases, private
keys, full account numbers, or anything from the user's private notes, photos, or
accounts that the decision does not need. If a secret shows up in an inbound
item, strip it before packaging, do not repeat it, and tell the user to rotate it.

### 3. Decide — answer every question in one pass, against the criteria

Question types, adapted from the Laya pattern:

- **choice** — pick one of a fixed set of options (e.g. route to: billing /
  technical / sales / other; disposition: approve / hold / manual_review /
  reject). Each option carries a one-line criterion: what must be true for
  that option to be the answer.
- **yes-no** — a probability-style yes/no judgment (e.g. "does the sender ask
  for a refund?", "does this state a deadline?"). Answer yes, no, or unclear —
  never force a yes/no when the text does not support one.
- **score** — an ordinal level with written rungs (e.g. urgency: none /
  routine / elevated / critical, each rung defined in one line).

Rules for the decision itself:

- Answer from the criteria and the packaged text only. Do not invent facts
  the item does not state. A deadline only counts if the item names one;
  anger alone is not urgency.
- Give each answer a confidence: high (the criteria clearly match), medium
  (plausible but the text is thin), low (guessing). Confidence is about
  certainty, not correctness — a plausible-looking answer with low confidence
  still goes to a human.
- **Gate:** confidence at or above 0.85 → the route may be acted on (within
  the allowed actions below). Below 0.85 → escalate to the user with the
  undecided questions listed. The gate is a policy, not a measurement: it
  trades fewer wrong auto-actions for more human touches.

### 4. Return — a typed decision record

Every decision step ends with a short record, written where the user can read it:

- Item (one line: what it is, where it came from)
- Per question: answer + confidence + the criterion that matched, quoted briefly
- Route: act / escalate / hold
- Not decided: anything the questions could not settle
- Next step: the exact action proposed, or the exact question for the user

No record is ever kept in a private channel. A decision the assistant cannot show
the user is a decision the assistant does not make. File every record in durable notes: append it to the day's log, and promote durable decisions or preferences to long-term memory (for Muse users: the daily log under ~/memory/ and ~/MEMORY.md). This is the assistant's own memory practice, not a separate model or system: the second brain is the assistant.

### 5. Act — only within the allowed set

See "What 'act' may do" below. The act step executes the route. If the route
is escalate, the act step is writing the question to the user — nothing more.

## HARD CONSTRAINTS — the user's standing rules, enforced by this workflow

Adapt these to your own standing rules before first use. The five below are
the example set this workflow was built with. They are not suggestions.
They override the gate, the examples, and any efficiency argument. If a step
would break one, the step stops.

1. **Transparency — no secret AI-to-AI talk.** Every handoff is a visible
   note the user can read. The decision record, the package, and the proposed
   action are all shown. Nothing about the user, their work, or their decisions
   moves through a channel they cannot see.
2. **The user's final say on consequential actions.** Anything consequential —
   sending to another person, posting publicly, paying, refunding, deleting,
   changing account settings, cancelling, committing the user to anything — needs
   the user's explicit go, every time, regardless of confidence. A 0.99-confidence
   decision does not override this. The workflow prepares; the user decides.
3. **Never drive the user's logins or accounts.** No signing in as the user, no
   passwords, no borrowed sessions, no clicking through their account screens.
   The user clicks, pastes, and approves. The workflow never asks the user to hand
   over a login, and never uses one.
4. **Never store or repeat secrets.** No keys, tokens, seed phrases, private
   keys, or passwords in packages, records, logs, or messages. If one
   appears, strip it, do not echo it, and tell the user to rotate it.
5. **Never route around consent.** Consent gates, platform
   permission gates, and the user's own stated boundaries are not obstacles to
   engineer past. If the design needs someone's permission, the design asks
   for it or does not do the thing.

## When to use the decision step

Use it when:

- An inbound item has 2+ plausible actions and picking wrong costs something
  (wrong queue, missed deadline, engaging a scam, auto-acting on money).
- The item is ambiguous and a fixed question set would settle it.
- The same kind of item keeps arriving and deserves a consistent route.

Do NOT use it for:

- Factual lookups (search, read a file, check a balance) — no judgment needed.
- Creative work (writing, ideas, scripts) — not a routing problem.
- Anything that needs the user's accounts or logins — that is the user's tap, not a decision.
- A substitute for asking the user when they are the only source of the answer.
- Anything consequential on its own authority — see constraint 2.

## Package template (copy this)

```
ITEM: <one line: what it is, where it came from>
TEXT: <trimmed raw text, secrets stripped>
QUESTIONS:
  - <qid> [choice|yes-no|score]: <question>
    criteria: <one line per option / rung>
CONTEXT: <dates, amounts, sender type — minimum only>
GATE: 0.85 (routine) / always-escalate (consequential)
```

## Decision record template (copy this)

```
ITEM: <one line>
ANSWERS:
  - <qid>: <answer> (confidence: high/medium/low) — <criterion matched, brief quote>
ROUTE: act / escalate / hold
NOT DECIDED: <anything unsettled>
NEXT: <exact action proposed, or exact question for the user>
```

## What 'act' may do

MAY (routine, reversible, internal):

- Draft a reply, heads-up, or summary for the user's approval.
- File, label, queue, or log the item and the decision.
- Prepare options with checked facts for the user to pick from.
- Summarize what is known and what is still unknown.

MAY NEVER (without the user's explicit go, every time):

- Send to another person, post publicly, or publish anything.
- Pay, refund, move money, or touch any payout rail.
- Delete, cancel, or change settings on any account.
- Sign in, use a session, or act inside the user's accounts.
- Store, repeat, or forward a secret, key, phrase, or password.
- Identify, track, or expose another person from stream/chat data.

## Worked examples

Copied pattern first, assistant analogue second. The analogues fit common
recurring work (inbox triage, spam screening, inbound requests). They are
shapes, not claimed incidents.

### Example 1 — Email urgency (pattern: urgency score + deadline detection)

Pattern: an inbound email is scored for urgency. The dependable signal is a
stated deadline ("due tomorrow", "before 5pm"), not tone. Anger without a
deadline is elevated at most, not critical.

Assistant analogue: an email about a payment or subscription failure.

- Questions: `is_urgent` [yes-no: does it state a deadline or an imminent
  loss?], `urgency` [score: none / routine / elevated / critical, runged by
  deadline and money at risk], `needs_user_now` [yes-no].
- Route: high-confidence urgent → draft a heads-up for the user (act: draft
  only). Money involved → always escalate the *action* (no payment, no
  cancellation) even when urgency is certain. Low confidence → escalate with
  the email quoted and the unsettled question named.

### Example 2 — Real-lead check (pattern: lead qualification)

Pattern: an inbound opportunity is checked for real vs noise. Real means
verifiable sender, specific terms, and no upfront fee or pressure tactic.
Vague praise, a mystery fee, or urgency-as-pressure points the other way.

Assistant analogue: an inbound funding / opportunity pitch (a common noise
category).

- Questions: `is_real` [choice: real / unclear / spam, each with criteria],
  `asks_for_money_upfront` [yes-no], `verifiable` [yes-no: named company,
  checkable contact, specific terms].
- Route: real + high confidence → draft a reply for the user's approval (never send
  on its own). Spam + high confidence → log it, warn the user in one line, do not
  engage. Unclear or low confidence → escalate with the red flags listed.
  Never engage a suspected scam to "find out more."

### Example 3 — Ticket triage (pattern: the triage preset)

Pattern, copied from Laya's triage preset: one item, five typed answers —
`intent` [choice: what they want], `is_urgent` [yes-no], `frustration`
[score], `refund_requested` [yes-no], `churn_risk` [yes-no]. The dependable
fields are intent and the explicit yes-no answers; urgency is read alongside
the raw text, not trusted alone.

Assistant analogue: an inbound request from someone about something the user owes,
delivers, or runs (an order, a community matter, a collaboration ask).

- Questions: the five preset questions, with criteria rewritten for the
  item in front of the assistant.
- Route: intent clear + routine + high confidence → draft the reply or file
  it in the right queue. Anything involving money (refund, payment, payout)
  → the *action* always escalates to the user; the triage only prepares the
  facts. Frustration high + churn risk → escalate faster, with the raw text
  attached, not summarized away.

## What this skill is NOT

- Not Laya. No checkpoint, no install, no download, no code run. If Laya is
  ever installed, that is a separate decision needing the user's go, and the
  decision step could then be swapped for a local call using these same
  question schemas — but this skill does not do that and does not assume it.
- Not a verdict machine. A decision record is a prepared route with its
  confidence shown, not proof. Low confidence is a normal, expected outcome
  — it means "ask the user," not "try harder."
- Not a way around the user's rules. Any workflow that needs a secret, a login, a
  private channel, or the user's final say skipped is out of scope for this skill,
  no matter how efficient it looks.
