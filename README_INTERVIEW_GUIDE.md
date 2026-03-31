# Playwright Concepts And Interview Guide

This document explains the main Playwright and automation concepts used in this repository in plain English.

## Why This File Exists

The main [README.md](/c:/Users/asafn/Desktop/NICE-Project/README.md) explains how to run the project.
This file explains why the framework is built this way, what the key terms mean, and how to talk about them in an interview.

## `await test.step(...)`

### What It Is

`test.step(...)` is a Playwright feature for breaking a test into named business steps.

Example from this repository:

- [core-banking.spec.ts](/c:/Users/asafn/Desktop/NICE-Project/tests/e2e/core-banking.spec.ts)

### Why It Exists

- It makes the report easier to read.
- It shows exactly which business stage failed.
- It keeps large tests organized.
- It allows one step to return data that the next step needs.

### Why `await` Is Used

The step body is asynchronous.
If the step is not awaited:

- Playwright will not wait for it to finish before moving on.
- returned values from the step cannot be used safely.
- the test flow becomes harder to reason about.

### Short Interview Answer

`await test.step(...)` is used to split a long business flow into readable stages, improve reporting, and keep async execution ordered.

## Fixture

### What It Is

A fixture is reusable test setup that Playwright injects into a test.

Built-in examples:

- `page`
- `request`

Custom examples in this repository:

- [bank.fixture.ts](/c:/Users/asafn/Desktop/NICE-Project/tests/fixtures/bank.fixture.ts)

This file provides reusable objects such as:

- `user`
- `registeredUser`
- `customer`
- `defaultAccount`
- page objects
- API client

### Why Fixtures Are Used In Automation

- to remove duplicated setup code
- to keep tests shorter
- to standardize setup across the suite
- to make scaling easier when the project grows

### Short Interview Answer

A fixture is shared setup injected into tests. It helps reduce duplication and keeps tests focused on the scenario instead of setup boilerplate.

## `test.describe` Vs `test.describe.serial`

### `test.describe`

`test.describe(...)` is a grouping mechanism.

It is used to:

- group related tests
- apply hooks such as `beforeEach`
- make reports easier to read
- apply tags or shared structure

It does not automatically force tests to depend on each other.

### `test.describe.serial`

`test.describe.serial(...)` means the tests inside that block must run in order and are treated as dependent.

This is usually used when:

- the tests share state
- one test depends on the result of the previous one
- the flow cannot be safely isolated

### Why Serial Should Be Used Carefully

Serial tests are less scalable because:

- they reduce parallelism
- a failure in an earlier test can affect the rest
- they usually indicate shared state or coupling

### Short Interview Answer

`test.describe` is only for grouping. `test.describe.serial` is for ordered dependent tests. In a strong framework, serial mode should be the exception, not the default.

## Why Not Use Only `test(...)`

You can technically write everything with only `test(...)`, but the suite becomes harder to maintain.

`test.describe`, fixtures, hooks, tags, page objects, and `test.step` exist to solve scaling problems:

- repeated setup
- poor readability
- weak reporting
- hard debugging
- difficult ownership
- poor suite organization

### Short Interview Answer

I do not use only `test(...)` because a real automation framework needs structure. Grouping, fixtures, page objects, and steps make the suite maintainable as it grows.

## Core Terminology

### Playwright

What:
- a browser automation and test framework

Why:
- used to automate UI flows, assertions, tracing, screenshots, videos, and reporting

### TypeScript

What:
- JavaScript with static typing

Why:
- helps catch mistakes earlier and makes the code easier to maintain

### UI Test

What:
- a test that validates user behavior through the web interface

Why:
- proves the real user journey works

### API Test

What:
- a test that validates backend behavior directly through HTTP calls

Why:
- faster than UI, better for data validation and backend checks

### `curl`

What:
- a command-line tool for sending HTTP requests

Why:
- the assignment explicitly required using `curl` for account creation
- it also proves direct service interaction outside the UI

Where in this repository:

