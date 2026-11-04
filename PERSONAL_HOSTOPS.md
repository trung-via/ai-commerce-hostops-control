# AI Commerce HostOps — Personal Mode (simple)

**Design selection (2026-11-04): ONE Windows user, minimal setup, practical deployment first.**
This replaces the previously proposed multi-broker W1.4 phase-3 path for the initial personal deployment. The older signed HostOps v3 / W1 sources and Issue history remain for reference, but are not a required prerequisite for Personal Mode.

## Two user interactions, one private GitHub channel

- **READ:** The Human approves the request in the current ChatGPT conversation. ChatGPT (using its authenticated GitHub connector) creates one private Issue whose title begins with `[HOSTOPS READ] ` and body contains the exact closed JSON request. A private-repository Windows runner with label `hostops-personal` runs the allowlisted read automatically. No second approval, Android SSH signing, token entry or Windows terminal consent per request.
- **WRITE:** ChatGPT prepares a private Issue whose title begins with `[HOSTOPS WRITE] ` and describes the exact write. It does **nothing on Issue creation**. The Human opens that Issue in the GitHub mobile app and posts one exact comment: `APPROVE WRITE #N` (replace N with the Issue number). The private Windows runner then performs **one create-new** file operation and posts the observed SHA256 result. A ChatGPT-generated approval comment is not an acceptable substitute for this user action; the current implementation relies on GitHub account ownership, not on a separate cryptographic Human identity.

Pipeline: **ChatGPT → private GitHub Issue → private Windows self-hosted runner → private Issue result → ChatGPT**.

No separate token broker, new Windows user, Android/Termux signing, per-request canonicalization, mandatory independent receipt server or public runner dispatch. GitHub Actions provides a per-job `GITHUB_TOKEN`; there is no private PAT stored by the script.

## One-time setup after the PR is merged

1. Keep `trung-via/ai-commerce-hostops-control` **private**.
2. In the private repository: **Settings → Actions → Runners → New self-hosted runner → Windows**. Register a *new repository-scoped runner* under the **existing non-admin Windows account** (not a new account). Give it the custom label `hostops-personal`. Use GitHub's generated, expiring registration token only in your own Windows terminal; never share it in ChatGPT. The existing public EF02 runner is **not** automatically registered to the private repository and must not be mistaken for this private runner.
3. Keep the runner online using the GitHub-provided start/run procedure. The first activation is attended; install/start-at-login can be considered separately. No independent secret broker or extra Windows service user is required.
4. Ensure `C:\TOOL\ai-commerce` exists for read-only workspace files and `C:\TOOL\AI-Commerce-HostOps-Approved` exists for write-once scratch files. Paths are fixed in `scripts/personal_hostops.py`, NOT supplied by GitHub Issues. All other laptop folders remain unavailable until deliberately onboarded.
5. Create a fresh `[HOSTOPS READ] health` Issue through ChatGPT. Inspect `PERSONAL_HOSTOPS_RESULT_V1` comment for `status=OBSERVED` on the **real Windows runner**. Only then test a new `[HOSTOPS WRITE]` Issue using the mobile `APPROVE WRITE #N` comment, and check the file independently on the PC.

This PR adds code and tests, **not** an automatically enrolled private runner or already executed laptop operation. Any pending job without a private `hostops-personal` runner is simply QUEUED/UNPROVEN.

## Closed request formats

Read the fixed Windows inventory (only create after Human chat approval):

```json
{"schema":"ai-commerce.personal-hostops.v1","operation":"inventory","parameters":{}}
```

Read one UTF-8 file up to 8 KiB within either `workspace` or `scratch` root:

```json
{"schema":"ai-commerce.personal-hostops.v1","operation":"read_text","parameters":{"root_id":"workspace","relative_path":"README.md"}}
```

Create one new UTF-8 file up to 2 KiB inside fixed `scratch` root, only after the GitHub owner writes the exact approval comment:

```json
{"schema":"ai-commerce.personal-hostops.v1","operation":"create_text","parameters":{"root_id":"scratch","relative_path":"personal-proof.txt","content_utf8":"AI_COMMERCE_PERSONAL_HOSTOPS_PROOF_V1"}}
```

Each Issue is a new request. A preexisting file is never overwritten. For an ambiguous outcome, **inspect the private Issue and the disk before attempting anything else**; do not automatically retry or create a new request for the same target.

## Minimal retained safety boundaries

- Exactly one Windows user, no extra account, no arbitrary remote PowerShell/command execution.
- Private GitHub repo and original owner-only Issue/comment. The one-line WRITE comment is an **operational approval**, not a tamper-proof independent signature. The owner connector and owner UI share an account identity; they cannot be cryptographically distinguished.
- Fixed two folder roots, filename/path checks, no junctions/symlinks, size bounds, no delete, no overwrite. READ can return UTF-8 file contents into the **private** Issue; do not use it for credentials or secrets.
- Public EF02 self-host jobs under the same Windows SID are **not isolated from the private runner**. This residual risk is accepted for limited personal-use data, **not** approval to store provider passwords, PULSE live credentials, payment tokens or keys there. Avoid running untrusted public PR code on the machine while private HostOps is active.
- Keep old W1.3 private Issues #6/#7 and their requests CONSUMED / NO_RETRY. Do not reuse them or claim this workflow proves a previous host write.

## Proof levels

`SOURCE_TEST_PASS` ≠ `PRIVATE_RUNNER_REGISTERED` ≠ `REAL_WINDOWS_READ_OBSERVED` ≠ `APPROVED_NEW_FILE_WRITE_OBSERVED`. Report a real Actions run URL, Issue number, observed SHA256 and independently checked local file before claiming live HostOps.
