# AI Commerce — private HostOps control plane

**Current preferred design: Personal Mode v1 (2026-11-04).** This is a personal project with ONE Windows login. The previous separate broker/signer layers are not required for the first limited live deployment.

- **READ:** Human approves in the current ChatGPT conversation; ChatGPT creates a private `[HOSTOPS READ]` Issue. The private Windows runner runs allowlisted read operations without another approval.
- **WRITE:** ChatGPT creates a private `[HOSTOPS WRITE]` Issue. The Human posts exactly `APPROVE WRITE #N` in the GitHub mobile Issue. Only this owner comment triggers a bounded create-new file write. No separate Android SSHSIG or source-hash hand transcription.
- **Transport:** ChatGPT → private GitHub Issue → repository-scoped Windows self-host runner (`hostops-personal`) → private Issue receipt → ChatGPT.

Full runbook and bounded JSON contracts: [PERSONAL_HOSTOPS.md](PERSONAL_HOSTOPS.md).

## Actual activation status

The new source and workflow require review/merge. A private Windows runner must be separately registered and observed online; the existing public EF02 runner is NOT the private runner. There is **no proven live personal read/write** until the GitHub Issue and native Windows evidence show it. No automatic installation, privilege escalation, or arbitrary PowerShell.

The older private `.github/workflows/hostops-v3.yml` queue-only workflow is retired in this PR to avoid `skipped` runs; its earlier implementation remains available in Git history. HostOps W1.3 requests associated with private Issues #6/#7 are CONSUMED / NO_RETRY. Preserve their evidence.

The simplified one-account model knowingly lacks strong isolation from other processes under the same Windows SID, including the existing PUBLIC self-host runner. Keep sensitive provider credentials outside its access, and do not run untrusted public PR code during private HostOps operations.
