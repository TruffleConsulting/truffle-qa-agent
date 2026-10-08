---
name: truffle-brd-jira-gap-analysis
description: Thoroughly compare a BRD against Jira ticket(s) to check whether they are aligned — everything stated in the BRD must be traceable to a ticket, and every ticket must be grounded in the BRD. Produces a gap table (Gap | BRD Says | Jira Says | Impact) and a recommended ticket testing order, then STOPS and waits for explicit user confirmation on which gaps matter before anything else happens (including test case generation). Use whenever the user provides both a BRD and Jira ticket(s) and asks to check alignment, compare BRD vs Jira, find gaps, verify everything in the BRD is covered, or wants to prep tickets before test cases are generated. Do not skip straight to test case generation when both a BRD and tickets are present — run this check first unless the user explicitly says gaps have already been reviewed.
---

# BRD vs Jira Gap Analysis

## 1. ROLE

You are doing a thorough, one-pass alignment check between a BRD and one or more Jira tickets, so the user never has to re-verify your gap-finding work. Treat this as the step that happens strictly BEFORE test case generation.

If ticket quality hasn't already been checked (see `jira-ticket-quality-check`), you can still run this analysis, but mention that a ticket-quality check hasn't been done if it looks like it would have mattered (e.g. missing AC makes coverage impossible to judge).

## 2. GOLDEN RULE — NEVER ASSUME ALIGNMENT

Do not assume something is covered just because it seems implied or "probably the same thing worded differently."

If you cannot point to the specific place in the ticket that covers a BRD requirement, or the specific place in the BRD that grounds a ticket item, it is a **gap** — not a judgment call you resolve silently.

## 3. INPUTS

- The BRD (full document)
- All Jira tickets relevant to that BRD

If tickets are missing for a BRD section, or a BRD is missing for a ticket, that absence is itself a finding — say so.

## 4. PROCESS

### Step 1 — Decompose the BRD
Break the BRD down into discrete, checkable items: functional requirements, business rules, fields/validations, workflows, roles/permissions, integrations, notifications, error handling, limits/boundaries, and any explicitly stated constraints.

### Step 2 — Decompose each Jira ticket
Break each ticket down into its discrete AC/requirements the same way.

### Step 3 — Cross-map both directions
- For every BRD item: find the ticket(s) that cover it. If none do, that's a gap.
- For every ticket item/AC: find the BRD requirement that grounds it. If none does, flag it — it may be legitimate scope the BRD just doesn't spell out, or it may be scope creep/misunderstanding. Don't decide which; flag it and let the user call it.
- Where a BRD item is partially covered (e.g. ticket handles the happy path but not the stated exception), flag the partial gap specifically — don't mark it fully covered.

### Step 4 — Determine recommended testing order
Using stated dependencies (data setup order, one ticket's output feeding another, explicit "depends on" links, or the BRD's described process flow), work out which ticket needs to be tested first, second, etc. Base this only on what's stated or clearly structural (e.g. a config ticket must land before the ticket that consumes that config) — do not guess at dependencies that aren't evidenced.

If two tickets have no dependency on each other, say so rather than forcing an arbitrary order.

## 5. OUTPUT FORMAT

### Gap Table

| Gap | BRD Says | Jira Says | Impact |
|---|---|---|---|
| | | | |

- **Gap**: short name of the misalignment (e.g. "Max discount % not in ticket", "Ticket ABC-104 adds bulk-edit not mentioned in BRD").
- **BRD Says**: exact requirement/wording area from the BRD (paraphrase briefly, cite section if the BRD has numbering).
- **Jira Says**: what the ticket(s) actually state, or "Not mentioned" if absent.
- **Impact**: what happens if this gap isn't resolved before testing (e.g. "Cannot write boundary test cases", "Risk of testing undocumented functionality with no defined expected result").

One table covering all tickets is fine; reference the relevant Ticket ID in the Gap column when it's ticket-specific.

### Recommended Testing Order

List tickets in the order they should be tested, with a one-line rationale each:

1. TICKET-ID — reason
2. TICKET-ID — reason

If order genuinely doesn't matter for some tickets, group them and say so explicitly rather than inventing a sequence.

## 6. MANDATORY STOP — WAIT FOR CONFIRMATION

After presenting the gap table and testing order, **stop here**.

Do not proceed to test case generation. Do not silently decide which gaps are "probably fine to ignore."

Ask the user explicitly which gaps should be considered in scope, which are non-issues, and confirm the testing order. Something like:

> "Which of these gaps should we account for, and which can we disregard? Once confirmed, I'll move to test case generation in the order above."

Only continue once the user responds.

## 7. HAND-OFF TO TEST CASE GENERATION

Once the user confirms which gaps count and the testing order:

- Pass the confirmed gap list and testing order forward as context.
- Use the `salesforce-qa-test-case-generator` skill (or whichever test-case-generation skill is available) to actually write the test cases.
- Enforce strict separation: **each ticket's test cases must stay under that ticket's own section — never blend or borrow scenarios from another ticket's context**, even when tickets are related or dependent. Dependency means "test this ticket after that one," not "merge their scenarios together."
- Generate ticket sections in the confirmed testing order, not in whatever order the tickets happened to be provided.
- For any gap the user confirmed as in-scope but still undefined (no stated expected behavior), do not invent the expected result — follow the base test-case-generation skill's rule and mark it as a Requirement Clarification instead of a test case.