- [curl.ts](/c:/Users/asafn/Desktop/NICE-Project/src/utils/curl.ts)
- used by [core-banking.spec.ts](/c:/Users/asafn/Desktop/NICE-Project/tests/e2e/core-banking.spec.ts)

### Swagger / OpenAPI

What:
- machine-readable API documentation

Why:
- defines endpoints, schemas, and request/response models
- supports contract checks and API understanding

Where in this repository:

- [parabank.openapi.json](/c:/Users/asafn/Desktop/NICE-Project/openapi/parabank.openapi.json)
- [openapi.contract.ts](/c:/Users/asafn/Desktop/NICE-Project/src/contracts/openapi.contract.ts)
- [openapi.contract.spec.ts](/c:/Users/asafn/Desktop/NICE-Project/tests/contract/openapi.contract.spec.ts)

### Page Object Model

What:
- a design pattern that wraps UI interactions inside page classes

Why:
- keeps selectors and actions out of test files
- makes UI code reusable and easier to update

### Assertion

What:
- a check that proves expected behavior happened

Why:
- tests are meaningful only when they validate a result, not just perform actions

### Smoke Test

What:
- a short critical-path test

Why:
- used to quickly confirm the system is alive after a change

### Regression Test

What:
- broader coverage to check that existing functionality still works

Why:
- used after changes, merges, and release candidates

### Negative Test

What:
- a test for invalid input or failing behavior

Why:
- proves the system handles bad scenarios correctly

### Contract Test

What:
- a test that checks whether an API still follows the expected documented contract

Why:
- catches mismatches between implementation and documentation

### Fixture

What:
- reusable injected setup

Why:
- reduces duplication and standardizes test preparation

### Trace

What:
- Playwright execution artifact that records steps, actions, DOM, and timing

Why:
- useful for failure analysis

### Reporter

What:
- test output format such as HTML, JSON, or JUnit

Why:
- needed for humans, CI systems, and dashboards

### Docker

What:
- containerized execution environment

Why:
- makes test execution more reproducible across machines

### Jenkins

What:
- CI/CD orchestration system

Why:
- runs suites automatically, stores artifacts, and standardizes automation pipelines

### Parallel Execution

What:
- running multiple tests at the same time

Why:
- reduces runtime
- must be controlled carefully in shared or stateful environments

### Matrix Execution

What:
- running the same suite across multiple dimensions such as browsers or environments

Why:
- increases coverage without changing test logic

### Hook

What:
- setup or cleanup code such as `beforeEach`, `afterEach`, `beforeAll`, `afterAll`

Why:
- used to avoid repeating the same preparation or cleanup logic in each test

### Worker

What:
- a Playwright process that runs tests

Why:
- workers are the mechanism behind parallel execution

### Retry

What:
- a configured second attempt for a failed test

Why:
- useful for collecting traces and separating transient environment noise from real failures

### Tag

What:
- a label such as `@smoke`, `@api`, or `@parallel`

Why:
- used to slice suites and control which tests run in each lane

## Questions And Answers

### Why did you use `await test.step(...)`?

Because the end-to-end flow is a business journey, not just a flat list of actions. `test.step` makes the report readable and isolates failures by stage.

### Why did you add fixtures?

To avoid repeating the same setup in every test file and to make the suite easier to grow.

### Why not keep everything in one file?

Because that creates duplication and makes the framework harder to maintain. UI, API, assertions, fixtures, and config have different responsibilities.

### Why use UI and API together?

UI validates the user journey. API validates data and backend state more directly and more efficiently.

### Why use `curl` if you already have an API client?

Because the assignment explicitly required the account creation step through `curl`. I implemented that as a real command execution and still validated the result through the framework.

### Why not make the whole suite parallel?

Because the public ParaBank environment is shared. Write-heavy flows such as registration and account creation are more likely to become flaky under concurrent load.

### Why check in an OpenAPI file locally?

So contract tests do not depend on the live Swagger UI being available at runtime.

