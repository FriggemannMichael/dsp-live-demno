# DSO Actions Live Demo

This repository showcases various GitHub Actions examples for getting started with CI/CD. It's set up to serve as a small, public reference project for workflows, triggers, jobs, and deployment scenarios. It also publishes its own landing page live via GitHub Pages: **[friggemannmichael.github.io/gh-actions-workflow](https://friggemannmichael.github.io/gh-actions-workflow/)**.

## Overview

GitHub only runs workflows from files directly inside `.github/workflows/` – subfolders are ignored there. To keep that folder tidy, it only contains three files:

- `starter-ci.yml` – the simplest possible workflow (push → checkout → `echo`)
- `repo-tour.yml` – a small bonus workflow that playfully walks through the repository
- `pages.yml` – builds and deploys the [`public/`](public/) folder to GitHub Pages on every push to `main`

All other examples are pure reading material and live as real subfolders under [`examples/`](examples/), sorted by what they demonstrate:

1. **basics** – additional triggers (`push`, `workflow_dispatch`)
2. **jobs** – dependencies, ordering, variables, outputs, artifacts
3. **ci** – CI pipelines for a real Node.js and Python project
4. **deploy** – real delivery via FTP and SSH with secrets
5. **security** – automated scans: secrets, dependencies, static code analysis, container images

These examples can't be run in this empty repository (they assume a real project or real secrets), but they are complete, commented YAML files meant for reading and reuse in your own projects.

## Table of contents

