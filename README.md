# QA BRD/Jira Workflow Plugin

Bundles three skills that run in sequence to take Jira tickets and a BRD from raw input through to signed-off, ticket-ordered manual test cases.

## Skills included

| Skill | Runs when | Does |
|---|---|---|
| `jira-ticket-quality-check` | First, on tickets alone | Checks AC quality, technical design, steps-to-perform, Epic/Story linkage, and numbering — flags anything missing instead of assuming it |
| `brd-jira-gap-analysis` | Once a BRD is available | Cross-checks BRD vs Jira in both directions, outputs a Gap table + recommended testing order, and **stops for sign-off** before anything else happens |
| `salesforce-qa-test-case-generator` | Last, after gaps/order are confirmed | Generates manual test cases ticket by ticket, following the confirmed gaps and testing order |

## Installing

If your Claude Code / Claude environment supports plugin marketplaces:

```
/plugin marketplace add <path-or-repo-to-this-plugin>
/plugin install qa-brd-jira-workflow
```

If you're using this inside claude.ai's skills feature instead, upload each `SKILL.md` under `skills/` individually as its own skill — claude.ai does not currently install multi-skill plugin bundles as a single unit.

## Usage

Hand over your Jira ticket(s) and/or BRD and describe what you need — e.g. "check this ticket," "compare this BRD against these tickets," or "run the full QA prep on this project." Each skill's description is written so Claude picks the right one automatically, and they hand off to each other in order when you want the full pipeline run end to end.

The gap-analysis stage will always pause and ask you to confirm which gaps matter and what order tickets should be tested in — it will not generate test cases until you respond.
