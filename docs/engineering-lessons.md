# Engineering lessons

These entries were recovered from the working ledger this repository kept at its root
until 2026-11-11: the notes of a discontinued automated assistant that proposed changes
under a security remit (`sentinel.md`). The entries are kept verbatim, each in the shape
it was written. A few entries quote the paths the ledger occupied before this move;
those paths no longer exist.
# Security (`sentinel.md`)


### 2024-10-27 - [Workflow Permissions Least Privilege]
**Vulnerability:** [Top-level file workflow permissions in `secret-scanning.yml` were applying `contents: read` to all jobs, instead of the specific jobs that needed them.]
**Learning:** [This existed because it is the default behavior in many boilerplate workflows and examples to specify permissions globally. While functionally correct, it violates the principle of least privilege, as any new jobs added to the workflow would implicitly inherit these permissions.]
**Prevention:** [Always enforce least privilege in GitHub Actions workflows by setting top-level `permissions: {}` and explicitly defining permissions on a per-job basis.]
