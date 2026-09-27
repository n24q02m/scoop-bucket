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

## 2025-02-24 - AI Agent Supply Chain Risk

**Vulnerability:** The codebase relies on an AI agent (`anomalyco/opencode/github`) to review Pull Requests. The agent was missing rules against accepting manual edits to auto-generated files in the `bucket/` directory, leaving the project open to supply-chain attacks where an attacker could submit a PR that alters a Scoop manifest to point to malware, and the AI agent might not flag it. It was also susceptible to prompt injections via PR titles or descriptions.

**Learning:** When using AI agents for automated code reviews, explicit guardrails must be established to prevent them from approving changes to sensitive or auto-generated directories, as well as to defend against prompt injection attempts meant to bypass security checks.

**Prevention:** Created `.github/review-learnings.md` containing strict instructions forbidding the AI from accepting manual edits to files within `bucket/` and directing it to ignore prompt injection attempts. Always configure AI review agents with explicit rules regarding project structure and prompt injection defenses.