### Why use Docker if the suite already runs locally?

Docker gives a repeatable environment. It reduces machine-specific differences and is useful in CI.

### Why add Jenkins if GitHub Actions already exists?

Because many organizations still use Jenkins. The project is now prepared for both paths.

### Why not use only exact UI text everywhere?

Because public shared environments sometimes change wording. Assertions should be stable enough to validate behavior without becoming brittle.

## How An Enterprise Lead Would Approach This

An enterprise lead would not only ask whether the tests pass. They would ask whether the framework can be owned, scaled, debugged, and trusted by multiple teams.

### Main Concerns

- maintainability
- execution speed
- test isolation
- flaky-test control
- CI pipeline design
- environment strategy
- reporting and observability
- ownership model
- onboarding of new engineers

### What They Would Add Or Strengthen

- stronger domain-based test organization
- clearer suite taxonomy such as smoke, sanity, regression, API, contract
- fixture libraries for authenticated and seeded states
- environment-specific configuration and secrets management
- dashboarding for pass rate, flaky rate, and duration trends
- quarantine process for unstable tests
- more contract coverage
- multi-browser and multi-environment matrix lanes
- data management strategy for setup and cleanup

### How They Would Talk About Parallelism

A lead would not say "make everything parallel."
They would say:

- isolate read-only tests
- isolate stateful tests
- parallelize only the safe subset
- keep business-critical write-heavy flows stable first

### Short Enterprise-Level Answer

To scale this framework, I would treat it as a test platform: shared fixtures, domain modules, layered CI, contract coverage, controlled parallelism, environment management, and dashboards for quality signals.

## Enterprise Interview Questions

### What would you ask a candidate or team lead about this framework?

- How do you decide which tests belong in smoke, regression, or contract lanes?
- How do you prevent flaky tests from polluting the main signal?
- How would you scale this suite across multiple products or teams?
- How would you manage test data in shared environments?
- What would you run on pull request versus nightly?
- How would you decide what is safe to parallelize?
- How would you measure whether the framework is healthy?
- How would you handle browser, environment, and dependency version drift?
- When should API checks replace UI checks?
- How would you design ownership so this does not become one person's framework?

### What strong answers usually include

- tradeoffs, not only tools
- stability before raw speed
- data strategy
- CI layering
- observability
- maintainability and onboarding
- clear handling of shared-state risk

## Repository Mapping

If someone wants to connect the concepts in this guide to the actual code:

- E2E flow: [core-banking.spec.ts](/c:/Users/asafn/Desktop/NICE-Project/tests/e2e/core-banking.spec.ts)
- Fixtures: [bank.fixture.ts](/c:/Users/asafn/Desktop/NICE-Project/tests/fixtures/bank.fixture.ts)
- API client: [bank-api.client.ts](/c:/Users/asafn/Desktop/NICE-Project/src/api/bank-api.client.ts)
- `curl` helper: [curl.ts](/c:/Users/asafn/Desktop/NICE-Project/src/utils/curl.ts)
- Contract helper: [openapi.contract.ts](/c:/Users/asafn/Desktop/NICE-Project/src/contracts/openapi.contract.ts)
- Contract tests: [openapi.contract.spec.ts](/c:/Users/asafn/Desktop/NICE-Project/tests/contract/openapi.contract.spec.ts)
- Parallel-safe config: [playwright.parallel.config.ts](/c:/Users/asafn/Desktop/NICE-Project/playwright.parallel.config.ts)
- Cross-browser matrix config: [playwright.matrix.config.ts](/c:/Users/asafn/Desktop/NICE-Project/playwright.matrix.config.ts)
- Jenkins pipeline: [Jenkinsfile](/c:/Users/asafn/Desktop/NICE-Project/Jenkinsfile)
- Docker setup: [Dockerfile](/c:/Users/asafn/Desktop/NICE-Project/Dockerfile) and [docker-compose.yml](/c:/Users/asafn/Desktop/NICE-Project/docker-compose.yml)
