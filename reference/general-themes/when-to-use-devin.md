# When to Use Devin

> Longer training: [Foundations, What to Give AI](../../courses/foundations/concepts/04-what-to-give-ai.md) | [Engineering Track, Task Selection Framework](../../courses/tracks/engineering/04-task-selection-framework.md)

Devin takes work that people on the team normally do by hand. Before you show Devin on a task, or before a customer assigns one, name the delegation test the task meets. A task that meets at least one test belongs to Devin, and the team keeps the choice of work, the review and the merge. When a task meets none of the tests, an engineer keeps it, or the team splits it until one piece passes a test.

## Delegation Tests

| Test | Ask about the task | Concrete task |
|------|--------------------|---------------|
| **1. Not complex** | Does an engineer already know the answer and only lack the hours? | Set each Lambda function's memory to the size where CloudWatch duration and cost per invocation are lowest, and attach the before and after figures. |
| **2. Recurring** | Will someone have to do the same work again next week, month or release? | On the first of every month, find and delete the abandoned EC2 instances, EBS volumes, snapshots and load balancers, and confirm the account is clean before the session stops. |
| **3. Tedious** | Is the work a long run of small checks that a person would do one at a time? | Check every resource in the account against the Well-Architected pillars and the FinOps tagging and rightsizing rules, and open a fix for each finding. |
| **4. Too large in context retrieval** | Does the answer depend on reading several other systems before anyone writes a line? | Find out why the iOS app is slower than the web app for the same request, reading Xcode traces in a macOS session, the browser network log, the API Gateway and Lambda logs, and the CloudWatch metrics. |
| **5. Self-verifiable (go until pass)** | Can a test, a plan or a scan tell Devin the work is done? | Replatform one module of an EC2 monolith to Lambda and API Gateway with Terraform, and keep going until `terraform plan` is clean and the module's tests pass against the new endpoint. |
| **6. Otherwise benefits from delegation** | Does handing the work off free the team, or let the work run at an hour or a scale people cannot cover? | Run the application, data and analytics migrations from the Migrations page in sequence, with each wave starting after the previous wave passes its checks. |

Most good tasks meet more than one test. The monolith replatform is self-verifiable for the first module and recurring for the rest, because the procedure from the first module becomes a playbook that Devin runs over every other module.

## Presenter Lines

Say the line for the test the task meets, then show Devin doing the task.

| Test | How to say it |
|------|---------------|
| **1. Not complex** | "Your engineers already know how to size a Lambda function, so Devin sizes every function in the account and attaches the cost before and after." |
| **2. Recurring** | "The cleanup comes back every month, so a schedule opens the session and nobody has to remember the date." |
| **3. Tedious** | "A Well-Architected review is one small check after another across the whole account, and Devin runs every check and opens a fix for each finding." |
| **4. Too large in context retrieval** | "The answer is spread across Xcode, the browser, API Gateway and CloudWatch, and Devin reads the traces, the logs and the metrics before changing a line." |
| **5. Self-verifiable (go until pass)** | "Devin keeps working on the module until the plan is clean and the tests pass, and the next module runs from the same playbook." |
| **6. Otherwise benefits from delegation** | "Your team owns the migration plan and the review, and Devin runs each wave and starts the next wave once the checks pass." |

## Sessions Started by an Alarm, a Schedule or a Webhook

Devin also works as an on-demand worker that nobody prompts. A Devin Automation listens for the trigger and opens the session when the work arrives, so the first person to touch the task is the reviewer of the pull request.

| Trigger | Source | Concrete task |
|---------|--------|---------------|
| **Alarm** | A CloudWatch alarm routed through EventBridge to a Devin Automation | A tester session breaks a deployment on purpose, the CloudWatch alarm fires, and the session the automation opens reads the alarm, the logs and the deploy history, restores the configuration from git and opens a pull request so the same break fails at boot. |
| **Schedule** | A cron trigger on an Automation | A monthly session reaps abandoned cloud resources and stops only after verifying the account is clean. A weekly code scan opens a remediation session for each finding, and each session re-runs the scan until the finding clears. |
| **Webhook** | An HTTP POST from GitHub, Jira, a CI pipeline or a scanner | A Jira ticket moves to "Ready for Devin" and the webhook opens a session that implements the ticket and opens a pull request. A failed pipeline posts its run, and Devin diagnoses the failure and pushes the fix. |

Say: "The alarm opened this session without anyone prompting Devin, and the first person involved is the engineer reviewing the pull request."

## Trigger Categories

| Category | Trigger | Examples | Common tests |
|----------|---------|----------|------------------------------|
| **Event-Driven** | An alarm or webhook from your toolchain | CloudWatch alarm opens a triage and fix session; a SAST finding gets remediated and re-scanned; a tagged ticket becomes a pull request; a CI failure gets diagnosed and fixed | Self-verifiable, otherwise benefits from delegation |
| **Large-Scale Campaign** | Many independent targets, same pattern | Replatform the first monolith module to Lambda, then run the playbook over the other modules in parallel sessions; COBOL to Java (200 copybooks); Spring Boot 2 to 3 (50 services); security backlog (500 findings across 80 repos) | Recurring, tedious, self-verifiable |
| **Capacity-Constrained** | Recurring maintenance that people defer | Monthly abandoned-resource cleanup; weekly dependency bumps; Lambda cost-performance tuning; doc refresh on code change; quarterly license audit | Not complex, recurring, tedious |

## Prerequisites for a Delegated Task

| Requirement | Why |
|-------------|-----|
| **Indexed codebase** | DeepWiki and Ask Devin give Devin the architecture before the first edit |
| **Programmatic access** | Devin reads CloudWatch, Jira, the scanner or the cloud account through an API, CLI or MCP server, so a task with a large context-retrieval load can still pass |
| **Locally buildable code** | Devin runs the tests and the build on its own machine, so the self-verifiable test has a check to run |
| **A stated finish line** | The prompt or playbook names the passing check (tests green, `terraform plan` clean, scan clear, account empty of abandoned resources) |
| **A branch and a reviewer** | Devin works on a branch and opens a pull request, and a person approves the merge |

## Key Takeaways

- Name the delegation test before you show Devin on the task, and leave a task that meets no test with an engineer.
- Devin takes the deferred and repeated work, so engineers spend their hours on design and review.
- An alarm, a schedule or a webhook can open the session, and the first human step is the review.
- Large campaigns start with one target done well, then a playbook and parallel sessions for the rest.
