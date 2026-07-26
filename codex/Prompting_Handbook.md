# Practical Codex Prompting Handbook

> A beginner-friendly guide to using OpenAI Codex effectively: understanding what to ask, writing useful prompts, supervising its work, reviewing changes, and learning software development through Codex.

---

## Table of Contents

1. [What Codex Actually Is](#1-what-codex-actually-is)
2. [The Most Important Mindset](#2-the-most-important-mindset)
3. [The Anatomy of a Good Codex Prompt](#3-the-anatomy-of-a-good-codex-prompt)
4. [The Core Codex Workflow](#4-the-core-codex-workflow)
5. [How to Know What to Ask](#5-how-to-know-what-to-ask)
6. [Understanding a New Repository](#6-understanding-a-new-repository)
7. [Planning Before Coding](#7-planning-before-coding)
8. [Implementing Features](#8-implementing-features)
9. [Debugging Problems](#9-debugging-problems)
10. [Testing and Verification](#10-testing-and-verification)
11. [Reviewing Codex’s Work](#11-reviewing-codexs-work)
12. [Using Codex to Learn Programming](#12-using-codex-to-learn-programming)
13. [Using `AGENTS.md`](#13-using-agentsmd)
14. [Managing Context](#14-managing-context)
15. [Working With Large Tasks](#15-working-with-large-tasks)
16. [Safe and Responsible Usage](#16-safe-and-responsible-usage)
17. [Common Prompting Mistakes](#17-common-prompting-mistakes)
18. [Reusable Prompt Library](#18-reusable-prompt-library)
19. [A Complete Example Workflow](#19-a-complete-example-workflow)
20. [A Personal Practice Plan](#20-a-personal-practice-plan)
21. [Recommended Guides and People](#21-recommended-guides-and-people)
22. [Final Checklist](#22-final-checklist)

---

# 1. What Codex Actually Is

Codex is not only a chatbot that writes code.

It is a coding agent that can work with a real project. Depending on how you use it, Codex can:

* read files;
* inspect a repository;
* search through code;
* edit multiple files;
* execute terminal commands;
* run tests;
* inspect errors;
* revise its implementation;
* review code;
* explain a codebase;
* generate documentation;
* create plans for larger engineering tasks.

OpenAI describes Codex as an agent for tasks such as implementing features, fixing bugs, answering questions about a codebase, performing refactors, reviewing changes, and proposing pull requests.

The important word is **agent**.

A normal chatbot mainly answers questions. A coding agent can investigate a problem, take actions, observe the results, and continue working.

A typical Codex loop looks like this:

```text
Read the task
    ↓
Inspect the repository
    ↓
Find the relevant files
    ↓
Create a plan
    ↓
Modify the code
    ↓
Run tests and checks
    ↓
Inspect failures
    ↓
Fix the implementation
    ↓
Summarize the result
```

Your job is therefore not to describe every line of code that Codex should write.

Your job is to give it:

* a clear destination;
* enough relevant context;
* important constraints;
* permission boundaries;
* a way to verify success.

---

# 2. The Most Important Mindset

Do not treat Codex like a search engine.

Treat it like a capable but unfamiliar developer who has just joined your project.

That developer may be technically strong, but they do not automatically know:

* what your application is supposed to do;
* which behavior is intentional;
* which files are generated;
* which architecture rules matter;
* what counts as finished;
* which trade-offs your team prefers;
* what actions are too risky.

## Weak mindset

```text
Make the login work.
```

The request has too many unanswered questions:

* What is currently broken?
* What should happen after login?
* Which authentication system is used?
* Can dependencies be added?
* How should success be tested?
* Should Codex only investigate or also edit files?

## Better mindset

```text
The login form submits successfully, but the user remains on the login
page instead of being redirected to /dashboard.

Investigate the authentication flow and identify the root cause.

Relevant areas are likely:
- frontend/src/pages/Login.tsx
- frontend/src/services/auth.ts
- backend/src/auth/

Constraints:
- Do not replace the authentication library.
- Do not change the public API unless necessary.
- Keep the existing coding style.

First inspect the implementation and explain the cause.
Then make the smallest reasonable fix.
Run the relevant tests and report exactly what you verified.
```

The second prompt gives Codex a goal, evidence, likely locations, boundaries, an order of work, and a verification requirement.

---

# 3. The Anatomy of a Good Codex Prompt

A strong Codex prompt usually contains six parts:

1. **Goal**
2. **Context**
3. **Evidence**
4. **Constraints**
5. **Process**
6. **Verification**

You do not need all six sections for every small task. However, thinking through them prevents most prompting problems.

---

## 3.1 Goal

Describe the desired outcome.

```text
Add pagination to the users table.
```

Better:

```text
Add server-side pagination to the admin users table so that it initially
shows 25 users and allows the administrator to move between pages.
```

Focus on the behavior you want, not only the code you imagine.

---

## 3.2 Context

Explain where the task belongs.

```text
The frontend is React with TypeScript.
The backend is Spring Boot with PostgreSQL.
The current table is implemented in AdminUsersPage.tsx.
The endpoint is GET /api/admin/users.
```

Codex can inspect the repository itself, but a small amount of accurate context helps it begin in the right place.

---

## 3.3 Evidence

Give Codex observations rather than only conclusions.

Weak:

```text
The backend is broken.
```

Better:

```text
POST /api/exams returns HTTP 500 when roomId is missing.

The log contains:

java.lang.NullPointerException:
Cannot invoke "Room.getId()" because "room" is null
```

Useful evidence includes:

* exact error messages;
* stack traces;
* failing test names;
* expected and actual results;
* commands that reproduce the problem;
* screenshots;
* relevant logs;
* example inputs and outputs.

---

## 3.4 Constraints

State what Codex must preserve or avoid.

```text
Constraints:
- Do not change the database schema.
- Do not introduce a new dependency.
- Preserve the existing API response format.
- Only modify files relevant to this bug.
- Follow the repository's existing naming conventions.
```

Constraints prevent Codex from solving the task in an unacceptable way.

---

## 3.5 Process

Tell Codex how autonomous it should be.

For investigation only:

```text
Do not edit anything yet. Inspect the code and explain the likely root cause.
```

For planning first:

```text
Inspect the repository and propose a plan before changing files.
```

For implementation:

```text
Inspect the relevant code, implement the fix, run the appropriate checks,
and summarize the changes.
```

For a risky task:

```text
You may modify application source files, but stop before changing migrations,
deployment configuration, credentials, or production infrastructure.
```

Current OpenAI model guidance recommends defining autonomy and approval boundaries, particularly for multi-step tasks that might become destructive, costly, external, or broader than the original request.

---

## 3.6 Verification

Define what proves the task is finished.

```text
Verification:
- Existing tests continue to pass.
- Add a regression test for the bug.
- Run the backend test suite.
- Run formatting and static-analysis checks.
- Explain any checks you could not run.
```

OpenAI’s current Codex prompting guidance emphasizes naming the desired behavior, pointing to relevant code or reproduction steps, preserving constraints, and explaining how the result should be verified.

---

# 4. The Core Codex Workflow

The most reliable workflow is:

```text
Understand → Plan → Implement → Verify → Review
```

Do not immediately ask Codex to make large changes when neither you nor Codex understands the project.

---

## Phase 1: Understand

```text
Inspect this repository and explain:

1. What the application does.
2. The main technologies.
3. The purpose of each top-level directory.
4. The important entry points.
5. How data moves through the application.
6. How the project is built and tested.
7. Which areas I should understand before modifying it.

Do not edit anything.
```

---

## Phase 2: Plan

```text
I want to add email verification after registration.

Inspect the existing registration and authentication flows.

Do not edit files yet.

Create an implementation plan containing:
- current behavior;
- relevant files and components;
- proposed behavior;
- required data changes;
- API changes;
- edge cases;
- security concerns;
- tests;
- risks and open questions.
```

---

## Phase 3: Implement

```text
Implement the approved plan.

Requirements:
- Keep the change limited to the planned scope.
- Follow existing patterns.
- Add or update tests.
- Do not silently change unrelated behavior.
- Do not add dependencies unless clearly necessary.
```

---

## Phase 4: Verify

```text
Verify the implementation thoroughly.

Run:
- relevant unit tests;
- integration tests;
- formatting;
- static analysis;
- the build.

Also manually inspect the main user flow where possible.

Report:
- commands executed;
- results;
- failures;
- anything that was not tested.
```

---

## Phase 5: Review

```text
Review the completed diff as a strict senior engineer.

Look specifically for:
- incorrect behavior;
- missing edge cases;
- security problems;
- concurrency issues;
- weak error handling;
- unnecessary complexity;
- duplicated logic;
- missing tests;
- accidental unrelated changes.

Fix confirmed problems, rerun the relevant checks, and summarize the final state.
```

OpenAI has described internal agent-first workflows where Codex reviews its own changes, receives additional review feedback, fixes problems, and iterates until the changes satisfy reviewers.

---

# 5. How to Know What to Ask

Beginners often believe their problem is that they cannot write sophisticated prompts.

Usually the real problem is that they do not yet know which engineering questions exist.

Use the following question categories.

---

## 5.1 Questions about purpose

```text
What problem does this component solve?

What user behavior is this code responsible for?

What would stop working if this file were removed?

What assumptions does this implementation make?
```

---

## 5.2 Questions about structure

```text
Where does the application start?

Which files contain business logic?

Which files only contain configuration?

Where are database operations performed?

Where are HTTP requests handled?

How are frontend and backend connected?
```

---

## 5.3 Questions about behavior

```text
Trace what happens after the user clicks Submit.

Trace this request from the controller to the database and back.

What happens when this operation fails?

Which edge cases are currently handled?

Which edge cases are missing?
```

---

## 5.4 Questions about changes

```text
Which files need to change?

What is the smallest safe implementation?

Could this be implemented without changing the public API?

What existing pattern should the new feature follow?

What other behavior could this modification affect?
```

---

## 5.5 Questions about correctness

```text
How can we prove this works?

Which tests cover this behavior?

What test would fail before the fix and pass afterward?

What manual test should I perform?

Which assumptions cannot be verified automatically?
```

---

## 5.6 Questions about risk

```text
Could this break existing data?

Does this expose sensitive information?

Could two requests interfere with each other?

Is user input validated?

Could an attacker misuse this endpoint?

Does this operation require authorization?
```

---

## 5.7 Questions about maintainability

```text
Is this consistent with the rest of the codebase?

Is the logic duplicated elsewhere?

Are the names understandable?

Is this implementation more complicated than necessary?

Would a new developer understand this code?
```

When you do not know what to ask, ask Codex to generate the questions:

```text
I am new to this repository and do not yet know which questions matter.

Inspect the project and give me the 20 most important questions I should
be able to answer before making changes.

Group them by:
- architecture;
- development workflow;
- testing;
- data;
- security;
- deployment;
- common failure points.

Answer each question using evidence from the repository.
```

---

# 6. Understanding a New Repository

Before using Codex to change an unfamiliar project, ask it to build a mental model.

## Repository onboarding prompt

```text
Act as a senior developer onboarding me to this repository.

Inspect the repository thoroughly, but do not edit anything.

Create a structured onboarding guide covering:

1. Purpose of the application
2. Technology stack
3. Top-level folder structure
4. Application entry points
5. Main modules
6. Data flow
7. Database model
8. API structure
9. Authentication and authorization
10. Configuration
11. Local development setup
12. Build process
13. Testing strategy
14. Deployment-related files
15. Logging and error handling
16. Important conventions
17. Areas that are risky to modify
18. A recommended reading order for the code

For each important explanation, reference the relevant file paths.

Clearly distinguish verified facts from your own inferences.
```

---

## Trace one feature

Do not try to understand the entire repository at once.

Pick one user action.

```text
Trace the complete flow for creating an exam.

Start from the frontend action and follow it through:
- UI component;
- state management;
- API client;
- HTTP endpoint;
- controller;
- service;
- repository;
- database entities;
- response handling;
- error handling.

List the files in execution order and explain the responsibility of each one.

Do not edit anything.
```

---

## Ask for a glossary

```text
Create a glossary of project-specific terms found in the repository.

For every term include:
- plain-English meaning;
- where it appears;
- related classes, functions, or database tables;
- why it matters.
```

This is especially useful in business applications where words such as “block,” “request,” “publication,” or “allocation” have project-specific meanings.

---

# 7. Planning Before Coding

A plan is especially useful when a task:

* affects several files;
* changes architecture;
* touches the database;
* changes an API;
* involves authentication;
* contains unclear requirements;
* could introduce regressions;
* will take several development steps.

## Planning prompt

```text
We need to implement the following feature:

[Describe the feature.]

Before editing anything:

1. Inspect the existing implementation.
2. Identify the relevant files.
3. Explain the current behavior.
4. Find similar patterns already used in the repository.
5. Propose the smallest maintainable solution.
6. Identify edge cases.
7. Identify security and data-consistency concerns.
8. Describe the required tests.
9. List any assumptions or unanswered questions.
10. Provide a step-by-step implementation plan.

Do not modify files yet.
```

---

## Ask Codex to challenge the request

Sometimes your own idea is not the best solution.

```text
Do not assume my proposed solution is correct.

Evaluate:
- whether the requested behavior already exists;
- whether there is a simpler solution;
- whether my approach conflicts with the architecture;
- whether it creates security or maintenance problems;
- whether the requirement is ambiguous.

Recommend a better approach if appropriate.
```

---

## Plan at file level

```text
For every file you propose changing, provide:

- file path;
- current responsibility;
- exact reason it must change;
- planned modification;
- risks;
- corresponding tests.
```

This helps you detect when Codex is about to change too much.

---

# 8. Implementing Features

A feature prompt should describe behavior, not merely appearance.

## Feature implementation template

```text
Implement the following feature:

## Desired behavior

[Describe what the user should be able to do.]

## Current behavior

[Describe what happens now.]

## Requirements

- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

## Edge cases

- [Edge case 1]
- [Edge case 2]

## Constraints

- Preserve [existing behavior/API/schema].
- Do not add dependencies without a clear reason.
- Follow existing project patterns.
- Avoid unrelated refactoring.
- Do not modify generated files.

## Process

1. Inspect the relevant implementation.
2. Explain your planned approach briefly.
3. Implement the feature.
4. Add or update tests.
5. Run the relevant checks.
6. Review the final diff.

## Completion criteria

- [Observable result 1]
- [Observable result 2]
- All relevant tests pass.
```

---

## Example: frontend feature

```text
Add a visible backend error code to the frontend error message.

Current backend error response:

{
  "code": "PT-404-001",
  "message": "The requested resource was not found.",
  "status": 404,
  "timestamp": "2026-07-08T10:15:30Z"
}

Desired behavior:

When an API request fails, the frontend should show the human-readable
message and the error code. Example:

The requested resource was not found.
Error code: PT-404-001

Requirements:
- Reuse the existing notification component.
- Handle responses where code is absent.
- Do not expose stack traces or internal exception details.
- Keep existing success behavior unchanged.
- Add tests for responses with and without an error code.

Inspect the existing API error handling first. Implement the smallest
centralized solution rather than modifying every page separately.
Run the relevant frontend tests and type checking.
```

This prompt contains enough information to guide the work without prescribing every implementation detail.

---

# 9. Debugging Problems

A useful debugging prompt includes:

* expected behavior;
* actual behavior;
* reproduction steps;
* exact errors;
* relevant environment information;
* what has already been attempted.

## Debugging template

````text
Investigate the following problem.

## Expected behavior

[What should happen.]

## Actual behavior

[What happens instead.]

## Reproduction steps

1. ...
2. ...
3. ...

## Error output

```text
[Paste the full error.]
````

## Environment

* Operating system:
* Runtime and version:
* Framework:
* Database:
* Branch or commit:
* Relevant configuration:

## Already attempted

* ...
* ...

First reproduce or trace the problem.
Do not immediately apply a speculative fix.

Identify:

1. the root cause;
2. evidence supporting that conclusion;
3. the smallest appropriate fix;
4. possible side effects;
5. the regression test that should be added.

Then implement and verify the fix.

````

---

## Ask for hypotheses

```text
Generate several plausible causes for this failure.

Rank them from most to least likely.

For each hypothesis, provide:
- evidence supporting it;
- evidence against it;
- the quickest way to test it.

Investigate the highest-probability causes first.
Do not modify code until the cause is supported by evidence.
````

---

## Ask Codex to reduce the problem

```text
Create the smallest reproduction of this bug.

Determine whether the failure comes from:
- application logic;
- configuration;
- dependency versions;
- environment;
- database state;
- test setup.

Do not hide the problem by weakening validation or removing the failing test.
```

---

## Avoid symptom fixes

Add this instruction when necessary:

```text
Do not fix only the visible symptom.

Trace the data and control flow until you identify where the invalid state
first enters the system.
```

---

# 10. Testing and Verification

A coding agent becomes much more useful when it can verify its work.

Simon Willison repeatedly emphasizes strong tests and manual verification in his agent-assisted development workflows. He has described asking Codex to manually exercise features even after automated tests pass.

Armin Ronacher similarly argues that a good test suite can be more valuable than the generated implementation because it gives agents a reliable target and feedback loop.

## Basic verification prompt

```text
Verify your implementation.

Run the most relevant:
- unit tests;
- integration tests;
- type checks;
- formatting checks;
- linting;
- build commands.

Do not claim success based only on reading the code.

Report:
1. every command executed;
2. whether it passed or failed;
3. relevant output;
4. checks you could not run;
5. remaining uncertainty.
```

---

## Regression-test prompt

```text
Add a regression test that demonstrates the original bug.

The test should:
1. fail against the old implementation;
2. pass with the fix;
3. test observable behavior rather than internal implementation details;
4. include the important edge case;
5. follow existing test patterns.
```

---

## Manual testing prompt

```text
Automated tests pass. Now manually exercise the feature from the user's
perspective where possible.

Test:
- the normal path;
- empty input;
- invalid input;
- repeated actions;
- refresh or reload behavior;
- backend failure;
- permission failure.

Report exactly what you tested and observed.
```

---

## Test-review prompt

```text
Review the tests, not only the production code.

Check whether:
- important assertions are missing;
- tests can pass for the wrong reason;
- mocks hide real integration problems;
- failure paths are covered;
- tests depend on execution order;
- tests are flaky;
- edge cases are omitted.
```

---

# 11. Reviewing Codex’s Work

Never assume that code is correct merely because it compiles or because Codex says that it is finished.

Review the diff.

## General review prompt

```text
Review the current diff as if it were a pull request submitted by another
developer.

Do not merely summarize it.

Look for:
- correctness bugs;
- incomplete requirements;
- accidental behavior changes;
- missing validation;
- weak error handling;
- security risks;
- race conditions;
- resource leaks;
- performance regressions;
- unnecessary complexity;
- duplicated code;
- inconsistent naming;
- missing tests;
- misleading comments;
- unrelated changes.

Rank findings by severity.

For each finding provide:
- file and location;
- why it is a problem;
- a concrete failure scenario;
- the recommended fix.

Do not invent issues without evidence.
```

---

## Security review prompt

Use only on systems you are authorized to inspect.

```text
Perform a defensive security review of the current changes.

Focus on:
- authentication;
- authorization;
- input validation;
- injection;
- sensitive-data exposure;
- insecure logging;
- path handling;
- file uploads;
- secret handling;
- cryptographic misuse;
- unsafe deserialization;
- dependency risks;
- denial-of-service opportunities.

For every finding:
- show the relevant code;
- explain how it could be reached;
- rate the severity;
- propose a safe remediation;
- recommend a test.

Do not perform attacks against external systems.
```

---

## Simpler beginner review

```text
Explain this diff to me like I am a junior developer.

For every changed file tell me:
- what changed;
- why it changed;
- what could go wrong;
- how it was tested;
- what I should inspect manually.
```

---

# 12. Using Codex to Learn Programming

Do not let Codex silently complete everything.

Use it as a teacher.

## Explanation levels

```text
Explain this code in three levels:

1. Beginner: what it does in plain English.
2. Intermediate: how the classes and functions cooperate.
3. Technical: important framework behavior, runtime details, and trade-offs.
```

---

## Line-by-line explanation

```text
Explain this function line by line.

For each section describe:
- what it does;
- why it is necessary;
- input and output;
- state changes;
- possible errors;
- a simpler alternative if one exists.
```

Do not use line-by-line explanations for an entire large repository. Use them for small, important sections.

---

## Predict before running

```text
Do not run this code yet.

Ask me to predict:
- its output;
- which branch executes;
- whether an exception occurs;
- how the state changes.

After I answer, evaluate my reasoning and then run the code to verify it.
```

---

## Learning mode

```text
I am learning, so do not immediately give me the complete solution.

First:
1. explain the underlying concept;
2. show me where the problem is located;
3. give me one useful hint;
4. let me attempt the fix.

Only provide the complete implementation when I explicitly ask.
```

---

## Generate exercises from a real project

```text
Based on this repository, create five practical exercises for me.

Start with an easy code-reading exercise and increase the difficulty.

For each exercise include:
- objective;
- relevant files;
- concepts practiced;
- acceptance criteria;
- optional hint.

Do not provide the solutions yet.
```

---

## Learn from Codex’s changes

After Codex implements something, ask:

```text
Teach me the change you just made.

Explain:
- the original problem;
- the root cause;
- the chosen solution;
- alternatives considered;
- every important changed file;
- the tests;
- what I should remember for similar problems.
```

---

# 13. Using `AGENTS.md`

`AGENTS.md` is a repository-level instruction file for coding agents.

It can tell Codex:

* how to build the project;
* how to test it;
* which code style to follow;
* which files not to edit;
* how the repository is structured;
* what completion means;
* project-specific rules.

Repository context files such as `AGENTS.md` have become an important configuration mechanism across agentic coding tools. A 2026 study of thousands of repositories found context files to be the most widely adopted configuration approach and identified `AGENTS.md` as an emerging interoperable convention.

## Example `AGENTS.md`

````markdown
# AGENTS.md

## Project overview

This repository contains a Spring Boot backend for an examination-planning
application.

## Technology

- Java 21
- Spring Boot
- Maven
- PostgreSQL
- Flyway
- JUnit 5
- Testcontainers

## Important directories

- `src/main/java`: application source code
- `src/main/resources/db/migration`: Flyway migrations
- `src/test/java`: automated tests
- `docs`: project documentation

## Build and test commands

```bash
./mvnw test
./mvnw verify
./mvnw spotless:check
````

## Coding rules

* Follow existing package boundaries.
* Keep controllers thin.
* Put business logic in services.
* Access the database through repositories.
* Use constructor injection.
* Do not return JPA entities directly from controllers.
* Add tests for bug fixes and new behavior.
* Prefer existing utilities over introducing new helpers.

## Database rules

* Do not edit an existing migration after it has been merged.
* Create a new migration for schema changes.
* PostgreSQL is the production database.
* Do not rely on H2-specific behavior.

## Security

* Never print secrets or personal data.
* Preserve authorization checks.
* Validate external input.
* Do not weaken security configuration to make tests pass.

## Scope control

* Do not perform unrelated refactoring.
* Do not update dependencies unless required by the task.
* Do not modify generated files.
* Ask before changing public APIs or database schemas.

## Completion requirements

Before declaring a task complete:

1. Run the relevant tests.
2. Run formatting checks.
3. Review the diff.
4. Report any checks that could not be executed.

````

---

## Ask Codex to draft `AGENTS.md`

```text
Inspect this repository and draft an AGENTS.md file.

Base it only on evidence found in the repository.

Include:
- project purpose;
- architecture;
- important directories;
- setup commands;
- build commands;
- test commands;
- formatting and linting;
- coding conventions;
- database rules;
- security rules;
- files that should not be edited;
- definition of done.

Clearly mark anything that requires confirmation.
Do not invent commands.
````

Review the result manually. A wrong command in `AGENTS.md` can repeatedly mislead the agent.

---

## What not to put in `AGENTS.md`

Avoid:

* a complete history of the project;
* enormous architecture essays;
* temporary task instructions;
* rules that Codex can infer reliably from tooling;
* contradictory requirements;
* commands that no longer work;
* secrets or credentials.

Keep durable repository rules in `AGENTS.md`. Put task-specific requirements in the current prompt.

---

# 14. Managing Context

Codex performs better when it receives relevant context, not simply more context.

## Useful context

* relevant file paths;
* exact error messages;
* reproduction commands;
* API examples;
* acceptance criteria;
* architecture rules;
* test commands;
* the current diff;
* links to local documentation;
* screenshots related to the task.

## Unhelpful context

* entire logs when only five lines matter;
* unrelated files;
* old requirements;
* contradictory instructions;
* huge copied documents without explaining what matters;
* several different tasks in one prompt.

---

## Point Codex toward likely locations

```text
The issue is probably related to:
- UserController.java
- UserService.java
- UserRepository.java

Inspect those files first, but follow references elsewhere if the evidence
indicates the problem is in another area.
```

Do not restrict Codex so strongly that it cannot follow the code.

---

## Ask it to build a context map

```text
Before modifying anything, identify the minimum set of files needed to
understand this task.

For each file explain why it is relevant.

Then inspect those files and revise the set if necessary.
```

---

## Start a new session when appropriate

Consider starting a fresh session when:

* the task has completely changed;
* the conversation contains many obsolete assumptions;
* Codex repeatedly returns to an incorrect idea;
* the previous work produced excessive context;
* you want an independent review;
* you need a clean investigation.

Do not continue one enormous session forever merely because it already exists.

---

# 15. Working With Large Tasks

Do not give Codex an entire large application as one vague request.

Weak:

```text
Build a complete social network.
```

Better:

```text
Create a plan for a minimal social application with:

- local authentication;
- user profiles;
- text posts;
- a chronological feed;
- PostgreSQL;
- a React frontend;
- a Spring Boot backend.

Do not implement yet.

Break the project into milestones. For each milestone include:
- behavior delivered;
- backend work;
- frontend work;
- database work;
- tests;
- dependencies;
- risks;
- completion criteria.
```

Then implement one milestone at a time.

---

## Use vertical slices

A vertical slice delivers one complete behavior across all necessary layers.

Example:

```text
Milestone 1: Create a post

- database table;
- backend entity;
- repository;
- service;
- POST endpoint;
- frontend form;
- API integration;
- validation;
- automated tests.
```

This is usually easier to verify than building all database models first, then all endpoints, then all frontend pages.

---

## Keep a task file

For substantial work, create a document such as:

```text
docs/tasks/email-verification.md
```

Possible structure:

```markdown
# Email Verification

## Goal

## Current behavior

## Requirements

## Non-goals

## Proposed design

## Implementation steps

## Edge cases

## Security considerations

## Test plan

## Progress

## Open questions

## Final verification
```

Ask Codex to update the progress section as work proceeds.

---

## Stop conditions

Give Codex explicit reasons to stop.

```text
Stop and report before proceeding if:

- the database schema must change;
- a public API must be broken;
- a new paid service is required;
- credentials are missing;
- production resources would be modified;
- the requirements conflict;
- the relevant tests cannot be executed;
- the task is substantially larger than described.
```

---

# 16. Safe and Responsible Usage

Codex can execute commands and modify files, so you should maintain normal engineering safeguards.

## Use version control

Before a significant task:

```bash
git status
git switch -c feature/descriptive-name
```

Make sure important work is committed or backed up.

After Codex works:

```bash
git status
git diff
```

Never rely only on Codex’s summary. Inspect the actual changes.

---

## Avoid exposing secrets

Do not paste:

* passwords;
* API keys;
* private keys;
* database credentials;
* production tokens;
* personal data;
* confidential customer information.

Keep secrets in appropriate environment or secret-management systems.

---

## Be careful with destructive commands

Commands requiring extra caution include:

```bash
rm -rf
git reset --hard
git clean -fd
DROP DATABASE
TRUNCATE TABLE
docker system prune
kubectl delete
terraform destroy
```

A useful instruction is:

```text
Do not execute destructive commands.

Do not delete files, reset Git history, remove containers or volumes,
modify production systems, or destroy data without explicit approval.
```

---

## Restrict the scope

```text
You may read the entire repository.

You may modify files under:
- src/
- tests/

Do not modify:
- deployment/
- infrastructure/
- database migrations/
- lock files;
- CI configuration.

Stop and explain if the task requires one of those changes.
```

---

## Review dependency changes

When Codex proposes a dependency, ask:

```text
Explain why this dependency is needed.

Check whether the repository already contains functionality that solves
the problem.

Compare:
- implementing the small behavior locally;
- using the proposed dependency.

Consider:
- maintenance;
- security;
- package size;
- licensing;
- compatibility;
- long-term support.
```

---

# 17. Common Prompting Mistakes

## Mistake 1: Being too vague

```text
Fix the code.
```

Better:

```text
The user-creation endpoint returns HTTP 500 when the email already exists.
It should return HTTP 409 with the standard error response.

Reproduce the problem, identify the root cause, add a regression test,
implement the smallest fix, and run the backend tests.
```

---

## Mistake 2: Combining unrelated work

```text
Fix login, redesign the dashboard, update all dependencies, improve security,
write documentation, and deploy the application.
```

Separate this into multiple tasks.

Each task should have one primary objective.

---

## Mistake 3: Prescribing an unverified solution

```text
Fix the bug by changing the database column to TEXT.
```

You may be wrong.

Better:

```text
The value is being truncated at 255 characters.

Investigate where the limit originates and recommend the safest fix.
Do not assume that changing the database type is the only solution.
```

---

## Mistake 4: Providing no completion criteria

```text
Improve error handling.
```

Better:

```text
Replace generic HTTP 500 responses for validation failures with the
project's standard error format.

Completion criteria:
- invalid input returns HTTP 400;
- the response contains code, message, status, and timestamp;
- unexpected failures still return HTTP 500;
- no stack trace is exposed;
- tests cover both cases.
```

---

## Mistake 5: Trusting the summary

Codex may say:

```text
All tests pass.
```

Check:

* Which tests?
* Which command?
* Did the command actually run?
* Were some tests skipped?
* Was the environment incomplete?

Ask for command-level evidence.

---

## Mistake 6: Asking for excessive explanation before every tiny action

For a small, low-risk edit, this is unnecessary:

```text
Before changing each line, explain why, ask permission, show three alternatives,
and wait.
```

Match the process to the task.

Use more planning for broad or risky tasks and less ceremony for small, obvious tasks.

---

## Mistake 7: Asking Codex to hide failures

Never instruct Codex to:

* remove a failing test without justification;
* disable type checking;
* weaken validation;
* ignore exceptions;
* catch every error silently;
* hard-code a result to satisfy a test;
* skip security checks;
* declare success without running the project.

Use:

```text
Do not make the check pass by weakening or deleting it.
Fix the underlying problem.
```

---

## Mistake 8: Requesting giant rewrites unnecessarily

```text
Rewrite the entire backend using a cleaner architecture.
```

Large rewrites create risk and are difficult to verify.

Prefer:

```text
Identify the most problematic architectural boundary and propose an
incremental improvement that preserves current behavior.
```

---

# 18. Reusable Prompt Library

## 18.1 Understand a repository

```text
Inspect this repository as if you were onboarding a new developer.

Explain:
- what the application does;
- technologies used;
- folder structure;
- main entry points;
- architecture;
- data flow;
- database access;
- external services;
- testing;
- build and run commands;
- risky areas.

Reference relevant file paths.
Do not edit anything.
```

---

## 18.2 Find important files

```text
Identify the 15 most important files for understanding this repository.

Rank them in the order I should read them.

For each file explain:
- its purpose;
- what calls it;
- what it calls;
- the concepts I need to understand.
```

---

## 18.3 Explain one feature

```text
Trace the complete implementation of [feature].

Follow the behavior from the user interface or external request through
every relevant layer and back to the result.

List files and important functions in execution order.
Do not edit anything.
```

---

## 18.4 Plan a feature

```text
Create an implementation plan for:

[Feature description]

Inspect the repository first.

Include:
- current behavior;
- relevant files;
- proposed design;
- data changes;
- API changes;
- user-interface changes;
- validation;
- error handling;
- security;
- edge cases;
- tests;
- risks;
- open questions.

Do not edit files yet.
```

---

## 18.5 Implement an approved plan

```text
Implement the plan described in [path or previous message].

Keep the work limited to the approved scope.

Requirements:
- follow existing patterns;
- preserve unrelated behavior;
- avoid unnecessary dependencies;
- add appropriate tests;
- run validation commands;
- review the final diff.

Stop and report if the plan requires a major unapproved change.
```

---

## 18.6 Fix a bug

```text
Fix this bug:

Expected:
[Expected behavior]

Actual:
[Actual behavior]

Reproduction:
[Steps or command]

Error:
[Exact error]

First identify and explain the root cause using evidence.

Then:
- add a regression test;
- implement the smallest maintainable fix;
- run relevant checks;
- review for side effects;
- summarize the result.
```

---

## 18.7 Review code

```text
Review the current uncommitted diff.

Focus on concrete, actionable problems involving:
- correctness;
- security;
- missing requirements;
- error handling;
- edge cases;
- performance;
- maintainability;
- tests.

Rank findings by severity and cite exact locations.
Do not praise or summarize unless it helps explain a finding.
```

---

## 18.8 Improve tests

```text
Review the test coverage for [feature or module].

Identify:
- untested behavior;
- weak assertions;
- false-positive tests;
- excessive mocking;
- missing failure cases;
- missing boundary cases;
- flaky behavior.

Propose the smallest high-value set of additional tests.
Then implement them.
```

---

## 18.9 Explain an error

```text
Explain this error to me as a junior developer:

[Error]

Cover:
1. literal meaning;
2. likely cause in this project;
3. how to investigate it;
4. common incorrect fixes;
5. the proper fix;
6. how to prevent it in the future.
```

---

## 18.10 Refactor safely

```text
Refactor [component] without changing observable behavior.

Before editing:
- explain the current responsibilities;
- identify the specific maintainability problem;
- describe the intended structure;
- identify existing tests that protect behavior.

During the refactor:
- use small steps;
- avoid unrelated changes;
- preserve public interfaces where possible;
- run tests after meaningful steps.

At the end:
- review the diff;
- explain how behavior preservation was verified.
```

---

## 18.11 Generate documentation

```text
Create developer documentation for [feature or module].

Base it on the current implementation.

Include:
- purpose;
- architecture;
- important files;
- data flow;
- configuration;
- normal behavior;
- failure behavior;
- examples;
- testing;
- troubleshooting;
- known limitations.

Use clear Markdown.
Do not claim behavior that is not supported by the code.
```

---

## 18.12 Prepare a pull request

```text
Review the changes on this branch and prepare a pull-request description.

Include:
- problem;
- solution;
- important implementation decisions;
- changed behavior;
- tests performed;
- screenshots or manual checks needed;
- risks;
- follow-up work.

Do not exaggerate or claim tests that were not run.
```

---

## 18.13 Learn without receiving the solution

```text
Act as my programming tutor.

Help me solve [problem], but do not provide the complete answer immediately.

Use this sequence:
1. ask me what I understand;
2. explain the relevant concept;
3. point me to the relevant file or function;
4. give one hint;
5. review my attempt;
6. provide a stronger hint if necessary;
7. reveal the complete solution only when I request it.
```

---

## 18.14 Create an IT troubleshooting guide

```text
Create a practical troubleshooting guide for [printer/network/account/software].

Structure it as:

1. Symptoms
2. Questions to ask the user
3. Quick checks
4. Likely causes
5. Step-by-step diagnosis
6. Safe fixes
7. Verification
8. When to escalate
9. Information to include in the ticket
10. Actions that should not be taken

Write it for a new first-level IT support employee.
```

---

# 19. A Complete Example Workflow

Assume you join a project and receive this task:

> “Show backend error codes in the frontend.”

Do not begin with:

```text
Show error codes.
```

Use the following workflow.

---

## Step 1: Understand current error handling

```text
Inspect how backend errors are represented and how the frontend handles
failed API requests.

Trace one representative failure from:
- backend exception;
- error response;
- frontend API client;
- frontend state;
- visible notification.

Identify whether error handling is centralized or duplicated.

Do not edit anything.
```

---

## Step 2: Ask for a plan

```text
Create a plan to display the backend error code in frontend error messages.

Desired output:

The requested resource was not found.
Error code: PT-404-001

Requirements:
- retain the human-readable message;
- handle missing codes gracefully;
- do not display stack traces;
- avoid editing every page separately;
- preserve current behavior for nonstandard errors;
- add tests.

List the files that need to change and explain why.
Do not edit yet.
```

---

## Step 3: Implement

```text
Implement the plan.

Prefer the existing centralized error-handling mechanism.

Keep the change limited to error display and its tests.
Do not refactor unrelated API code.
```

---

## Step 4: Verify

```text
Run the relevant frontend tests, type checking, formatting, and build.

Test these cases:
1. standard backend error with code;
2. backend error without code;
3. network failure without a backend response;
4. unexpected response shape.

Report the commands and results.
```

---

## Step 5: Review

```text
Review the final diff.

Check specifically for:
- unsafe assumptions about the error object;
- showing undefined or null to the user;
- leaking internal details;
- duplicated formatting;
- changed behavior on successful requests;
- insufficient tests.

Fix confirmed issues and rerun checks.
```

---

## Step 6: Learn

```text
Explain the final implementation to me.

Show:
- where the error enters the frontend;
- where it is normalized;
- where the display text is created;
- why the solution is centralized;
- how each test protects the behavior.
```

That is professional agent usage: not one magical prompt, but a controlled engineering process.

---

# 20. A Personal Practice Plan

You do not become good at prompting by memorizing hundreds of templates.

You improve by repeatedly completing the same cycle.

## Week 1: Repository reading

Every day, use a small repository and ask Codex to:

* explain the structure;
* identify entry points;
* trace one feature;
* explain one important file;
* create a glossary.

Your goal is to compare Codex’s explanation with the actual files.

---

## Week 2: Small changes

Practice tasks such as:

* rename a confusing variable;
* improve one error message;
* add validation;
* add one test;
* fix one small bug;
* update one documentation section.

For every task use:

```text
Understand → Change → Test → Review
```

---

## Week 3: Debugging

Create or find small failures:

* null value;
* invalid input;
* incorrect API path;
* missing environment variable;
* failing database query;
* broken frontend state.

Ask Codex to generate hypotheses and gather evidence before modifying code.

---

## Week 4: Feature work

Implement one complete small feature.

Require:

* plan;
* acceptance criteria;
* tests;
* implementation;
* manual verification;
* self-review;
* documentation.

---

## Keep a prompt journal

Create:

```text
codex-prompt-journal.md
```

Use this format:

```markdown
## Task

What I wanted to accomplish.

## Original prompt

The exact prompt I used.

## Result

What Codex did well and badly.

## Missing information

What I should have included.

## Improved prompt

A better version.

## Lesson

What I will do next time.
```

After 20–30 tasks, your own journal will be more useful than a generic prompt collection.

---

# 21. Recommended Guides and People

## Official OpenAI Codex resources

### Codex best practices

The official best-practices guide covers prompting, planning, validation, tool use, and habits that apply across the CLI, IDE extension, and Codex app. It should be your primary reference.

### Codex prompting guide

OpenAI provides a dedicated Codex prompting guide with guidance specifically designed for coding models and agentic coding tasks.

### Codex prompting documentation

The prompting documentation explains how to provide useful context, including relevant files, images, and repository information.

### Codex CLI documentation

The CLI documentation covers interactive usage, models, reasoning levels, commands, configuration, and terminal workflows.

### Codex use cases

OpenAI maintains a collection of real use cases covering software engineering, quality assurance, security review, data work, testing, and automation.

---

## Simon Willison

Simon Willison is a software developer and writer who documents his practical use of coding agents in detail.

Important ideas from his work include:

* use agents for code research, not only implementation;
* run multiple independent agents when useful;
* maintain comprehensive automated tests;
* manually verify behavior beyond automated tests;
* ask independent agents to perform code review;
* use subagents to keep noisy tasks out of the main context;
* inspect the actual code rather than trusting the generated summary.

He has publicly described using Codex CLI and Codex cloud workflows as part of his daily development process.

His writing on subagents explains how fresh agent contexts can be used for code review, test execution, investigation, and other focused tasks.

---

## Armin Ronacher

Armin Ronacher is the creator of Flask and a highly experienced open-source developer. He has written extensively about agentic coding workflows.

Important ideas from his work include:

* assign agents complete, well-defined jobs;
* build projects that are easy to test;
* prefer clear architecture and predictable tooling;
* use tests as the feedback mechanism;
* avoid excessive interruption during well-defined tasks;
* inspect final work manually;
* be cautious with dependency upgrades and large automated changes;
* recognize that some programming-language features and highly dynamic behavior are difficult for agents;
* use deterministic tools when they are more appropriate than language-model reasoning.

His “Agentic Coding Recommendations” article describes a workflow based on assigning full tasks to agents and using the editor mainly for final inspection and polish.

His later writing emphasizes the importance of repeatable validation loops and strong test suites.

He has also documented approaches that did not work well, including postponing tests until the end of development.

---

# 22. Final Checklist

Before sending a task to Codex, ask yourself:

## Goal

* Do I know what observable result I want?
* Did I describe expected behavior?
* Did I separate this from unrelated tasks?

## Context

* Did I include the relevant file, component, command, or user flow?
* Did I include exact errors and reproduction steps?
* Did I distinguish facts from assumptions?

## Constraints

* Did I say what must remain unchanged?
* Did I define whether dependencies, APIs, schemas, or infrastructure may change?
* Did I identify dangerous or out-of-scope actions?

## Process

* Should Codex investigate, plan, implement, or only explain?
* Does it need to stop before risky changes?
* Is the task small enough, or should it be split into milestones?

## Verification

* What command should pass?
* What user behavior should work?
* Which regression test is required?
* Is manual testing necessary?
* What evidence should Codex report?

## Review

* Will I inspect `git diff`?
* Will I read the important changed files?
* Will I verify that tests actually ran?
* Will I ask for an independent review?
* Do I understand the final implementation?

---

# The Main Rule

A good Codex prompt does not need to sound intelligent.

It needs to make the work inspectable.

Use this formula:

```text
Here is the desired behavior.

Here is what happens now.

Here is the evidence.

Here are the important constraints.

First understand the existing implementation.

Then make the smallest appropriate change.

Verify it with these checks.

Review the result and report remaining uncertainty.
```

The professional skill is not producing one perfect prompt.

The professional skill is repeatedly guiding Codex through:

```text
Understand
→ Plan
→ Implement
→ Verify
→ Review
→ Learn
```
