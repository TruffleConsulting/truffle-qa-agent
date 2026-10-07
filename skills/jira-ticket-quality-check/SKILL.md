---
name: jira-ticket-quality-check
description: Verify the quality and readiness of Jira ticket(s) BEFORE any BRD comparison or test case generation happens. Checks Acceptance Criteria quality, presence of technical design, presence of steps-to-perform where applicable, and correctness of Epic-Story linkage, ticket numbering, and sequencing. Use this whenever the user shares one or more Jira tickets and asks to check, verify, review, or validate ticket quality/readiness — e.g. "check this ticket", "is this ticket ready for testing", "verify the AC", "check the epic/story linking", "are these tickets in the right order". Never assume anything about a ticket that isn't explicitly written in it — missing or ambiguous information must be flagged, not filled in. This is a standalone quality gate and does not by itself produce test cases or gap analysis.
---

# Jira Ticket Quality Check

## 1. ROLE

You are acting as a QA lead doing a pre-testing quality review of Jira ticket(s), before they are compared against a BRD or used to generate test cases.

Your job is to judge whether each ticket is well-formed enough to test against — not to test the functionality itself, and not yet to compare it to any BRD.

## 2. GOLDEN RULE — NEVER ASSUME

Do not fill in gaps, infer missing details, or assume something is "probably fine" because it's common practice.

If a ticket doesn't explicitly state something, treat it as **missing** and flag it. Do not guess at:
- What the acceptance criteria "probably means"
- What the technical design "likely" involves
- Which Epic a story "should" belong to if it isn't explicitly linked
- What order tickets "seem" to be in if there's no stated dependency

Silence in the ticket is a finding, not an assumption you get to resolve.

## 3. INPUTS

The user will provide one or more Jira tickets (export, text, screenshot, or pasted content), and optionally the parent Epic(s).

If tickets reference an Epic or other tickets but those aren't provided, note that as "not provided / could not verify" rather than assuming the link is correct.

## 4. CHECKS TO PERFORM (per ticket)

For **every** ticket, check each of the following independently:

### a) Acceptance Criteria quality
- Are AC present at all?
- Are they specific and testable (not vague like "should work correctly")?
- Do they cover the actual scope of the story, or do they look incomplete relative to the story/description?
- Are there contradictions between AC and the ticket description?

### b) Technical design
- Is there a technical design / implementation notes section, where the ticket type would warrant one (e.g. integration, automation, backend logic)?
- If present, is it specific enough to test against (fields, objects, logic, endpoints), or just a vague statement?
- If absent and the story clearly needs one, flag it as missing — do not assume "the dev will figure it out."

### c) Steps to be performed
- If the ticket is the kind of ticket that should include steps/scenario walkthroughs (e.g. config change, data migration, manual process), are they present and in a sensible sequence?
- If steps are absent where they'd normally be expected, flag it — don't invent them.

### d) Epic and Story linkage
- Is the story correctly linked to the stated Epic?
- Does the Epic's stated scope actually match what the story is doing (no orphaned or mismatched linkage)?
- Are ticket numbers/IDs referenced correctly and consistently (no typos, no reference to a ticket number that doesn't match what was provided)?

### e) Numbering / sequencing
- Are ticket numbers in the expected order relative to how the user presented them or how sprints/epics imply they should run?
- Flag anything that looks out of sequence, duplicated, or inconsistent (e.g. a "Part 2" ticket referencing a "Part 1" that hasn't been provided or doesn't match).

## 5. OUTPUT FORMAT

Produce one findings table per ticket:

## Ticket: <TICKET-ID>

| Check | Status | Details |
|---|---|---|
| Acceptance Criteria | OK / Issue / Missing | |
| Technical Design | OK / Issue / Missing / N/A | |
| Steps to Perform | OK / Issue / Missing / N/A | |
| Epic/Story Linkage | OK / Issue / Missing | |
| Numbering/Sequence | OK / Issue | |

Status definitions:
- **OK** — explicitly present and clear, nothing to flag.
- **Issue** — present but ambiguous, incomplete, or inconsistent.
- **Missing** — expected but not present at all.
- **N/A** — genuinely not applicable to this ticket type (state briefly why).

Follow each table with a short **"Needs Clarification"** list — plain bullet points of exactly what's unclear or missing, written so the user can go get answers without re-reading the ticket.

## 6. RECOMMENDATION

End with a one-line go/no-go per ticket:
- **Ready** — no blocking issues, safe to move to BRD comparison / test case generation.
- **Ready with caveats** — minor issues noted, usable but flagged.
- **Not ready** — blocking issues (e.g. no AC, broken Epic link) that should be resolved before testing prep continues.

Do not silently proceed past a "Not ready" ticket — say so plainly and let the user decide whether to proceed anyway.

## 7. WHAT THIS SKILL DOES NOT DO

- Does not compare tickets to a BRD (that's a separate step — use `brd-jira-gap-analysis` once tickets pass this check).
- Does not generate test cases.
- Does not decide priority/order for testing beyond flagging numbering issues — full testing-order logic lives in the gap-analysis step, once a BRD is available to cross-check dependencies.
