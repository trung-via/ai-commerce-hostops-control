# AI Commerce — private HostOps control plane

This repository is the **private request and receipt boundary** for the HostOps v3 architecture documented in [public Issue #248](https://github.com/trung-via/ai-commerce/issues/248).

- **Public source/CI:** `trung-via/ai-commerce` (must stay public).
- **Private control/receipts:** `trung-via/ai-commerce-hostops-control` (must stay private).
- **Laptop:** isolated non-admin broker and transport identities, signed immutable releases, strict local ACL, fail-closed KILL, no inbound internet listener.
- **No self-hosted Actions runner and no request execution in this repository.** The queue workflow only checks shape and records a SHA-256 digest. `QUEUED_UNVERIFIED` is never a signed approval or a live-host receipt.

**Current installation status: NOT INSTALLED / NOT ENABLED.** No GitHub workflow can enable a Windows broker, acquire local signing keys, change Windows accounts/ACLs, or grant itself laptop read/write privileges. An independently installed, authenticated and explicitly enabled local transport is required for any eventual operation.

Never put private keys, GitHub tokens, Windows credentials, arbitrary host file contents or installer logs into Issues/commits/Actions output. Even private GitHub history is not a secret vault.

## Intended activation sequence

1. Independently review and merge the **public** HostOps v3 protocol change binding each signed request to both repository identities and an immutable public source SHA.
2. Validate an exact private queue workflow digest against the signed release manifest; verify completed GitHub-run provenance; keep hosted runner only.
3. Human signs/enrolls the immutable release and leases on the laptop, verifies dedicated Windows SIDs, DACL, KILL/revocations, and separate transport token scope.
4. Demonstrate one bounded, signed read operation with a native Windows IPC and disk receipt; approve write operations separately. Do not assert full access from source CI.

This bootstrap repository has **no live host capability**.