- [Quick start](#quick-start)
  - [Prerequisites](#prerequisites)
  - [Try Starter CI](#try-starter-ci)
  - [Run Repo Tour manually](#run-repo-tour-manually)
- [Repository structure](#repository-structure)
- [Runnable workflows](#runnable-workflows)
  - [Starter CI](#starter-ci)
  - [Repo Tour](#repo-tour)
  - [Pages deploy](#pages-deploy)
- [Reference examples (`examples/`)](#reference-examples-examples)
  - [Basics](#basics)
    - [Test Runner](#test-runner)
    - [Manual Run Demo](#manual-run-demo)
  - [Jobs & data exchange](#jobs--data-exchange)
    - [Connecting jobs](#connecting-jobs)
    - [Pipeline stage order](#pipeline-stage-order)
    - [Environment variables](#environment-variables)
    - [Passing values between jobs](#passing-values-between-jobs)
    - [Transferring artifacts between jobs](#transferring-artifacts-between-jobs)
  - [CI for real projects](#ci-for-real-projects)
    - [Frontend CI – Node.js](#frontend-ci--nodejs)
    - [Backend CI – Python](#backend-ci--python)
    - [Matrix testing](#matrix-testing)
  - [Deployment](#deployment)
    - [FTP deploy – shipping a static website live](#ftp-deploy--shipping-a-static-website-live)
    - [Angular build & deploy](#angular-build--deploy)
  - [Security](#security)
    - [Secrets scan](#secrets-scan)
    - [Dependency audit](#dependency-audit)
    - [SAST scan](#sast-scan)
    - [Container scan](#container-scan)
- [Contributing](#contributing)
- [Overall goal](#overall-goal)
- [License](#license)

## Quick start

### Prerequisites

- a GitHub account
- this project as a GitHub repository
- GitHub Actions enabled in the repository
- write access to the repository if you want to trigger a workflow or push a change

No packages need to be installed and no application needs to run locally for this demo. The workflows run entirely on GitHub.

### Try Starter CI

`Starter CI` reacts to a push on the `main` branch.

1. Change a file in the repository.
2. Create a commit with the change.
3. Push the commit to `main`.
4. Open the **Actions** tab in the GitHub repository.
5. Select the workflow run to see its jobs, steps, and output.

The workflow checks out the repository onto an Ubuntu runner and then prints the runner's operating system.

### Run Repo Tour manually

`Repo Tour` isn't triggered by a push, but through the GitHub UI:

1. Open the **Actions** tab in the repository.
2. Select **Repo Tour** on the left.
3. Click **Run workflow**.
4. After it runs, open the workflow run and read through the output.

> **Note:** A manually triggered workflow is normally only shown in GitHub once its workflow file exists on the default branch.

## Repository structure

The most important project files are organized as follows:

- `.github/workflows/` – actually runs, flat (GitHub only executes from here)
  - `starter-ci.yml`: Simplest workflow – push, checkout, an `echo` output.
  - `repo-tour.yml`: Walks once through the repository – the best first workflow to try.
  - `pages.yml`: Builds and deploys [`public/`](public/) to GitHub Pages via GitHub's OIDC permissions – no secrets.
- `public/` – the static site published live by `pages.yml`, a single `index.html`.
- `examples/` – for reading and reuse only, real subfolders by category
  - **`basics/`**
    - `test-runner.yml`: Second automatic example workflow, shows that one event can trigger multiple workflows.
    - `manual-run.yml`: Manually triggered workflow with an input for the target environment.
  - **`jobs/`**
    - `dependencies.yml`: Connects two parallel jobs with a shared follow-up job.
    - `ordered-pipeline.yml`: Models a fixed pipeline order with four jobs.
    - `environment-variables.yml`: Shows the scopes of environment variables.
    - `outputs.yml`: Passes a small text value from one job to a follow-up job.
    - `artifacts.yml`: Transfers a file between two jobs.
  - **`ci/`**
    - `frontend.yml`: CI workflow for a Node.js frontend with install, test, and build.
    - `backend.yml`: CI workflow for a Python backend with install, test, and pytest.
    - `matrix.yml`: Runs the same test job in parallel across multiple Node.js versions.
  - **`deploy/`**
    - `ftp.yml`: Real-world example for uploading a static HTML/CSS website via FTP with GitHub secrets.
    - `angular.yml`: Build, test, and deployment of an Angular app via SSH/SCP.
  - **`security/`**
    - `secrets-scan.yml`: Scans the repository for accidentally committed credentials with TruffleHog.
    - `dependency-audit.yml`: Checks Node.js and Python dependencies for known vulnerabilities.
    - `sast.yml`: Scans the project's own source code for insecure patterns with Semgrep.
    - `container-scan.yml`: Builds a Docker image and scans it for vulnerabilities with Trivy.
- `LICENSE`: MIT license – the code may be freely used for learning, copying, and adapting.
- `Readme.md`: Describes the purpose, structure, and usage of the demo.

## Runnable workflows

These three workflows live in `.github/workflows/` and can actually be run in this repository.

### Starter CI

`starter-ci.yml` starts on every push to `main`, on every pull request against `main`, and can also be triggered manually via `workflow_dispatch` in the Actions tab. The `build` job uses `actions/checkout` to load the repository content onto the runner. A shell command then prints the value of the GitHub context variable `runner.os`.

### Repo Tour

`repo-tour.yml` needs no secret, no project, and is guaranteed never to go red: it checks out the repository, prints a header, counts the runnable workflows under `.github/workflows/`, then browses through the reference categories under `examples/`, and finishes with a random GitHub Actions tip.

### Pages deploy

`pages.yml` starts on every push to `main` and publishes [`public/index.html`](public/index.html) to GitHub Pages – the live page linked at the top of this README. Unlike `starter-ci.yml` and `repo-tour.yml`, it deliberately has **no** `workflow_dispatch` trigger: deploying the live site is something only the repository owner does by pushing, not something to invite visitors or contributors to trigger on demand. Unlike the FTP/SSH deploy examples under `examples/deploy/`, it needs no stored credentials: `permissions: pages: write` and `id-token: write` let GitHub issue a short-lived OIDC token to the job at runtime. The `build` job uploads the `public/` folder with `actions/upload-pages-artifact`, and the `deploy` job publishes it with `actions/deploy-pages`.

## Reference examples (`examples/`)

Everything from here on lives under `examples/`, is pure reading material, and doesn't show up in GitHub's Actions tab – GitHub Actions doesn't execute subfolders. The files are complete and commented so they can be copied 1:1 into your own project.

### Basics

#### Test Runner

`examples/basics/test-runner.yml` demonstrates the same basic flow as `starter-ci.yml`. The separate workflow is useful for seeing in the Actions UI that one event can trigger multiple workflows.

#### Manual Run Demo

`examples/basics/manual-run.yml` uses the `workflow_dispatch` trigger. When started, a target environment can be specified via the `environments` input field. The expression `${{ github.event.inputs.environments }}` inserts this value into the shell command that runs.

### Jobs & data exchange

#### Connecting jobs

`examples/jobs/dependencies.yml` contains the jobs `build`, `test`, and `deploy`. Since `build` and `test` have no dependency on each other, they can run in parallel. The `deploy` job uses `needs: [build, test]` and only starts once both preceding jobs have succeeded:

```text
build ──┐
        ├──> deploy
test ───┘
```

If `build` or `test` fails, `deploy` is skipped by default. The job IDs in `needs` must exactly match the IDs under `jobs`.

#### Pipeline stage order

GitHub Actions has no built-in `stages` property. In `examples/jobs/ordered-pipeline.yml`, the desired order is therefore modeled as a chain of job dependencies:

```text
lint ──> build ──> test ──> deploy
```

Each job uses `needs` to wait for its direct predecessor. If an earlier stage fails, the later stages that depend on it don't run by default. This ensures, for example, that no failed build reaches the test or deployment stage.

#### Environment variables

`examples/jobs/environment-variables.yml` shows three scopes for variables. A variable under the top-level `env` applies to the whole workflow, a variable under `jobs.<job-id>.env` only to that job, and a variable directly on a step only to that step.

Since the workflow runs on Ubuntu, the values are read in the shell command using, for example, `$APP_NAME`. The output shows that all three variables are available in the last step.

#### Passing values between jobs

`examples/jobs/outputs.yml` generates a small text value in the first job. The step writes it to the file `$GITHUB_OUTPUT` provided by GitHub. The step output is then published as a job output.

The second job waits for the first job with `needs: create-message` and reads the value via `${{ needs.create-message.outputs.greeting }}`. Outputs are suited to small values like a version number or a status, not entire files.

#### Transferring artifacts between jobs

`examples/jobs/artifacts.yml` creates a text file in the first job and uploads it with `actions/upload-artifact`. The second job runs on a fresh runner VM, downloads the file with `actions/download-artifact`, and prints its content:

```text
Create file ──> Upload artifact ──> Download artifact ──> Read file
```

Artifacts are useful for things like build results, test reports, or log files.

### CI for real projects

#### Frontend CI – Node.js

`examples/ci/frontend.yml` shows the typical CI flow for a frontend project: check out the repository, install Node.js, install dependencies, run tests, and then verify the build.

#### Backend CI – Python

`examples/ci/backend.yml` demonstrates the same approach for Python projects. After setting up Python, dependencies are installed and tests are then run with `pytest`.

#### Matrix testing

`examples/ci/matrix.yml` uses `strategy.matrix` to run the same `test` job multiple times in parallel, once per Node.js version listed in the matrix (18, 20, 22). GitHub Actions shows each run separately, so it's easy to see at a glance which versions pass and which don't. This is the standard way to check compatibility across several runtime versions without duplicating the job.

### Deployment

#### FTP deploy – shipping a static website live

`examples/deploy/ftp.yml` shows a real deployment scenario for a simple HTML/CSS page. On push to `main`, the content from a local folder is uploaded and pushed to an external web server via FTP.

The key point here is separating code from credentials:

- Server, username, and password don't live in the workflow code
- They're stored as GitHub secrets
- GitHub only injects these values into the job at runtime

#### Angular build & deploy

`examples/deploy/angular.yml` shows a slightly different flow: dependencies are installed first, then tests and build run, after which the finished build is passed on as an artifact and transferred to a server via SSH.

The key principles here are:

- separate jobs for build/test and deployment
- `needs` prevents a deploy if the build or test fails
- `upload-artifact` and `download-artifact` transport the result between jobs
- secrets protect the server credentials from the code

For a secret-less deployment model as a contrast to these two, see [Pages deploy](#pages-deploy) above – it's a real, runnable workflow in this repository rather than a reference-only example.

### Security

All four workflows below trigger on `pull_request`, except the secrets scan – they're meant to catch problems before code reaches `main`.

#### Secrets scan

`examples/security/secrets-scan.yml` searches the repository for accidentally committed credentials with [TruffleHog](https://github.com/trufflesecurity/trufflehog). It runs on `workflow_dispatch`, on every push to `main`, and additionally on a weekly `schedule` (cron), so older, unchanged commits get checked again periodically too. The action is pinned to an immutable commit hash instead of a tag, which prevents a tag that's later tampered with from silently running different code.

#### Dependency audit

`examples/security/dependency-audit.yml` checks a project's dependencies for known vulnerabilities (Software Composition Analysis / SCA) – it complements `examples/ci/frontend.yml` and `backend.yml` with a security check. The `audit-frontend` job runs `npm audit --audit-level=high`, and `audit-backend` runs `pip-audit`; both only fail the job for severe or critical findings.

#### SAST scan

`examples/security/sast.yml` runs [Semgrep](https://semgrep.dev/) against the project's own source code, using the `p/security-audit` rule set. Unlike the dependency audit, which checks third-party code, SAST (Static Application Security Testing) looks for insecure patterns in code you wrote yourself.

#### Container scan

`examples/security/container-scan.yml` builds a Docker image and scans it with [Trivy](https://github.com/aquasecurity/trivy) for known vulnerabilities in its packages, before the image would be used in a deploy workflow. `--severity HIGH,CRITICAL` filters out low-priority findings, and `--exit-code 1` fails the job if anything is found.

## Contributing

This repository is public and licensed under MIT. Bug fixes, clearer
explanations, and small new examples are explicitly welcome.

You don't need direct write access for this: via **fork → own branch →
pull request** you can propose a change. Small text fixes can even be
done entirely through the GitHub website. The detailed step-by-step
guide is in [CONTRIBUTING.md](CONTRIBUTING.md).

Before contributing, these documents are also worth a look:

- [Code of Conduct](CODE_OF_CONDUCT.md)
- [Security Policy](SECURITY.md)
- the templates GitHub shows when creating an issue or pull request

## Overall goal

This repository is meant to serve as a compact reference for GitHub Actions. It shows the key building blocks of a CI/CD pipeline in an understandable way:

- Triggers
- Jobs
- Steps
- Uses
- Run
- Dependencies between jobs
- Secrets
- Deployment examples

This makes it easy to explain the basic principle of GitHub Actions: automating code checks, tests, and deployment with clear, traceable steps.

## License

This repository is licensed under the [MIT License](LICENSE). The code may be freely used for learning, copying, and adapting.
