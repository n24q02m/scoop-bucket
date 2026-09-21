## 2024-05-18 - Missing Timeouts and Permissions in GitHub Actions
**Vulnerability:** The GitHub Actions workflow (`opencode.yml`) lacked top-level permissions mapping and individual job timeouts (`timeout-minutes`).
**Learning:** Without top-level permissions empty mapping `permissions: {}`, any new job added without explicit permissions inherits full read/write access depending on the repo settings. Without job timeouts, a hanging action or infinite loop could consume all available runner minutes, leading to a Denial of Service (DoS) for the CI/CD pipeline and unexpected costs.
**Prevention:** Always add a top-level `permissions: {}` block in all new workflows to enforce the principle of least privilege. Always define a `timeout-minutes` value (e.g., 15) for all jobs to prevent runaway processes.

## 2025-02-23 - Automated PR Review Bypass (TOCTOU)
**Vulnerability:** The automated PR review GitHub action (`.github/workflows/opencode.yml`) was configured to run only when a `pull_request_target` event had the `opened` type. An attacker could open a benign PR to receive an approval, and then push malicious commits later that would not be reviewed.
**Learning:** This is a Time-Of-Check to Time-Of-Use (TOCTOU) vulnerability specific to CI/CD pipelines. Security checks must run not only when a PR is opened but also whenever new code is synchronized (pushed) to the PR.
**Prevention:** Ensure GitHub Actions that perform security checks or auto-approvals on PRs are triggered on `[opened, synchronize, reopened]` to evaluate all code changes.
