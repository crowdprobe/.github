# Crowdprobe contribution and agent lifecycle policy

This repository defines default organization-wide contribution guidance for Crowdprobe repositories.

## Agent lifecycle requirements

For agent-driven implementation work, the following lifecycle is authoritative unless a repository explicitly defines a stricter rule.

1. **The first lifecycle action for any implementation task is to move the linked `Crowdprobe Workboard` project item to `In Progress`. This is a mandatory precondition for implementation.** Before that status transition succeeds, the agent must not edit files, create an implementation branch, run implementation commands, or otherwise begin implementation work. It may only inspect the issue/project metadata needed to perform the status transition. If the item is already `In Progress`, treat this precondition as satisfied.
2. After `In Progress` is confirmed and still before implementation begins, post a short issue comment recording that the agent assumed ownership, including the acting agent identity and useful branch/run context. Do not assign the issue to the bot. If the linked project item or its status cannot be accessed or updated, **stop and report that explicitly as a permissions/configuration problem; do not start implementation and do not substitute the ownership comment for the required status transition.**
3. When implementation is ready and a pull request is opened, update the issue description to include the PR link/number explicitly. Do not rely only on comments, branch naming, or implicit GitHub linkage.
4. **Opening or merging a PR does not complete the issue.** Do not move the issue to `Done` or close it merely because a PR was opened or merged.
5. After each implementation/fix PR is merged and deployed/applied where applicable, verify the actual result against the issue's acceptance criteria in the real target environment where reasonably possible. Never claim verification that was not actually performed.
6. If verification succeeds, document the verification result, move the corresponding project item to `Done` where applicable, and close the issue as completed.
7. If verification is blocked or fails, document the blocker/failure, keep the issue open, create a new PR with the required fix, merge/apply it through the normal review process, and re-run verification. Count each corrective PR as one retry. **Maximum: 3 verification-fix retries.**
8. If verification is still blocked or failing after retry 3, document the final status and remaining blocker/failure, stop automatic retries, leave the issue open, and reassign it to `@triesz` for human follow-up.

## Crowdprobe Workboard access

Agents authenticated with the Crowdprobe GitHub App must use the GitHub Projects v2 REST API for Workboard discovery and status updates. The App has organization-level Projects access; do not use `gh project list`, GraphQL `projectItems`, or another API path that requires a user-scoped project token as the authoritative existence check.

For an organization-owned project, discover the Workboard through `GET /orgs/crowdprobe/projectsV2`, then use the project fields/items endpoints to locate the issue by repository and issue number or content URL and inspect/update its `Status` field. Use the current versioned GitHub REST API headers supported by GitHub.

Workboard handling must be idempotent and fail closed before implementation:

- If the issue item already has `In Progress`, treat the status precondition as successful and do not rewrite it unnecessarily.
- If the item exists with another status, update it to `In Progress` and confirm the update before implementation begins.
- An empty result from `gh project list` or missing GraphQL `projectItems` is **not evidence that the issue is absent from the Workboard**.
- Only state that the Workboard item is missing after successfully reading the Crowdprobe Workboard through the supported Projects v2 API and failing to find the issue there.
- If the Projects v2 API itself is inaccessible or rejects the App token, stop before implementation and report that as a Projects permission/API-access problem. Do not claim that the issue is unlinked or absent unless that was actually verified.
- If the issue is not on the Workboard or no usable `In Progress` status exists, stop before implementation and report the configuration gap rather than bypassing the precondition.

## Verification expectations

Verification should prove the requested outcome, not merely that code was written or CI passed. Use the issue's own acceptance criteria as the source of truth. For deployed/runtime changes, verify the deployed/runtime result where reasonably possible. For generated artifacts, verify the actual artifact and its stated constraints.

Do not claim verification that was not performed. If a required verification cannot be performed, treat that as blocked verification and follow the retry/escalation flow above.
