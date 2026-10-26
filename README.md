# Donnie Second Brain Workflow (template)

A decision workflow with durable memory for a personal AI assistant.
Built for Meta Muse, adaptable to any assistant that keeps skills as files
and notes that survive chat restarts.

Made for Meta's Muse assistant. Unofficial community template — not affiliated with or endorsed by Meta.

The idea in one line: the assistant's second brain is the assistant itself.
No new model, no install, no separate system. A fixed decision procedure,
plus a habit: every decision record lands in durable notes, so the next
conversation starts from the last one's reasoning instead of from scratch.

## What's inside

- `decision-workflow/SKILL.md` — the whole workflow. Five steps
  (trigger → package → decide → return → act), typed questions
  (choice, yes-no, score), a 0.85 confidence gate (below it, escalate
  to the user instead of acting), hard constraints, package and record
  templates, allow-lists for what "act" may do, and three worked examples.

## Install (Muse)

1. Copy the `decision-workflow/` folder into your custom-skills folder
   (the folder where your assistant keeps user skills).
2. Read the skill once, end to end.
3. Adapt it before first use:
   - Replace the five example standing rules under HARD CONSTRAINTS
     with your own. The file ships the example set it was built with;
     your rules are what actually bind your assistant.
   - Adapt the note paths in step 4 (Return) if your setup keeps durable
     notes somewhere else. For Muse users, the daily log under `~/memory/`
     and `~/MEMORY.md` are the usual places.
   - Replace any assistant/user names in your own copy with yours.
4. From then on, the rule is simple: every decision step ends with a
   record, and every record goes into durable notes. Anything durable
   (a decision, a preference, a boundary) also goes into long-term memory.

## How it works

- **Trigger:** a real inbound item that needs a routing or classification
  judgment (email, message, ticket, opportunity, request). No item, no step.
- **Package:** the item trimmed to what matters, 2–5 typed questions with
  written criteria, minimum context only, and a confidence gate.
  Packages never contain secrets; a secret gets stripped, never repeated,
  and the user gets told to rotate it.
- **Decide:** every question answered in one pass against the criteria,
  each with a confidence (high / medium / low).
- **Return:** a short typed record — item, answers with confidence and
  the criterion matched, route (act / escalate / hold), what was not
  decided, and the exact next step. Filed in durable notes, never in a
  private channel.
- **Act:** only within the allowed set (draft, file, label, prepare
  options, summarize). Anything consequential — sending, posting, paying,
  deleting, changing settings, acting inside accounts — needs the user's
  explicit go every time, regardless of confidence.

## Origin

The pattern is adapted from Laya's workflow examples (typed decisions,
confidence gating, triage presets). No Laya code, checkpoint, model, or
install is included or required. The decision step is the assistant's own
structured judgment against written criteria.

Developed as the "Donnie second brain workflow": the name comes from the
assistant it was built with. When you adopt it, it becomes yours — your
assistant's name, your rules, your records. That is the point. Everyone
deploys their own.

## Use

Free for anyone to use, adapt, and share. There is no license file;
this note is the permission: use it, change it, share your version.
No private doors, no data, and no accounts are included or needed.
This is a template, not a product: no support, no warranty, and no
claim that it fits every setup. Adapt it, and keep what earns its keep.
