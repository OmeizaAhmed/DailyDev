# CI vs CD: Passing Tests Is Only Part of Shipping Software

Your code builds. The tests pass. But releasing it still involves copying files, changing settings, and hoping you remembered every step.

That gap is what CI and CD help address.

They work together, but they handle different parts of getting code into users’ hands.

## CI: Integrate Changes and Catch Problems Early

**Continuous Integration (CI)** means developers frequently merge small changes into a shared codebase, with automated checks validating those changes.

A typical CI pipeline:

- Builds the application.
- Runs automated tests.
- Checks code quality.

Imagine two developers working on an ASP.NET application. One changes an API response; the other updates code that depends on it.

Their changes may work separately but fail when combined. CI helps catch that conflict early, while the changes are still small enough to investigate easily.

CI gives feedback about whether changes work together. Its confidence depends on the quality of the checks—you can still ship bugs with a green pipeline.

## CD: Make Releases Repeatable

**CD can mean Continuous Delivery or Continuous Deployment.** This distinction matters.

### Continuous Delivery

Changes that pass the pipeline are kept ready for release. The pipeline automates the steps needed to prepare and validate a release, but production deployment still requires a deliberate approval or action.

For example, a release is deployed to staging automatically. After reviewing it, someone approves deployment to production.

### Continuous Deployment

Every change that passes the required checks is automatically deployed to production.

There is no routine manual approval between successful validation and deployment.

| Practice               | Main purpose                             | Production deployment          |
| ---------------------- | ---------------------------------------- | ------------------------------ |
| Continuous Integration | Validate frequently integrated changes   | Not required                   |
| Continuous Delivery    | Keep validated changes ready for release | Requires a deliberate decision |
| Continuous Deployment  | Release validated changes automatically  | Happens automatically          |

## A Practical Example

Suppose you add pagination to an ASP.NET API.

Your pipeline builds the application, runs tests, packages it, and deploys it to staging.

The build and integration checks support **CI**.

If the validated release waits for production approval, you have **Continuous Delivery**.

If it automatically goes to production after passing all required checks, you have **Continuous Deployment**.

The difference is the release decision, not whether you use GitHub Actions, Jenkins, or another tool.

## Which Approach Should You Use?

Start with reliable CI checks and a repeatable deployment process.

Choose Continuous Delivery when releases need deliberate approval, coordination, or scheduled timing.

Choose Continuous Deployment when your testing, monitoring, and recovery processes support automatically releasing small changes.

Automation increases release speed, but weak checks can also let mistakes reach production faster.

## Key Takeaway

**CI validates integrated changes. Continuous Delivery keeps them ready to release. Continuous Deployment releases them automatically.**

Build confidence in your checks before removing the final approval step.
