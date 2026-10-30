# Cursor meta setup prompt - GAS platform repo

Randy Derego - 2026-10-29

## 1. Cursor settings (once, by hand)

| Setting | Value | Why |
|---|---|---|
| Open the repo | Remote-SSH to **GAS-AUTOCTLR**, folder = repo root | Validation and runs need the controller's venv, tofu, krb5; `scripts/run.sh` refuses other hosts |
| Privacy Mode | On (enforce at team level if available) | Repo holds internal topology and public IPs. Your existing practice for AI tools is a sanitized dataset; Privacy Mode is the minimum here |
| Agent terminal auto-run | Off, or allowlist only: `scripts/validate.sh`, `ansible-playbook --syntax-check`, `ansible-lint`, `yamllint`, `tofu fmt`, `tofu validate`, `python3 scripts/check_ipam.py`, `git status`, `git diff` | Stops the agent running `scripts/run.sh`, `tofu apply`, or a playbook without `--check` |
| Deny list | `scripts/run.sh`, `tofu apply`, `tofu destroy`, `ksm`, `ssh`, `kinit`, `rm -rf` | These touch production or secrets - you run them yourself |
| Rules | `.cursor/rules/*.mdc` (in repo) + `AGENTS.md` | Loaded automatically; check they appear under Settings > Rules |
| Ignore | `.cursorignore` (in repo) | Keeps env/keeper.env, seed ISOs, state, keys out of context |

Rule file format (frontmatter `description` / `globs` / `alwaysApply`) is per Cursor docs as of mid-2026 - if Settings > Rules does not list them, check the current format.

## 2. Meta setup prompt (paste once into a new Agent chat)

```text
You are setting up to work in the GAS platform automation repo on GAS-AUTOCTLR. Do not change any file in this step.

1. Read, in order: AGENTS.md, README.md, docs/REVIEW-FIXES.md, docs/semaphore.md, docs/ksm-endpoint-design.md, data/ipam.yml, data/hyperv_hosts.yml, data/vms.yml, data/media.yml, and every file in .cursor/rules/.
2. Confirm in 5 lines: the domain, the controller host and IP, the platform VLAN with its subnet math, the runner (Semaphore, not AWX), and how secrets reach a playbook.
2b. List every workflow in .cursor/rules/80-workflows.mdc (W1-W6, architecture stages, O1-O5) with its gates and artifact paths, and confirm docs/templates/ has a template for each artifact type. Report any workflow gate that cannot run today and which <FILL-IN> blocks it.
3. List every null / <FILL-IN> value across data/*.yml and docs/REVIEW-FIXES.md "Open items" as a table: file | key | what is needed.
4. Run scripts/validate.sh and paste the real summary lines. If a tool is missing, say which one and stop - do not substitute or simulate.
5. List anything in the repo that contradicts .cursor/rules (conflict table: file | says | rule says). Do not fix it.
6. End with Assumptions, Risks, Open items, and the one next step you recommend.

Rules for every later request in this repo:
- Never invent environment values; use <FILL-IN>. Report conflicts instead of resolving them.
- Plan first (3-6 lines, files touched, hidden risks), wait for "go" on anything prod-affecting.
- Never claim something passed unless a command in this chat produced it.
- Do not run scripts/run.sh, tofu apply/destroy, ksm, or any playbook without --check. I run those.
```

## 3. Task prompt template (each new request)

Per-workflow kickoff prompts: `docs/cursor/WORKFLOWS.md`. Generic form:

```text
Workflow: <W1-W6 | stage 1-7 | O1-O5>
Task: <what you want>
Scope: <files / playbook / host(s) by GAS- name>
Change level: read-only | check-mode only | change (I will run it)
Risk link: <R-0xx or "uncovered gap: ..."> 
Done when: <observable result>, scripts/validate.sh passes
Do not: touch data/*.yml facts, add tools, or widen inventory unless I say so.
```

## 4. What the rules prevent (from the first Cursor-generated bundle)

| Old failure | Rule that blocks it |
|---|---|
| corp.internal, 10.0.x DCs, invented Zabbix/Graylog IPs | 00 rule 1, 5; 50 |
| "10/10 PASSED", "APPROVED FOR PRODUCTION" with missing files | 00 rule 3; 60 |
| P@ssw0rd2026! defaults and fallbacks | 10 |
| hosts: all on DCs/firewalls, CredSSP, cert validation off | 00 rule 10; 20 |
| Auto-heal daemon, OpenSpec UI, Packer, Docker runner | 00 rules 6, 9 |
| Machine KSM_CONFIG on 500 desktops | 10 |
| Broken templatefile/Jinja mix, unattached cloud-init | 30 (schema check, validate) |
| Non-ASCII / PS7 syntax in 5.1 scripts | 40 |
| Jumping straight to code / reports with no discovery, CR or rollback | 80 (workflow gates) |
