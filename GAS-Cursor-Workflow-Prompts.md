# Cursor kickoff prompts - one per workflow

Randy Derego - 2026-10-29. Paste the matching block into a new Agent chat. Workflow definitions: `.cursor/rules/80-workflows.mdc`; artifact templates: `docs/templates/`.

## W1 New infrastructure project
```text
Workflow W1. Project: <name> (slug <slug>). Goal: <one line>. Risk link: <R-0xx or gap>.
Run gate 1 only: produce docs/projects/<slug>/01-discovery.md with read-only commands for me to run and what each answers. Conflicts table first. Stop after gate 1.
```
Next prompts: "W1 gate 2 - here is the discovery output: <paste>", then gates 3-7 one at a time.

## W2 Quote / BOM
```text
Workflow W2. Need: <what>. Constraints: <ports, rack, budget>. Existing gear to stay compatible with: <from data/ or FILL-IN>.
Produce docs/quotes/<slug>.md from docs/templates/quote.md. No invented prices: Estimated - pending vendor confirmation. Show the compatibility check and total arithmetic.
```

## W3 Incident triage
```text
Workflow W3. Symptom: <what/since when/who>. Evidence files: <paths>.
Copy evidence to a working dir first. Produce docs/incidents/<date>-<slug>.md from the template: evidence table, most likely cause with confidence, ruled out, next read-only commands. No fixes yet.
```

## W4 PCI remediation
```text
Workflow W4. Scan: <file/date>. Group: <root cause or "consolidate all">.
Produce docs/security/<slug>/ with grouped findings and, before any change, a read-only PS 5.1 detection script under scripts/ that proves current use. Then stop for my review.
```

## W5 Firewall change
```text
Workflow W5. Need: <source> to <destination> for <purpose>, temporary: <yes until DATE / no>.
Give: ACP + rule position, objects (with subnet math), minimum ports, owner, expiry, rollback, a docs/firewall-flows.md row, and a CR draft from docs/templates/cr.md.
```

## W6 Brainstorm / new idea
```text
Workflow W6. Idea: <...>.
Gate 1 first: which 12+ risk does it reduce, or which liability/gap does it close? If none, write a rejected ADR in docs/decisions/ and stop.
```

## Architecture review stage
```text
Architecture review, stage <1-7>: <topic>. Conflicts first, every recommendation mapped to a risk ID, end with the next stage.
```

## O2 Build a platform VM
```text
Workflow O2 for <GAS-NBX01|GAS-GLG01>. Step 1: list every <FILL-IN> blocking this VM across data/*.yml and the exact value you need from me. Do not run anything.
```
Then: "O2 step 5 - review this plan output: <paste tofu plan>".

## O3 Zabbix agent rollout
```text
Workflow O3, host <GAS-HPV0xx>, phase transition. Confirm prerequisites (host exists in Zabbix, package filename, API URL, FMC rule if HPV004/HPV009) and give me the exact check-mode command. Do not run it.
```

## O4 KSM endpoint rollout
```text
Workflow O4, site <ADC|EP1|EP2|PH|VN>, stage <pilot|site>. List blocking FILL-INs, the Atera job settings, and the pass criteria. Do not change scripts unless a FILL-IN value is given.
```

## O1 Any repo change
```text
Workflow O1. Change: <...>. Plan first (files, risks, rollback). Wait for go. Then edit, run scripts/validate.sh, and give me the ADO PR description.
```
