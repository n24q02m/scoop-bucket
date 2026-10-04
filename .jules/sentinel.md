## 2024-05-18 - Missing Timeouts and Permissions in GitHub Actions
**Vulnerability:** The GitHub Actions workflow (`opencode.yml`) lacked top-level permissions mapping and individual job timeouts (`timeout-minutes`).
**Learning:** Without top-level permissions empty mapping `permissions: {}`, any new job added without explicit permissions inherits full read/write access depending on the repo settings. Without job timeouts, a hanging action or infinite loop could consume all available runner minutes, leading to a Denial of Service (DoS) for the CI/CD pipeline and unexpected costs.
**Prevention:** Always add a top-level `permissions: {}` block in all new workflows to enforce the principle of least privilege. Always define a `timeout-minutes` value (e.g., 15) for all jobs to prevent runaway processes.

## 2025-02-23 - Automated PR Review Bypass (TOCTOU)
**Vulnerability:** The automated PR review GitHub action (`.github/workflows/opencode.yml`) was configured to run only when a `pull_request_target` event had the `opened` type. An attacker could open a benign PR to receive an approval, and then push malicious commits later that would not be reviewed.
**Learning:** This is a Time-Of-Check to Time-Of-Use (TOCTOU) vulnerability specific to CI/CD pipelines. Security checks must run not only when a PR is opened but also whenever new code is synchronized (pushed) to the PR.
**Prevention:** Ensure GitHub Actions that perform security checks or auto-approvals on PRs are triggered on `[opened, synchronize, reopened]` to evaluate all code changes.

## 2024-09-22 - AI Prompt Injection leading to Authorization Bypass
**Vulnerability:** An AI code review bot was configured with a prompt allowing it to output `APPROVE` on PRs, while its action had `pull-requests: write` permissions. A malicious contributor could inject prompts into PR code or descriptions to trick the AI into approving malicious changes, bypassing branch protection rules.
**Learning:** AI systems reviewing untrusted input must operate under the principle of least privilege. If the AI can be tricked by its input, giving it authorization power (like PR approvals) creates a critical vulnerability.
**Prevention:** Never instruct AI code reviewers to issue `APPROVE` verdicts if they run on untrusted PRs. Restrict their output to `COMMENT` only, ensuring human reviewers always have the final say on merging.

## 2026-09-28 - AI Agent Guardrails
**Vulnerability:** The AI agent lacked explicit instructions to reject unauthorized manual edits to `bucket/` manifests and was potentially vulnerable to prompt injection attacks via issues or PRs.
**Learning:** AI agents with write access must have strict security guardrails defined in their instructions (e.g., `.github/review-learnings.md`) to prevent abuse and accidental corruption of auto-generated files.
**Prevention:** Always define explicit security boundaries for automated agents, specifically restricting modifications to auto-generated directories and instructing the agent to ignore prompt injection attempts.

## 2026-09-28 - Automerging GitHub Action Digests Bypasses Pinning Security
**Vulnerability:** The Renovate configuration (`renovate.json`) was set to automatically merge digest (`pinDigest`) updates for GitHub Actions.
**Learning:** Pinning GitHub Actions to a specific SHA digest is done to prevent supply chain attacks if a mutable tag (e.g., `@v2`) is hijacked. Automerging digest updates defeats this purpose, as a maliciously altered tag will cause Renovate to automatically update the digest and merge the compromised action without human review.
**Prevention:** Never auto-merge digest updates for GitHub Actions. Always require human review to verify that the tag update is legitimate and not a supply chain attack.

## 2026-09-28 - Automerging GitHub Action Digests Bypasses Pinning Security
**Vulnerability:** The Renovate configuration (`renovate.json`) was set to automatically merge digest (`pinDigest`) updates for GitHub Actions.
**Learning:** Pinning GitHub Actions to a specific SHA digest is done to prevent supply chain attacks if a mutable tag (e.g., `@v2`) is hijacked. Automerging digest updates defeats this purpose, as a maliciously altered tag will cause Renovate to automatically update the digest and merge the compromised action without human review.
**Prevention:** Never auto-merge digest updates for GitHub Actions. Always require human review to verify that the tag update is legitimate and not a supply chain attack.

## 2025-09-25 - AI Prompt Injection leading to Repository Write Access
**Vulnerability:** The `mention` job in the GitHub Actions workflow (`opencode.yml`) granted the AI agent `contents: write` permission to allow it to update `.github/review-learnings.md`. However, because the agent reads untrusted PR diffs and issue bodies when summoned by a maintainer, an attacker could use prompt injection to trick the AI into committing malicious code (like a rogue workflow) directly to the repository.
**Learning:** If an AI agent has the ability to process untrusted input (like pull request code or issue descriptions), granting it write access to the repository creates a severe prompt injection vulnerability leading to Privilege Escalation and potential Remote Code Execution.
**Prevention:** Enforce the principle of least privilege for AI-driven GitHub Actions. Never grant `contents: write` permissions to jobs that process untrusted input. If an AI must propose changes to the repo, it should do so by creating a pull request from a fork, rather than pushing directly.

## 2024-09-24 - AI Prompt Injection in Manual Triggers
**Vulnerability:** The manual trigger (`mention`) job in `.github/workflows/opencode.yml` lacked strict prompt controls, leaving it vulnerable to prompt injection if a maintainer summoned the bot on an attacker-controlled PR.
**Learning:** Even manual AI triggers require the same strict prompt restrictions as automated ones, because the underlying PR context remains untrusted.
**Prevention:** Always include a restrictive `prompt` overriding the AI's behavior to forbid dangerous actions (like `APPROVE`) in any job that interacts with untrusted code, regardless of how it was triggered.
