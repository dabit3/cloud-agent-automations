# Cloud Agent Workflows

Prompt templates for [Devin automations](https://docs.devin.ai/product-guides/automations).

Each template is a complete prompt with numbered steps and a recommended trigger. The full setup is one step: paste the prompt into a new Devin automation. An automation starts a Devin session when a trigger occurs. A trigger can be a Slack message, a GitHub event, or a schedule.

Many templates use an MCP server, for example Datadog, Stripe, Figma, or Supabase. Each template names the MCP that it uses.

## How to use the templates

1. Copy the prompt from a template below.
2. Create a new automation in Devin. Paste the prompt. Set the trigger that the template recommends.
3. If your channel names, service names, or schedule differ from the template, change them in the prompt.
4. If the template uses an MCP, connect that MCP in Devin before the first run.

## Slack triage

### 1. Bug triage

Trigger: a new message in the bug channel.

```text
A user posted a bug report in the channel.

1. Read the report.
2. Search the codebase for the related code.
3. Identify the root cause.
4. If you are highly confident in the root cause, post your findings in the thread.
5. If you are not confident, do not post.
```

### 2. Support replies

Trigger: a new message in the support channel.

```text
A user posted a support request in the channel.

1. Identify the technical problem.
2. Search the codebase and the docs for related context.
3. Draft a reply in the thread. Include a direct answer, links to related docs, and the next steps.
4. If the problem is a bug, state the severity in the reply.
5. If the bug needs a fix PR, state that in the reply.
6. If an item needs a human decision, flag that item in the thread.
```

## Monitoring and incidents

### 3. Alert investigation

Trigger: a new alert message in the alerts channel.

```text
An alert was posted to Slack. The full event details are below.

1. Read the alert. Identify the affected service and the affected metric.
2. Use the Datadog MCP to pull metrics, logs, and traces from the alert window.
3. Compare the alert time with the recent deployments in the git log.
4. Identify the root cause: a code change, a traffic spike, or an infrastructure problem.
5. If the root cause is a code change, open a fix PR.
6. Post your findings in the Slack thread: the root cause, the impact, and the next steps.
```

### 4. Sentry error fixes

Trigger: a daily schedule.

```text
Use the Sentry MCP to pull the unresolved errors from the past 24 hours. Sort the errors by frequency.

For each of the top 5 errors:
1. Pull the stack trace and the breadcrumbs.
2. Find the related source code.
3. Open a fix PR with a regression test. Link the Sentry issue in the PR.

If an error has the tag 'wontfix' or 'expected-behavior', skip it.
At the end, post a summary of the errors and the fix PRs.
```

### 5. Daily error report

Trigger: a daily schedule.

```text
Compile a daily error report with the Datadog MCP.

1. Query Datadog for the error rates and the error logs of the key services for the past 24 hours.
2. Identify new error types and errors that increased since the prior day.
3. Compare the notable errors with the recent deployments in the git log.
4. Compile a report: the top errors by volume, the new and increased errors, the likely causes, and the suggested owners.
5. Post the report to the #engineering Slack channel.
```

### 6. Capacity review

Trigger: a weekly schedule.

```text
Do a capacity review with the Datadog MCP.

1. Pull the CPU, memory, request-rate, and queue-depth trends for the key services. Use the data from recent weeks.
2. Identify services with continuous growth. Identify services near their resource limits or their autoscaling ceilings.
3. For each of these services, calculate when it will reach its capacity at the current growth rate.
4. Recommend scaling actions: instance counts, resource limits, or sharding.
5. Post a capacity summary to the #engineering Slack channel.
```

## CI and GitHub events

### 7. CI failure fix

Trigger: a failed check run on GitHub.

```text
A CI check failed. The full event details are below.

IMPORTANT: If devin-ai-integration[bot] authored the commit that caused the failure, reply 'Skipping: commit authored by Devin' and stop.

If a different author made the commit:
1. Open the URL of the failed check run. Read the build logs and the test logs.
2. Identify the root cause of the failure.
3. Fix the problem on the same branch.
4. Run the related tests locally. Make sure that the tests pass.
5. Push the fix.
```

### 8. Issue command

Trigger: an issue comment that contains /devin.

```text
A user commented /devin on a GitHub issue. The full event details are below.

1. Read the issue title, the issue body, and all follow-up comments.
2. Search the codebase for the related code paths.
3. Implement the fix with tests.
4. Run the test suite. Make sure that all tests pass.
5. Open a PR that references the issue.
```

## Repository maintenance

### 9. Dependency updates

Trigger: a weekly schedule.

```text
Scan for outdated dependencies.

1. Find the available updates in the package manager files.
2. Read the changelogs of the updates. Find the breaking changes.
3. Open one PR. Group the updates by risk level: patch, minor, and major.
4. Run the tests. Make sure that all tests pass.
```

### 10. Weekly changelog

Trigger: a weekly schedule.

```text
Generate a weekly changelog.

1. List all PRs merged in the past 7 days.
2. Put each PR in one category: features, bug fixes, improvements, or breaking changes.
3. Write a one-line summary for each change.
4. Open a PR that updates CHANGELOG.md.
```

### 11. Stale PR reminders

Trigger: a weekly schedule.

```text
Scan for stale pull requests.

1. List the open PRs with no update for more than 7 days.
2. Examine each PR for merge conflicts.
3. Post a friendly comment on each stale PR. Ask the author to update the PR or close it.
4. If a PR has merge conflicts, tell the author in the comment.
```

## Security

### 12. Vulnerability scan

Trigger: a weekly schedule.

```text
Scan for dependency vulnerabilities.

1. Run the audit commands, for example `npm audit` or `pip-audit`. Compare the results with the GitHub Advisory Database.
2. If a critical or high severity vulnerability has an available patch, update the dependency. Then run the tests.
3. Open one fix PR for each vulnerability. Include the CVE ID and the severity in the PR.
4. Post a summary: the count of vulnerabilities by severity and the list of fix PRs.
```

### 13. Secret scan

Trigger: a weekly schedule.

```text
Scan the codebase for leaked secrets.

1. Search for API keys, tokens, passwords, private keys, and connection strings. Use pattern matching and entropy analysis.
2. Examine the .env files, the configuration files, and the hardcoded strings in the source code.
3. For each finding, replace the secret with an environment variable reference. Add the variable to .env.example.
4. Open one fix PR for each finding.

CAUTION: Do not put the secret value in a PR, a commit message, or a summary.
```

### 14. OWASP scan

Trigger: a monthly schedule.

```text
Run an OWASP Top 10 security scan.

1. Search for injection vectors: SQL injection, command injection, and template injection.
2. Search for unescaped user output (XSS). Make sure that the responses have CSP headers.
3. Make sure that the sensitive endpoints have authentication and authorization.
4. Search for insecure defaults and weak CORS or cookie settings. Make sure that the server enforces HTTPS.
5. Open a fix PR for each finding. Include the OWASP category and the severity rating in each PR.
```

### 15. Cloudflare audit

Trigger: a weekly schedule.

```text
Run a weekly Cloudflare security audit.

1. Use the Cloudflare Audit Logs MCP to pull the logs from the past 7 days.
2. Flag suspicious activity: unusual login locations, permission changes, DNS modifications, and firewall rule changes.
3. Find configuration changes that can have an effect on security.
4. Post a summary: the total events, the flagged items by severity, and the recommended actions.
```

## Reports and project management

### 16. Weekly status update

Trigger: a weekly schedule.

```text
Compile a weekly engineering status update with the Notion MCP.

1. List all PRs merged in the past 7 days across the repository.
2. Summarize each project area: what shipped and what is in progress.
3. Add a new dated section to the top of the Notion status page.

Keep the summaries short. Write one line for each PR.
```

### 17. Backlog cleanup

Trigger: a monthly schedule.

```text
Clean the Linear backlog.

1. Find issues with no activity for 60 days or more.
2. Comment on each stale issue and mark it stale. If the issue is clearly obsolete, close it instead.
3. Find likely duplicates. Link each duplicate to the canonical issue.
4. Flag untriaged issues for review. An untriaged issue has no assignee, no label, and no priority.
5. If a missing label or priority is obvious, add it.
6. Post a summary of the changes. Include the items that need a human decision.
```

### 18. Sprint report

Trigger: a daily schedule.

```text
Generate a daily sprint report from Asana.

1. Use the Asana MCP to list the tasks in the active projects.
2. Group the tasks by status: completed yesterday, in progress, and blocked.
3. Flag overdue tasks and tasks without an assignee.
4. Post the report to the #standup Slack channel.
```

## Data and databases

### 19. Slow query audit (PostgreSQL)

Trigger: a weekly schedule.

```text
Audit the slow queries with the PostgreSQL MCP.

1. Query pg_stat_statements for the queries with the highest total time and the highest mean time.
2. Run EXPLAIN on the top 10 queries. Find sequential scans on large tables.
3. Find the source of each query in the codebase (ORM call or raw SQL).
4. If an index removes the bottleneck, open a PR with a migration that adds the index.
5. Post a summary: the slow queries, the causes, and the fix PRs.
```

### 20. Database health report (Amazon RDS Postgres)

Trigger: a weekly schedule.

```text
Compile a health report for the RDS Postgres instances.

1. Use the Amazon RDS MCP to pull the storage use, the connection counts, and the replication lag.
2. Calculate when each instance will reach its storage limit at the current growth rate.
3. Flag instances near their connection limits.
4. Query for long transactions and for tables with many dead tuples.
5. Recommend actions: storage increases, connection pooling, or vacuum settings.
6. Post the report to the #engineering Slack channel.
```

### 21. Schema drift check (Prisma)

Trigger: a daily schedule.

```text
Detect schema drift with the Prisma MCP.

1. Compare the Prisma schema with the live database schema.
2. If the schemas match, reply "No drift" and stop.
3. If the schemas differ, list the differences: tables, columns, and types that do not match.
4. Generate the migration that removes the drift.
5. Open a PR with the migration and a description of each difference.
```

### 22. Row level security audit (Supabase)

Trigger: a weekly schedule.

```text
Audit the row level security policies with the Supabase MCP.

1. List all tables in the public schema.
2. Flag tables without row level security.
3. Read each policy. Flag policies that give anonymous users write access.
4. Compare the policies with the access rules in the application code.
5. Open a PR with the new policies.

CAUTION: Do not change a policy on a live table without a human review. A wrong policy can expose user data.
```

### 23. Query cost audit (BigQuery)

Trigger: a weekly schedule.

```text
Audit the query costs with the BigQuery MCP.

1. Query the job history for the past 7 days. Rank the queries by scanned bytes and by cost.
2. Read the SQL of the top 10 queries. Find full table scans and missing partition filters.
3. Identify tables that need partitioning or clustering.
4. If the repository contains the query, open a PR with the optimized SQL.
5. Post a report to the #data Slack channel: the top costs, the causes, and the projected savings.
```

### 24. Data governance report (Dataplex)

Trigger: a weekly schedule.

```text
Compile a data governance report with the Dataplex MCP.

1. List the data assets across the lakes and zones.
2. Flag assets without an owner, a description, or tags.
3. Pull the results of the data quality scans. Flag the failed checks.
4. Flag tables with possible PII and no PII tag.
5. Post a report to the #data Slack channel: the gaps by type and the suggested owners.
```

### 25. Dashboard repair (Metabase)

Trigger: a merged PR that contains a database migration.

```text
A PR with a database migration was merged. The full event details are below.

1. Read the migration. List the renamed, changed, and removed tables and columns.
2. Use the Metabase MCP to find the questions and dashboards that reference these tables and columns.
3. For each broken question, update the query to match the new schema.
4. If a question has no clear fix, flag it for the owner.
5. Post a comment on the PR: the updated questions and the flagged questions.
```

## Design and frontend

### 26. Design token sync (Figma)

Trigger: a weekly schedule.

```text
Sync the design tokens from Figma into the repository.

1. Use the Figma MCP to pull the color, typography, and spacing variables from the design library.
2. Compare them with the token files in the repository.
3. List each difference: new tokens, changed values, and removed tokens.
4. Open a PR that updates the token files to match Figma.
5. Include a table of the changes in the PR description.
```

### 27. Component parity report (Figma)

Trigger: a monthly schedule.

```text
Compare the Figma component library with the code component library.

1. Use the Figma MCP to list the published components and their variants.
2. List the components in the repository.
3. Report the gaps: components in Figma without code, and components in code without a design.
4. Report components with mismatched variants or states.
5. Post the report to the #design Slack channel with a suggested build order.
```

## Deployments and infrastructure

### 28. Deployment failure fix (Vercel)

Trigger: a failed production deployment on Vercel.

```text
A production deployment failed on Vercel. The full event details are below.

1. Use the Vercel MCP to pull the build logs for the failed deployment.
2. Identify the cause: a build error, a missing environment variable, or a bad dependency.
3. If the cause is in the code, fix it on a branch and open a PR.
4. If an environment variable is missing, name the variable and flag it for a human. Do not set secret values yourself.
5. Post the cause and the fix in the #deploys Slack channel.
```

### 29. Release regression watch (Sentry)

Trigger: a new production release.

```text
A new release went to production.

1. Use the Sentry MCP to compare the error rate of this release with the prior release.
2. List the issues that first appeared in this release.
3. Read the stack trace of each new issue. Match it to a commit in the release with git log.
4. If a commit caused the issue, comment on its PR with the Sentry link and the evidence.
5. If the error rate increased more than 20 percent, flag the release in the #deploys Slack channel.
```

### 30. Base image updates (Docker Hub)

Trigger: a weekly schedule.

```text
Keep the base images current with the Docker Hub MCP.

1. List the base images in each Dockerfile in the repository.
2. For each image, pull the available tags and digests from Docker Hub.
3. Flag images with a newer stable tag and images with known vulnerabilities.
4. Update the Dockerfiles to the newer tags. Build the images locally.
5. If the builds pass, open one PR with the updates and the reason for each.
```

### 31. Infrastructure drift check (Pulumi)

Trigger: a daily schedule.

```text
Detect infrastructure drift with the Pulumi MCP.

1. Run a refresh and a preview for each stack.
2. List the resources that changed outside of the Pulumi code.
3. For each drifted resource, identify the change and the likely source.
4. If the manual change is correct, open a PR that adds it to the Pulumi code.
5. If the manual change looks wrong, flag it in the #infra Slack channel. Do not revert it yourself.

CAUTION: Do not run `pulumi up` or `pulumi destroy`. Report the drift only.
```

## Business and customer tools

### 32. Webhook failure recovery (Stripe)

Trigger: a daily schedule.

```text
Recover failed webhooks with the Stripe MCP.

1. List the webhook events that failed delivery in the past 24 hours.
2. Group the failures by endpoint and by error type.
3. Find the handler code for each failed event type.
4. If the handler has a bug, fix it and open a PR.
5. After the fix deploys, resend the failed events.
6. Post a summary: the failure counts, the causes, and the resent events.

CAUTION: Do not create, change, or refund payments. Work with webhook events only.
```

### 33. Feature request mining (HubSpot)

Trigger: a weekly schedule.

```text
Mine the support tickets for product signals with the HubSpot MCP.

1. Pull the tickets closed in the past 7 days.
2. Sort the tickets into groups: feature requests, bugs, and questions.
3. For each repeated feature request, count the customers who asked for it.
4. For each bug with no GitHub issue, create an issue with the ticket links.
5. Post a digest to the #product Slack channel: the top requests by customer count and the new issues.
```

### 34. Zap health report (Zapier)

Trigger: a weekly schedule.

```text
Compile a Zap health report with the Zapier MCP.

1. List the Zaps and their run history for the past 7 days.
2. Flag Zaps with failed runs, high error rates, or no runs.
3. For each failure, identify the cause: expired auth, a changed API, or bad data.
4. If our own API caused the failure, fix the API and open a PR.
5. Post the report: the failing Zaps, the causes, and the recommended fixes.
```

### 35. Roadmap sync (Airtable)

Trigger: a daily schedule.

```text
Sync the roadmap from Airtable to GitHub.

1. Use the Airtable MCP to list the roadmap records with the status "Ready for dev".
2. For each record without a linked GitHub issue, create an issue. Include the spec, the priority, and the requester.
3. Write the new issue URL back to the Airtable record.
4. For each record with a closed issue, set the record status to "Shipped".
5. Post a summary of the created issues and the updated records.
```

## Team and knowledge

### 36. Unanswered questions digest (Slack)

Trigger: a daily schedule.

```text
Find the unanswered questions in the #help channel.

1. Use the Slack MCP to list the threads from the past 24 hours with no reply.
2. For each question, search the codebase and the docs for an answer.
3. If you find a clear answer, post it in the thread with links to the source.
4. If you do not find an answer, add the question to a digest.
5. Post the digest to the #engineering Slack channel and tag the on-call engineer.
```

### 37. TODO comment sync (Asana)

Trigger: a weekly schedule.

```text
Turn TODO comments into Asana tasks with the Asana MCP.

1. Search the codebase for TODO and FIXME comments.
2. Use git blame to find the author of each comment.
3. For each comment without an Asana task, create a task. Include the file path, the line, and the context.
4. Assign the task to the comment author.
5. If the comment for an open task no longer exists in the code, close the task.
6. Post a summary: the new tasks and the closed tasks.
```

### 38. Ticket reconciliation (Atlassian)

Trigger: a weekly schedule.

```text
Reconcile the Jira tickets with the state of the code.

1. Use the Atlassian MCP to list the tickets in the current sprint.
2. For each ticket marked "Done", make sure that a merged PR references it. If no PR exists, flag the ticket.
3. For each open ticket, search the merged PRs. If a merged PR completed the work, move the ticket to "Done" with a comment.
4. Find tickets with no epic, no assignee, or no estimate. Flag them for triage.
5. Post a summary to the #engineering Slack channel and update the sprint page in Confluence.
```

### 39. Docs drift check (Notion)

Trigger: a weekly schedule.

```text
Find drift between the Notion docs and the repository.

1. Use the Notion MCP to pull the engineering setup and runbook pages.
2. Extract the commands, the file paths, and the version numbers from the pages.
3. Compare them with the repository: scripts, configuration files, and package versions.
4. If a page names a command or a path that no longer exists, update the page.
5. Post a summary of the updated pages and the changes.
```
