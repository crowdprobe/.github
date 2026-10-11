# Crowdprobe contribution and agent lifecycle policy

This repository defines default organization-wide contribution guidance for Crowdprobe repositories.

## Agent lifecycle requirements

For agent-driven implementation work, the following lifecycle is authoritative unless a repository explicitly defines a stricter rule.

1. When an agent starts work, move the issue/project status to `In Progress`.
2. When implementation is ready and a pull request is opened, update the issue description to include the PR link/number explicitly. Do not rely only on comments, branch naming, or implicit GitHub linkage.
3. **Opening or merging a PR does not complete the issue.** Do not move the issue to `Done` or close it merely because a PR was opened or merged.
4. After each implementation/fix PR is merged and deployed/applied where applicable, verify the actual result against the issue's acceptance criteria in the real target environment where reasonably possible. Never claim verification that was not actually performed.
5. If verification succeeds, document the verification result, move the corresponding project item to `Done` where applicable, and close the issue as completed.
6. If verification is blocked or fails, document the blocker/failure, keep the issue open, create a new PR with the required fix, merge/apply it through the normal review process, and re-run verification. Count each corrective PR as one retry. **Maximum: 3 verification-fix retries.**
7. If verification is still blocked or failing after retry 3, document the final status and remaining blocker/failure, stop automatic retries, leave the issue open, and reassign it to `@triesz` for human follow-up.

## Verification expectations

Verification should prove the requested outcome, not merely that code was written or CI passed. Use the issue's own acceptance criteria as the source of truth. For deployed/runtime changes, verify the deployed/runtime result where reasonably possible. For generated artifacts, verify the actual artifact and its stated constraints.

Do not claim verification that was not performed. If a required verification cannot be performed, treat that as blocked verification and follow the retry/escalation flow above.
