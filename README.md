# Cloud Agent Workflows

Prompt templates for [Devin automations](https://docs.devin.ai/product-guides/automations).

Each template is a complete prompt that tells Devin to create an automation, including the recommended trigger. No code is required.

To use a template, paste its full prompt into a new Devin session. Devin then creates the automation for you.

Devin starts the automation when a schedule, Slack message, or GitHub event matches the trigger. Some templates also require an MCP connection.

## How to use the templates

1. Choose a template below.
2. Copy the full prompt, including the "Create a Devin automation" line and the trigger.
3. If the example names, channels, or schedule do not match your environment, change them in the prompt.
4. Paste the prompt into a new Devin session. Devin creates the automation and asks about any missing details, such as the exact Slack channel or repository.
5. If the template uses an MCP, connect that MCP before the first run.
6. Give each MCP connection the least access that the template needs. If a template only reads data, connect read-only credentials.

The prompts contain safety rules, for example "Do not apply a migration". These rules guide Devin, but they are not a security boundary. The credentials that you connect set the real limit on what Devin can do.

## Slack triage

### 1. Bug triage

```text
Create a Devin automation named "Bug triage".

Trigger: a new message in the bug channel.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

A user posted a bug report in the channel.

IMPORTANT: If Devin posted the message, stop. Do not post a reply. A reply can start a new run and cause a loop.

1. Read the report and the full thread.
2. If the report includes reproduction steps, reproduce the bug.
3. Record the expected behavior and the actual behavior.
4. Search the codebase for the code that controls this behavior.
5. Trace the behavior to its root cause. Collect evidence from the code, tests, or logs.
6. If the evidence supports one cause, post a concise finding in the thread.
7. Include the cause, the affected code, the impact, and the recommended next step.
8. If the evidence is inconclusive, do not claim a root cause. If more information can unblock the investigation, ask one focused question.
```

### 2. Support replies

```text
Create a Devin automation named "Support replies".

Trigger: a new message in the support channel.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

A user posted a support request in the channel.

IMPORTANT: If Devin posted the message, stop. Do not post a reply. A reply can start a new run and cause a loop.

1. Read the request and the full thread.
2. Identify the specific technical issue or question.
3. Search the codebase and the docs for direct evidence.
4. If essential information is missing, ask one focused question and stop.
5. If the available information is sufficient, post a concise reply in the thread.
6. Include a direct answer, links to the source, and clear next steps.
7. If the issue is a bug, state its severity and whether it needs a fix PR.
8. If the request needs a human decision, identify the decision and the appropriate owner.
```

## Monitoring and incidents

### 3. Alert investigation

```text
Create a Devin automation named "Alert investigation".

Trigger: a new alert message in the alerts channel.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

An alert was posted to Slack. The full event details are below.

IMPORTANT: If Devin posted the message, stop. Do not post a reply. A reply can start a new run and cause a loop.

1. Read the alert. Identify the affected service, metric, threshold, and time window.
2. Use the Datadog MCP to pull metrics, logs, and traces from before the alert through recovery.
3. Establish the normal baseline for the affected service.
4. Compare the alert with deployments from the preceding 24 hours in the git log.
5. Determine whether the evidence supports a code change, traffic spike, infrastructure problem, or external dependency failure.
6. If the cause is a code change, implement the smallest safe fix.
7. If the cause is a code change, add a regression test.
8. If you changed the code, run the related tests.
9. If the tests pass, open a fix PR.
10. If the evidence is inconclusive, state that clearly and list the next diagnostic step.
11. Post the findings in the Slack thread. Include the cause, evidence, impact, current status, and next steps.
```

### 4. Sentry error fixes

```text
Create a Devin automation named "Sentry error fixes".

Trigger: a daily schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Use the Sentry MCP to find unresolved issues from the past 24 hours.

1. Exclude issues tagged 'wontfix' or 'expected-behavior'.
2. Sort the remaining issues by event frequency.
3. Select the top 5 issues.
4. If no eligible issues remain, post a short summary and stop.

For each selected issue:
1. Pull the stack trace, breadcrumbs, release, and affected environment.
2. Search for an existing issue or open PR that addresses the same error.
3. If a fix already exists, record its link and continue to the next issue.
4. Find the related source code. If possible, reproduce the error.
5. Add a regression test that fails before the fix.
6. Implement the smallest safe fix.
7. Run the related tests.
8. Open a fix PR. Link the Sentry issue in the PR.

Post a summary with each Sentry issue, its frequency, its status, and its fix PR.
```

### 5. Daily error report

```text
Create a Devin automation named "Daily error report".

Trigger: a daily schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Compile a daily error report with the Datadog MCP.

1. Query the error rates and error logs for the key services from the past 24 hours.
2. Query the same data for the preceding 24 hours.
3. Identify new error types and material increases in error volume or rate.
4. For each increase, report the absolute change and the percentage change.
5. Compare notable errors with recent deployments in the git log.
6. If the logs, traces, or code changes support a likely cause, include that cause in the report.
7. Suggest an owner from CODEOWNERS or the recent change history.
8. Post the report to the #engineering Slack channel.

Use these sections: top errors, new errors, increased errors, likely causes, suggested owners, and next steps.
```

### 6. Capacity review

```text
Create a Devin automation named "Capacity review".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Run a capacity review with the Datadog MCP.

1. Pull CPU, memory, request-rate, and queue-depth trends for the key services from the past 30 days.
2. Identify sustained growth and services near a resource limit or an autoscaling ceiling.
3. State the threshold that you use to classify a service as near a limit.
4. Project when each affected service will reach capacity at its current growth rate.
5. State the assumptions and confidence level for each projection.
6. Recommend a specific action: increase instance counts, change resource limits, or add sharding.
7. Rank the recommendations by urgency and expected impact.
8. Post the capacity summary to the #engineering Slack channel.
```

## CI and GitHub events

### 7. CI failure fix

```text
Create a Devin automation named "CI failure fix".

Trigger: a failed check run on GitHub.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

A CI check failed. The full event details are below.

IMPORTANT: If devin-ai-integration[bot] authored the failing commit, reply 'Skipping: commit authored by Devin' and stop.

1. Open the failed check run. Read the complete build and test logs.
2. Find the first actionable failure. Ignore errors that only result from that failure.
3. If possible, reproduce the failure locally with the same command.
4. Before you change the code, identify the root cause.
5. Fix the root cause on the same branch.
6. Do not change a test only to hide a product defect.
7. Run the failed check locally. Then run the related tests.
8. If all checks pass, push the fix.
9. If you cannot make a safe fix, post the cause and the remaining blocker on the PR.
```

### 8. Issue-to-PR command

A team member comments /devin on an issue that describes work. Devin reads the issue, makes the change, and opens a PR that resolves it.

Note: Use this template on private repositories only. On a public repository, any GitHub user can comment /devin and start the automation.

```text
Create a Devin automation named "Issue-to-PR command".

Trigger: a /devin comment on a GitHub issue.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

A user commented /devin on a GitHub issue. The full event details are below.

1. Read the /devin comment, the issue title, the issue body, and all follow-up comments.
2. Identify the requested outcome and the acceptance criteria.
3. If the request is ambiguous, ask one focused question on the issue and stop.
4. Search the codebase for the related code and tests.
5. If the project supports tests, add one that demonstrates the problem.
6. Implement the smallest change that meets the acceptance criteria.
7. Run the targeted tests and the relevant test suite.
8. Open a PR that references the issue and explains how the change meets each criterion.
```

## Repository maintenance

### 9. Dependency updates

```text
Create a Devin automation named "Dependency updates".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Scan for outdated dependencies.

1. Find available updates in the package manager files.
2. If an open PR from an earlier run already contains an update, skip that update.
3. Read the release notes and migration guides for each update.
4. Identify breaking changes, security fixes, and required code changes.
5. Unless a release fixes a critical vulnerability, do not select it until it is at least 7 days old.
6. Group compatible patch and minor updates in one PR.
7. Put each major update in a separate PR.
8. Run the tests, type checks, and build for each PR.
9. If these checks pass, open a PR.
10. Include the update risk, notable changes, and required follow-up work in each PR description.
```

### 10. Weekly changelog

```text
Create a Devin automation named "Weekly changelog".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Generate a weekly changelog.

1. List all PRs merged in the past 7 days.
2. Exclude changes that already appear in CHANGELOG.md.
3. Read each PR description and diff. Do not rely on the title alone.
4. Categorize each PR as a feature, bug fix, improvement, or breaking change.
5. Write one clear line for each change. Include the PR number and link.
6. Put breaking changes first and state the required user action.
7. If an open PR from an earlier run updates CHANGELOG.md, add the new section to that PR.
8. If no open changelog PR exists, open a PR that adds the dated section to CHANGELOG.md.
```

### 11. Stale PR reminders

```text
Create a Devin automation named "Stale PR reminders".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Scan for stale pull requests.

1. List open PRs with no human activity for more than 7 days.
2. If an automation posted a stale reminder in the past 7 days, skip that PR.
3. Check each PR for failed checks, requested changes, and merge conflicts.
4. Post one concise comment that names the current blocker.
5. If a PR has no blocker, ask its author to update or close the PR.
6. If a PR has merge conflicts, mention them in the same comment.
```

## Security

### 12. Vulnerability scan

```text
Create a Devin automation named "Vulnerability scan".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Scan for dependency vulnerabilities.

1. Run the audit commands for each package manager, for example `npm audit` or `pip-audit`.
2. Compare the results with the GitHub Advisory Database.
3. Remove duplicate findings for the same dependency and CVE.
4. If an open PR already fixes the vulnerability, record its link and skip it.
5. If a critical or high severity vulnerability has a patch, update the dependency.
6. If you updated a dependency, run the related tests.
7. If the tests pass, open one fix PR for each dependency update.
8. List every related CVE and severity in the PR.
9. If a critical or high severity vulnerability has no patch, open an issue with the available mitigation.
10. Suggest an owner for each unresolved issue.
11. Post a summary with the vulnerability count by severity, the fix PRs, and the unresolved issues.
```

### 13. Access review

```text
Create a Devin automation named "Access review".

Trigger: a monthly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Review access to the GitHub repositories.

1. List the collaborators, teams, and permission levels for each repository.
2. List the deploy keys, webhooks, and installed GitHub Apps.
3. Flag collaborators with write access and no commits or reviews in the past 90 days.
4. Flag outside collaborators with write or admin access.
5. Flag deploy keys and webhooks with no clear owner or purpose.
6. Review the branch protection rules for each default branch.
7. Flag branches that allow force pushes or merges without a review.
8. Do not change permissions, keys, or protection rules.
9. Post a report to the #security Slack channel with each flag, its evidence, and a recommended action.
```

### 14. OWASP scan

```text
Create a Devin automation named "OWASP scan".

Trigger: a monthly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Run an OWASP Top 10 security review.

1. Search for SQL, command, and template injection paths.
2. Search for unescaped user output and missing CSP headers.
3. Make sure that sensitive endpoints require authentication and authorization.
4. Review HTTPS enforcement, CORS rules, cookie settings, and other security defaults.
5. Trace untrusted input to the vulnerable operation for each possible finding.
6. Separate confirmed findings from findings that need manual review.

For each confirmed finding:
1. If an open PR already fixes the finding, record its link and continue to the next finding.
2. Add a regression test.
3. Implement the smallest safe fix.
4. Run the related tests.
5. If the tests pass, open a fix PR.
6. Include the OWASP category, severity, evidence, and affected path in the PR.

Post a summary of confirmed findings, review items, and fix PRs.
```

### 15. Cloudflare audit

```text
Create a Devin automation named "Cloudflare audit".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Run a weekly Cloudflare security audit.

1. Use the Cloudflare Audit Logs MCP to pull events from the past 7 days.
2. Flag logins from new locations, permission changes, DNS changes, and firewall rule changes.
3. If change records are available, compare each flagged event with the approved work.
4. Identify configuration changes that weaken authentication, access control, DNS security, or firewall protection.
5. Assign a severity from the evidence and the possible impact.
6. Do not revert a change automatically.
7. Post a summary with the total event count, flagged items by severity, evidence links, and recommended actions.
```

## Reports and project management

### 16. Weekly status update

```text
Create a Devin automation named "Weekly status update".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Compile a weekly engineering status update with the Notion MCP.

1. List PRs merged in the past 7 days.
2. List open PRs with meaningful activity in the same period.
3. Group the work by project area.
4. For merged PRs, state what shipped.
5. For active PRs, state the current status and any blocker.
6. Write one line for each PR. Include its link.
7. Add a section with the current date to the top of the Notion status page.
```

### 17. Backlog cleanup

```text
Create a Devin automation named "Backlog cleanup".

Trigger: a monthly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Clean the Linear backlog.

1. Find issues with no activity for 60 days or more.
2. Before you change an issue, search its linked PRs and recent changes.
3. Comment on each stale issue and mark it stale.
4. If clear evidence shows that an issue is a duplicate, resolved, or no longer relevant, close it.
5. Find likely duplicates. If the match is clear, link the duplicate to the canonical issue.
6. Flag issues with no assignee, label, or priority for triage.
7. If the issue provides enough evidence, add the missing label or priority.
8. Post a summary of all changes and all items that need a human decision.
```

### 18. Sprint report

```text
Create a Devin automation named "Sprint report".

Trigger: a daily schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Generate a daily sprint report from Asana.

1. Use the Asana MCP to list tasks in the active projects.
2. Use the project timezone to determine which tasks were completed yesterday.
3. Group tasks as completed yesterday, in progress, or blocked.
4. For each blocked task, include the owner, the blocker, and the next action.
5. Flag overdue tasks and tasks without an assignee.
6. Post a concise report to the #standup Slack channel.
```

## Data and databases

### 19. Slow query audit (PostgreSQL)

```text
Create a Devin automation named "Slow query audit (PostgreSQL)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Audit slow queries with the PostgreSQL MCP.

1. Query pg_stat_statements for queries with the highest total time, mean time, and call count.
2. Select the 10 queries with the greatest user impact.
3. Use read-only execution plans to find large sequential scans and inefficient joins.
4. Do not execute write queries or expensive analysis against production.
5. Find the source of each query in the codebase. Search both ORM calls and raw SQL.
6. If an open PR from an earlier run already fixes the query, record its link and skip it.
7. If the evidence supports an index, open a PR with the required migration.
8. Include a benchmark, rollout note, and rollback plan in the PR.
9. Post a summary with each query fingerprint, cause, impact, recommendation, and fix PR.
10. Do not include query parameter values in the summary.
```

### 20. Database health report (Amazon RDS Postgres)

```text
Create a Devin automation named "Database health report (Amazon RDS Postgres)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Compile a health report for the RDS Postgres instances.

1. Use the Amazon RDS MCP to pull storage use, connection counts, and replication lag.
2. Project the date when each instance will reach its storage limit.
3. State the growth assumptions for each projection.
4. Flag instances near their connection limits. State the threshold that you use.
5. If database access is available, find long transactions and tables with many dead tuples.
6. Recommend a specific action for each risk: more storage, connection pooling, or new vacuum settings.
7. Rank the risks by severity and time to impact.
8. Post the report to the #engineering Slack channel.
9. Do not apply scaling or database changes automatically.
```

### 21. Schema drift check (Prisma)

```text
Create a Devin automation named "Schema drift check (Prisma)".

Trigger: a daily schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Detect schema drift with the Prisma MCP.

1. Compare the Prisma schema, migration history, and live database schema.
2. If all three match, reply "No drift" and stop.
3. If these sources differ, list each table, column, type, constraint, and index difference.
4. Use the migration history to identify the likely source of the drift.
5. If an open PR from an earlier run already fixes this drift, reply with its link and stop.
6. If the intended schema is clear, generate the required schema or migration change.
7. If you generated a change, run the schema validation and migration tests.
8. If the tests pass, open a PR that explains the drift and the chosen direction.
9. If the intended schema is unclear, report the differences and request a human decision.

CAUTION: Do not apply a migration to the live database.
```

### 22. Row-level security audit (Supabase)

```text
Create a Devin automation named "Row-level security audit (Supabase)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Audit row-level security policies with the Supabase MCP.

1. List all tables in the public schema and their row-level security status.
2. For each policy, record the role, operation, and access condition.
3. Flag tables without row-level security.
4. Flag policies that allow anonymous users to write data.
5. Compare each policy with the access rules in the application code.
6. If an open PR already fixes a gap, record its link and skip that gap.
7. For each remaining gap, create a migration and a policy test.
8. Open a PR with the proposed policies and the supporting evidence.

CAUTION: Do not apply a policy to the live database. A wrong policy can expose user data.
```

### 23. Query cost audit (BigQuery)

```text
Create a Devin automation named "Query cost audit (BigQuery)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Audit query costs with the BigQuery MCP.

1. Query the job history from the past 7 days.
2. Rank query patterns by total cost, scanned bytes, and execution count.
3. Read the SQL of the top 10 query patterns.
4. Find full table scans, missing partition filters, and repeated work.
5. Identify the source and owner of each query. If either is unknown, state that in the report.
6. If the repository contains a query, open a PR with the optimized SQL.
7. Use a dry run to estimate the cost before and after the change.
8. Post the report to the #data Slack channel with costs, causes, owners, and projected savings.
```

### 24. Data governance report (Dataplex)

```text
Create a Devin automation named "Data governance report (Dataplex)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Compile a data governance report with the Dataplex MCP.

1. List data assets across the lakes and zones.
2. Flag assets without an owner, description, classification, or required tag.
3. Pull the latest data quality results. Flag failed checks and repeated failures.
4. Flag assets with possible PII and no PII classification.
5. Use metadata and scan results only. Do not include sensitive field values.
6. Rank the gaps by data sensitivity and downstream impact.
7. Post the report to the #data Slack channel with the gaps, evidence, and suggested owners.
```

### 25. Dashboard repair (Metabase)

```text
Create a Devin automation named "Dashboard repair (Metabase)".

Trigger: a merged PR that contains a database migration.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

A PR with a database migration was merged. The full event details are below.

1. Read the migration. List each renamed, changed, or removed table and column.
2. Use the Metabase MCP to find questions and dashboards that reference these items.
3. Before you update a query, make sure that the migration reached the target database.
4. If the migration is not deployed, post the affected dashboard links on the PR and stop.
5. Test each affected query against the new schema.
6. Update only the queries that the database migration broke.
7. If a query has no safe fix, notify the query owner and include the blocker.
8. Comment on the PR with the updated queries, unresolved queries, and dashboard links.
```

## Design and frontend

### 26. Design token sync (Figma)

```text
Create a Devin automation named "Design token sync (Figma)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Sync design tokens from Figma into the repository.

1. Use the Figma MCP to pull published color, typography, and spacing variables.
2. Map each Figma variable to its token in the repository.
3. List new tokens, changed values, removed tokens, and tokens with no match.
4. Before you change a token, make sure that Figma is the approved source of truth.
5. If Figma is not the source of truth, post the differences and stop.
6. Flag removed tokens that still have references in the codebase.
7. Update the token files. Do not remove a token that the code still uses.
8. Run the related build and visual tests.
9. If an open token PR from an earlier run exists, update that PR. Do not open a second PR.
10. If no open token PR exists, open a PR with a table of changes and any unresolved mapping.
```

### 27. Component parity report (Figma)

```text
Create a Devin automation named "Component parity report (Figma)".

Trigger: a monthly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Compare the Figma component library with the code component library.

1. Use the Figma MCP to list published components, variants, properties, and states.
2. List coded components and their documented variants and states.
3. Match components by purpose, not by name alone.
4. Report Figma components without code and coded components without a design.
5. Report mismatched variants, states, properties, and accessibility behavior.
6. Rank the gaps by product usage and reuse across the design system.
7. Post the report to the #design Slack channel with source links and a recommended build order.
```

## Deployments and infrastructure

### 28. Deployment failure fix (Vercel)

```text
Create a Devin automation named "Deployment failure fix (Vercel)".

Trigger: a failed production deployment on Vercel.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

A production deployment failed on Vercel. The full event details are below.

1. Use the Vercel MCP to pull the build logs and deployment metadata.
2. Find the first actionable error in the logs.
3. Reproduce the failure with the same build command and runtime version.
4. Identify whether code, a dependency, or an environment variable caused the failure.
5. If code caused the failure, fix it on a branch.
6. If code caused the failure, add a regression test.
7. If you changed the code, run the local build and related tests.
8. If the checks pass, open a PR.
9. If an environment variable is missing, report its name only. Do not set or expose its value.
10. Post the cause, evidence, PR link, and deployment status in the #deploys Slack channel.
```

### 29. Release regression watch (Sentry)

```text
Create a Devin automation named "Release regression watch (Sentry)".

Trigger: a new production release.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

A new release went to production.

1. Use the Sentry MCP to compare this release with the prior release over equivalent traffic windows.
2. Compare error rates, affected users, and new issue counts.
3. List issues that first appeared or increased in this release.
4. Read the stack trace, breadcrumbs, and changed code for each issue.
5. If the evidence supports the connection, attribute the issue to the commit.
6. If the cause is clear, implement the smallest safe fix.
7. If the cause is clear, add a regression test.
8. If you changed the code, run the related tests.
9. If the tests pass, open a fix PR.
10. If the cause is unclear, post the evidence and the next diagnostic step.
11. If the error rate increased by more than 20 percent, flag the release in the #deploys Slack channel.
```

### 30. Base image updates (Docker Hub)

```text
Create a Devin automation named "Base image updates (Docker Hub)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Keep base images current with the Docker Hub MCP.

1. List the base image, tag, and digest in each Dockerfile.
2. Pull supported stable tags, digests, release notes, and vulnerability data from Docker Hub.
3. Flag unsupported images, known vulnerabilities, and newer stable versions.
4. Unless a release fixes a critical vulnerability, do not select it until it is at least 7 days old.
5. If an open PR already updates the image, skip that image.
6. Update each image to an appropriate stable version and pin its digest.
7. Build and scan each image locally. Run the related tests.
8. If all checks pass, open a PR with the updates, risks, and release-note links.
```

### 31. Infrastructure drift check (Pulumi)

```text
Create a Devin automation named "Infrastructure drift check (Pulumi)".

Trigger: a daily schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Detect infrastructure drift with the Pulumi MCP.

1. Use read-only refresh previews and update previews for each stack.
2. Do not update the Pulumi state.
3. List resources that differ from the Pulumi code.
4. For each resource, identify the changed properties, likely source, and possible impact.
5. If the evidence shows approval, add the manual change to the Pulumi code in a PR.
6. If the evidence does not show approval, flag the manual change in the #infra Slack channel.
7. If the approval status is unclear, request a human decision in the same thread.

CAUTION: Do not run `pulumi up` or `pulumi destroy`. Do not apply or revert infrastructure changes.
```

## Business and customer tools

### 32. Webhook failure recovery (Stripe)

```text
Create a Devin automation named "Webhook failure recovery (Stripe)".

Trigger: a daily schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Investigate failed webhooks with the Stripe MCP.

1. List webhook events that failed delivery in the past 24 hours.
2. Group failures by endpoint, event type, and error.
3. Compare each failure with the endpoint logs.
4. Find the handler code and existing issues for each failed event type.
5. If an open PR already fixes a handler bug, record its link and skip that bug.

For each confirmed handler bug:
1. Add a regression test.
2. Implement the smallest safe fix.
3. Run the related tests.
4. If the tests pass, open a fix PR.

6. After the fix deploys, make sure that the handler processes duplicate events safely.
7. Prepare a replay list with event IDs only.
8. Before you resend any event, ask for human approval.
9. Post a summary with failure counts, causes, fix PRs, and replay status.

CAUTION: Do not create, change, or refund payments. Do not include customer or payment data in the summary.
```

### 33. Feature request mining (HubSpot)

```text
Create a Devin automation named "Feature request mining (HubSpot)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Find product signals in support tickets with the HubSpot MCP.

1. Pull tickets closed in the past 7 days.
2. Remove customer names, contact details, and other personal data from your notes.
3. Group the tickets as feature requests, bugs, or questions.
4. Merge requests that describe the same user need.
5. Count the distinct customers for each repeated request.
6. Before you create an issue, search GitHub for an existing one.
7. For each new bug, create a redacted issue with reproduction details and internal ticket links.
8. Post a digest to the #product Slack channel with the top requests, customer counts, and new issues.
```

### 34. Zap health report (Zapier)

```text
Create a Devin automation named "Zap health report (Zapier)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Compile a Zap health report with the Zapier MCP.

1. List Zaps, expected schedules, owners, and run history from the past 7 days.
2. Flag failed runs, sustained error rates, and unexpected periods with no runs.
3. For each failure, identify the evidence for expired authentication, an API change, or invalid data.
4. If authentication expired, identify the owner who must reconnect the account.
5. If our API caused the failure, add a regression test.
6. If our API caused the failure, implement the smallest safe fix.
7. If you changed the API, run the related tests.
8. If the tests pass, open a fix PR.
9. Do not enable, disable, or edit a Zap without human approval.
10. Post the report with affected Zaps, causes, owners, and recommended actions.
```

### 35. Roadmap sync (Airtable)

```text
Create a Devin automation named "Roadmap sync (Airtable)".

Trigger: a daily schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Sync the roadmap from Airtable to GitHub.

1. Use the Airtable MCP to list roadmap records with the status "Ready for dev".
2. Before you create an issue, search GitHub by linked URL, external ID, and title.
3. For each record without a matching issue, create one with the spec, priority, and requester role.
4. Do not include personal or confidential customer data in the issue.
5. Write the new issue URL to the Airtable record.
6. If a closed issue has a merged and deployed change, set the record status to "Shipped".
7. If an issue closed without a shipped change, flag the record and do not change its status.
8. Post a summary with created issues, updated records, and flagged records.
```

## Team and knowledge

### 36. Unanswered questions digest (Slack)

```text
Create a Devin automation named "Unanswered questions digest (Slack)".

Trigger: a daily schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Find unanswered questions in the #help channel.

1. Use the Slack MCP to list threads from the past 24 hours with no clear answer.
2. For each question, search the codebase and the docs for direct evidence.
3. If you find a clear answer, post it in the thread with source links.
4. If the answer needs private information, ask the user to continue in an approved private channel.
5. Add each unresolved question to a digest with its thread link and the missing information.
6. If the digest is empty, do not post it.
7. If the digest contains questions, post it to the #engineering Slack channel and tag the responsible team.
```

### 37. TODO comment sync (Asana)

```text
Create a Devin automation named "TODO comment sync (Asana)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Turn TODO comments into Asana tasks with the Asana MCP.

1. Search the codebase for TODO and FIXME comments.
2. Ignore generated files, vendored code, fixtures, and dependencies.
3. Search Asana for an existing task with the same file path and comment text.
4. Use CODEOWNERS to identify the owner. If no owner exists, use git blame as a fallback.
5. For each new comment, create a task with the path, line, context, and completion criteria.
6. If the identified owner has an active Asana account, assign the task.
7. If a tracked comment no longer exists, inspect the related code change.
8. If the code change completed the work, close the Asana task. If the work remains incomplete, flag the task for review.
9. Post a summary with new tasks, closed tasks, and review items.
```

### 38. Ticket reconciliation

```text
Create a Devin automation named "Ticket reconciliation".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Reconcile Jira tickets with the state of the code.

1. Use the Atlassian MCP to list tickets in the current sprint.
2. For each ticket marked "Done", find the merged PR or other completion evidence.
3. If no completion evidence exists, flag the ticket for review.
4. For each open ticket, search merged PRs for work that meets its acceptance criteria.
5. If the work is complete and deployed when required, move the ticket to "Done".
6. If you move a ticket, add the completion evidence in a comment.
7. If the evidence is incomplete, add a comment and keep the current status.
8. Flag tickets with no epic, assignee, or estimate for triage.
9. Post a summary to the #engineering Slack channel.
10. Update the sprint page in Confluence with completed work, open work, and triage items.
```

### 39. Docs drift check (Notion)

```text
Create a Devin automation named "Docs drift check (Notion)".

Trigger: a weekly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Find drift between the Notion docs and the repository.

1. Use the Notion MCP to pull the engineering setup and runbook pages.
2. Extract commands, file paths, configuration names, and version numbers from each page.
3. Compare these items with scripts, files, and package versions in the repository.
4. Collect direct evidence for each mismatch.
5. If the correct value is clear, update the Notion page with that value.
6. If the correct value is unclear, report the mismatch without changing the Notion page.
7. Post a summary with each page link, the old text, the new text, and unresolved mismatches.
```

## Engineering intelligence

### 40. Architecture decision capture (Slack)

```text
Create a Devin automation named "Architecture decision capture (Slack)".

Trigger: a Slack message that contains /decision.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

A user posted /decision in a Slack thread. The full event details are below.

1. Use the Slack MCP to read the full thread and its linked messages.
2. Identify the problem, final decision, alternatives, tradeoffs, owner, and decision date.
3. Do not infer agreement from silence or reactions alone.
4. If the thread has no explicit final decision or owner, ask one focused question and stop.
5. Search the repository for an existing architecture decision record about the same topic.
6. If an existing record conflicts with the new decision, post a link to that record.
7. If the records conflict, request a human resolution and stop.
8. Do not overwrite a conflicting record.
9. Draft a new record or update the matching record with direct evidence from the thread.
10. If no record format exists, use these sections: Context, Decision, Alternatives, Consequences, Owner, and Date.
11. Do not copy secrets, customer data, or personal data from Slack.
12. Open a documentation PR with the decision record.
13. Post the PR link and a one-line decision summary in the original Slack thread.
```

### 41. SLO error budget forecast (Datadog)

```text
Create a Devin automation named "SLO error budget forecast (Datadog)".

Trigger: an hourly schedule.

When the automation runs, start a session with the prompt below. Everything after this line is the session prompt.

Forecast SLO error budget risk with the Datadog MCP.

1. Pull all active service SLOs, targets, windows, remaining error budgets, and burn rates.
2. Compare the burn rates over 1 hour, 6 hours, 24 hours, and 7 days.
3. Identify SLOs that are projected to exhaust their error budget before the current window closes.
4. If no SLO is at risk, reply "No SLOs at risk" and stop.

For each at-risk SLO:
1. Use metrics, logs, and traces to identify the largest sources of budget use.
2. Break down the impact by service, endpoint, region, and release.
3. Compare the change with deployment markers and the recent git log.
4. Calculate the projected exhaustion time. State the assumptions behind the forecast.
5. If direct evidence supports a likely cause, identify that cause.
6. If the evidence is inconclusive, state "Cause unknown".
7. Suggest an owner from CODEOWNERS or the recent change history.

If a code change is the identified cause:
1. If an open PR already fixes the cause, record its link and continue to the next SLO.
2. Add a regression test.
3. Implement the smallest safe fix.
4. Run the related tests.
5. If the tests pass, open a fix PR.

Post a report to the #reliability Slack channel. Include the SLO, budget remaining, burn rate, forecast, cause, owner, and next action.
If a fix PR exists, include its link.

CAUTION: Do not change an SLO target. Do not deploy or roll back a release automatically.
```
