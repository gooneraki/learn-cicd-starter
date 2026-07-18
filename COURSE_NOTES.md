# Learn CI/CD with GitHub Actions, Docker and Go

- **Course URL:** https://www.boot.dev/courses/learn-ci-cd-github-docker-golang
- **Author:** Boot.dev Team
- **Last updated:** Jul 2026

Personal notes/reminder captured from the Boot.dev course, for future reference when revisiting this repo.

---

## CH1: Continuous Integration

### L1: Welcome to "Learn CI/CD"

In this course, we'll learn about Continuous Integration and Continuous Delivery (CI/CD) and how to use it to automate the process of building, testing, and deploying software.

We'll also deploy a production-ready web application to the internet using modern best practices!

**For Windows Users:** If you try to complete this course without WSL 2 installed on Windows, you're gonna have a bad time.

**Forking a Repo:** Forking is how you create your own copy of a repository on GitHub.

**Assignment**
- Open the base repository for this course on GitHub.
- Click the "fork" button to create a copy of the repo in your own GitHub account. When you're done, paste the URL of your forked repo into the text box and submit.

### L2: Run Notely

Clone down your forked repo onto your local machine:

```
git clone REPO_URL
```

Make sure it's your forked repo! Cloning boot.dev's repo will cause errors!

We'll be using this repository throughout the course.

**Assignment**
- Take a look at the README.md in the root of the project for instructions on how to run the application locally.
- Run it and open the hosted webpage in your browser.
- Run and submit the CLI tests using the Boot.dev CLI Tool in another terminal.

### L3: Branches

You might be primarily familiar with using Git in a single-user environment, pushing updates through a linear workflow. For example:

```
# working off of the "main" branch locally
git add .
git commit -m "undo Lane's giant mistake"
git push origin main
```

This works quite well for single developer side projects, but it doesn't work well when you're working within a team setting. What happens when a teammate makes changes to the same function you're working on and you both push directly to the main branch? What happens if you want a teammate to review your code before it's merged? What happens if you want to work on multiple features at once?

Branches solve these problems.

**What Is a Branch?**

A branch is (basically) a copy of your codebase. However, it's a special kind of copy that makes merging new code changes from one branch to another simple and easy.

So far, you may have been only working on the main branch, but you can create as many branches as you want, and they're a great way to keep changes that are unrelated to each other isolated and contained.

In many teams, the main branch reflects the state of the codebase that's running in production. This means that the main branch should always be stable and ready to deploy. If you want to:
- Add a new feature
- Fix a bug
- Refactor some code

Then you should create a new branch to add those changes to. This allows you to work on those changes independently without affecting the main branch.

**Assignment**
- Check which branch you're currently on with `git branch`. You'll see a list of branches, with an asterisk next to the branch you're currently on: `* main`
- Create a new branch called `addtests`. I like to name my branches after the change I'm about to make, and in this case, we're about to add tests.
  ```
  git switch -c addtests
  ```
- When you create a new branch, it only exists locally. Push this new branch up to GitHub:
  ```
  git push origin addtests
  ```
- You can check on GitHub to make sure the branch exists.
- Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L4: Pull Requests

Pull requests are a way to propose changes to a codebase. They're a great way to collaborate with your team and ensure that your code is reviewed and passes tests before it's merged into the main branch for deployment and for other developers to work on.

**Assignment**
- While still on the `addtests` branch, make a change to the repo's README.md file by adding a line of text to the bottom: "MYNAME's version of Boot.dev's Notely app." Replace MYNAME with your name.
- Push the new commit to the remote branch.
- Open a pull request to "propose" merging your changes into main.
  - Open your repo on GitHub
  - Click on the "Pull requests" tab
  - Click on the green "New pull request" button

Make sure that:
- The "base repository" is your forked repo, not the original `/bootdotdev/learn-cicd-starter` repo.
- The base branch is `main`, and the compare branch is your branch, `addtests`.
- You'll see a diff of the changes between main and your branch. If you're happy with the changes, click on the green "Create pull request" button.

**Do not merge the pull request!** You'll need it open for upcoming lessons.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L5: Continuous Integration

Continuous Integration (CI) is where developers regularly push code changes into a central repository, and by doing so, automated builds and tests are automatically run.

Those tests can include unit tests, integration tests, styling checks, linting checks, security checks or any other type of automated test. If any of the tests fail, the build is considered "broken" and the developer is notified so they can fix it.

**Our Internal CI at Boot.dev:** Here at Boot.dev we have CI tests that run each time a new pull request is opened. The reviewer doesn't need to manually check for formatting issues or run tests locally before approving the changes. It automates part of the code review process.

CI is all about automating as much of the testing and review process as possible.

**Assignment**
- While still on the `addtests` branch, create a new `.github` directory in the root of your repository
- Create a new directory inside `.github` called `workflows`
- Create a new workflow by adding a file called `ci.yml` inside `.github/workflows`:

  > Make sure you use the `.yml` extension. `.yaml` is valid, but we test for `.yml`.

  GitHub Actions workflows are written in YAML, and GitHub automatically checks for and runs workflows in the `.github/workflows` directory of your repository.

- Open `ci.yml` in your editor and add the following:

```yaml
name: ci

on:
  pull_request:
    branches: [main]

jobs:
  tests:
    name: Tests
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v6

      - name: Set up Go
        uses: actions/setup-go@v6
        with:
          go-version: "1.26.0"

      - name: Force Failure
        run: (exit 1)
```

By default, a step "succeeds" if it exits with a status code of 0 and "fails" if it exits with a status code other than 0.

Every (good) CLI tool that I'm aware of follows the convention of exit code 0 = pass, anything else = fail. For example, if a test case fails, `go test` will exit with a status code of 1.

Let's make sure that our CI tests fail when our tests fail. The last step of our workflow file is:

```yaml
- name: Force Failure
  run: (exit 1)
```

This step always fails because it runs the command `exit 1`, which exits with a status code of 1.

Commit and push your changes up to your remote branch. Then, on your pull requests page, you should see that the "tests" workflow runs and fails. Great! That means that our (very basic) CI is working.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L6: Breakdown of GitHub Actions

Let's break down some of the concepts from the workflow file we created.

```yaml
name: ci

on:
  pull_request:
    branches: [main]

jobs:
  tests:
    name: Tests
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v6

      - name: Set up Go
        uses: actions/setup-go@v6
        with:
          go-version: "1.26.0"

      - name: Force Failure
        run: (exit 1)
```

**Workflows:** A workflow is triggered when an event occurs in your GitHub repository. For example, we'll trigger our "tests" workflow when we open a pull request into the main branch.

In our case, the `ci.yml` file contains a single workflow called "ci", but we could have named it anything.

**Jobs:** A workflow is made up of one or more jobs. A job is a set of steps that run on the same runner, which is just a virtual machine that runs your job on GitHub's servers.

For now, we just have one job. You would typically have multiple jobs if you wanted to run your tests in parallel, or if you wanted to run your tests on multiple operating systems.

In our case, the `ci.yml` workflow contains a single job called "Tests".

**Steps:** A job is made up of one or more steps. A step is a single task that can run commands, a script, or an action. For example, the steps of a job might include:
- Checking out the code
- Installing dependencies
- Running tests

In our case, the "Tests" job contains 3 steps:
1. Check out the code
2. Set up Go
3. Force failure of the CI job

**Assignment**
- Update the last step of the job to run `go version` instead of `(exit 1)`. That command should just print the current version of Go and exit with code 0. Give the step a new name. Commit and push the changes, and your CI job should pass!

> A workflow that triggers on pull requests will re-run when the branch to be merged is updated.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L7: GitHub Actions

Let's dissect this workflow file and understand what each line is doing.

```yaml
name: ci

on:
  pull_request:
    branches: [main]

jobs:
  tests:
    name: Tests
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v6

      - name: Set up Go
        uses: actions/setup-go@v6
        with:
          go-version: "1.26.0"

      - name: Echo Go version
        run: go version
```

**Workflow Name**

```yaml
name: ci
```

The first line simply assigns a human-readable name to the workflow.

**Triggering the Workflow**

```yaml
on:
  pull_request:
    branches: [main]
```

The `on` key specifies when the workflow should run. In our case, we want to run the workflow when a pull request is opened to the main branch.

**Jobs**

```yaml
jobs:
  tests:
    name: Tests
    runs-on: ubuntu-latest
```

The `jobs` key is a list of jobs that make up the workflow. In our case, we only have one job called `tests`.

Each job has a few pieces of metadata associated with it. The `name` key assigns a human-readable name to the job. The `runs-on` key specifies the type of runner to use. In our case, we want to use the latest version of Ubuntu/Linux.

**Job Steps**

```yaml
- name: Check out code
  uses: actions/checkout@v6
```

Each job has a list of steps that make up the job. In our case, we have three steps. The first step checks out the code by using the pre-built checkout action to clone the repository into the runner. You'll almost always want to include this step in your workflows. The `uses` key specifies the action to use, and the `with` key specifies the inputs to the action.

An action is a reusable custom application that helps reduce the complexity of creating workflows. The checkout action is a publicly available action that checks-out your repository so your workflow can access it.

```yaml
- name: Set up Go
  uses: actions/setup-go@v6
  with:
    go-version: '1.26.0'
```

The second step sets up Go by using the pre-built setup-go action to configure a Go environment for use in workflows.

```yaml
- name: Echo Go version
  run: go version
```

The third step runs the `go version` command to print the version of Go installed in the runner. The `run` key specifies the command to run. The `run` key is used to run arbitrary command-line commands in the runner.

---

## CH2: Tests

### L1: Running Tests

Our current CI doesn't do anything interesting.

A good CI pipeline typically includes:
- Unit tests
- Integration tests
- Styling checks
- Linting checks
- Security checks
- Any other kind of automated test

If any of the tests fail, the build is considered "broken" and the developer is notified (in our case by GitHub) so they can fix it.

**Assignment**
- The Notely repo has, gasp, zero unit tests!
- Create `internal/auth/get_api_key_test.go`.
- Add a couple unit tests for `GetAPIKey`.

  If you're still a little fuzzy on how to write unit tests in Go, I'd highly recommend Dave Cheney's excellent blog post on the subject.

- Run them locally to make sure they work:
  ```
  go test ./...
  ```
  The `./...` tells Go to run all tests in the current directory and all subdirectories.
- Run and submit the CLI tests from the root of your repo.

### L2: Tests on CI

Now that you've got some unit tests working locally, let's get them running on CI.

**Assignment**
- Remove the `go version` step from the "Tests" workflow, and replace it with a step that runs your tests. You should run them in exactly the same way as you do locally:
  ```
  run: go test ./...
  ```
- Break your code temporarily. We want to make sure that your CI fails when your tests don't pass. Commit and push the broken code and watch the workflow fail.

  You'd be surprised how many times companies I've worked at thought they had CI that was checking for failures, but broken code didn't actually fail the CI. It's important to make sure that your CI is checking for failures.

- Once you're convinced that the CI properly catches failing tests, fix your code, commit it and push it to GitHub. You should see your CI tests pass on GitHub.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L3: Code Coverage

Code coverage is a measure of how much of your code is being tested. It's a controversial metric, but I'll try to provide a balanced take... granted I'm not without my own biases.

```
code_coverage = (lines_covered / total_lines) * 100
```

If you have 1000 lines of code in your project, and you have tests that cover the logic in 500 of those lines, then you have 50% code coverage.

**Why Is Code Coverage Controversial?**

It's quite possible to have 100% code coverage and still have bugs in your code. It's also possible to have 0% code coverage and have a bug-free application. Unit tests help us find bugs, and codify the expected behavior of individual units of code but they don't guarantee that there are no bugs.

My personal take is that it's really hard to say "20% is bad, 80% is good". I think some functions and methods are more important to have unit tests for than others. For example, I don't love the idea of mock unit testing external systems, like databases.

I think that's a better use case for integration tests.

**What Does This Mean to You?**

As a junior developer, you should know what code coverage is, and you should be amicable to the code coverage requirements of the company you work for. You'll certainly develop your own opinions (and should politely vocalize them) as you gain trust in your organization. It's important to be a team player and to be open minded to your team's processes, especially when you're new.

**Assignment**
- We won't fail our CI if there aren't enough unit tests, but we should at least print the coverage out to the console. Add the `-cover` flag to the `go test` command in your CI workflow, then commit and push your changes.
- You should be able to inspect the logs of your latest workflow run in the GitHub UI (the "actions" tab) and see the code coverage report.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L4: README Badge for Tests

One cool feature on GitHub is that you can add a dynamic badge to your README.md file that shows the status of your tests.

It's a great way to show off that your code is well-tested, and that the tests are passing without users having to go check the actions tab.

**Assignment**
- Add a badge to the top of your README.md file that shows the status of your tests. The syntax for the URL of the dynamically generated image is:
  ```
  https://github.com/<OWNER>/<REPOSITORY>/actions/workflows/<WORKFLOW_FILE>/badge.svg
  ```
  Your README.md file is written in markdown, and the syntax for adding an image is:
  ```
  ![alt text goes here](IMAGE_URL)
  ```
- Commit and push your changes to GitHub, then merge them. Once your changes are merged into your main branch, you should see the badge appear on the main page of your repository in the rendered README.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

---

## CH3: Formatting

### L1: Formatting

Automated code formatting keeps your code consistent and readable. It's also a great way to avoid arguments about code style (bikeshedding).

I almost never start a new project without enforcing automated code formatting.

For example, this is technically valid:

```go
func main() {
	fmt.Println("hello world!") }
```

But it's not formatted according to standard conventions. You would normally write it like this:

```go
func main() {
    fmt.Println("hello world!")
}
```

**`go fmt`:** The `go fmt` command formats Go code. It's built into the Go toolchain, so you don't need to install anything additional to use it.

I have configured my editor to auto-format my code on save, so my code is always formatted. The default "Notely" project should already be formatted properly, so let's break it so that we can see how `go fmt` works.

**Assignment**
- While still on your `addtests` branch, edit one of the functions in the Notely codebase so that it's not formatted properly. For example, you could remove the whitespace between the function parentheses and body:
  `func main() {` vs `func main(){`
- Save the file without autoformatting it (you might need to get around auto-formatting if your editor does it automatically).
- Run `go fmt ./...` in the root of your project. You should see the code get formatted properly.
- Run and submit the CLI tests from the root of your repo.

### L2: Check Formatting

Unfortunately (in my opinion) the `go fmt` command always exits with status code 0. Luckily `go fmt` prints the names of all the files it fixes, so if we want to fail a CI check when a repo isn't formatted, the easiest way is to make sure that nothing is printed to stdout.

We can use the `test` command to do so:

```
test -z $(go fmt ./...)
```

Let's break down how it works:
- `go fmt ./...`: Runs the `go fmt` tool on the current directory and all its subdirectories (that's what `./...` stands for). `go fmt` returns the names of files that it has formatted. If no files need formatting, it will return an empty output.
- `$(go fmt ./...)`: The `$()` syntax is used for command substitution in bash. It runs the command inside the parentheses, and then replaces the `$()` in the command line with the output of that command.
- `test -z $(go fmt ./...)`: The `test` command is built into bash. The `-z` option checks if the following argument is an empty string, returning 0 if it is, and 1 if it isn't.

**Assignment**
- Edit one of the functions in the Notely codebase so that it's not formatted properly. Save it and run:
  ```
  test -z $(go fmt ./...)
  ```
- Check the exit code:
  ```
  echo $?
  ```
  The `echo $?` command prints the exit code of the last command that was run. You should see that it prints 1, indicating that the repo is not formatted properly.

  However, it should have also fixed the formatting!

- Run this one more time:
  ```
  test -z $(go fmt ./...)
  echo $?
  ```
  You should see that it prints 0, indicating that the repo is formatted properly.

- Run and submit the CLI tests.

### L3: Formatting CI

Now that we understand how to check for formatting issues, let's add a formatting check to the CI workflow.

**Check for Formatting Issues**

```
test -z $(go fmt ./...)
```

**Assignment**
- Run the formatting check in the CI workflow under a separate "style" job. Give it the name "Style".

  We could simply add it as another step within the same "tests" job, but I think it will be cool to run independent CI checks in parallel.

  After all, tests and formatting aren't dependent on each other, so why not run them in parallel and save some time? However, we will need to duplicate the "set up Go" and "check out code" steps in the new job because they are not shared between jobs.

- Commit and push your changes to your remote branch.
- Create another pull request from your branch into main. The CI workflow only runs the checks when a pull request is made to main, and since you merged your first pull request you'll need a new one.
- Verify that both CI jobs pass.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

**Tips:** If you have an open pull request and push new commits to the head branch (the one with the new changes) GitHub will automatically update the PR and rerun the actions.

---

## CH4: Linting

### L1: Linting

Code Formatting deals with the aesthetic appearance of the code. For example, it enforces things like whitespace, indentation, and line length.

On the other hand, linting has more to do with the analysis of code to detect functional issues. Linters provide warnings or errors for potentially problematic code.

**Staticcheck:** Personally I use staticcheck for linting in Go. It has a lot of useful checks with sane defaults, and it's easy to configure. As far as I can tell it's essentially replaced golint as the most popular linter for Go.

**Install Staticcheck**

```
go install honnef.co/go/tools/cmd/staticcheck@latest
```

If you're on Mac and already have Homebrew, you may run:

```
brew install staticcheck
```

**Run Staticcheck**

To run staticcheck on the entire Notely codebase, run this from the root of the project:

```
staticcheck ./...
```

If you get a command not found error, your GOPATH may not be in your PATH. Run the following and use the appropriate config file name for your shell.

```
echo 'export PATH=$PATH:$GOPATH/bin' >> ~/.bashrc
source ~/.bashrc
```

It should run without errors because the project is already configured to pass all of the default checks. Let's make sure that's true by breaking something!

Add an unused function to the `main.go` file:

```go
func unused() {
    // this function does nothing
    // and is called nowhere
}
```

If you're using an editor with Go language server support (like Zed or VS Code with the Go extension), staticcheck should automatically detect the error and underline the function name. You can also run staticcheck manually from the CLI:

```
staticcheck ./...
```

You should see an error like this:

```
func unused is unused (U1000)
```

If so, great! Staticcheck is working properly.

Run and submit the CLI tests from the root of your repo.

### L2: CI Linting

To get linting on our CI runner, we can't just call `staticcheck`: it's not installed on the runner!

Before any steps that use the staticcheck command, you'll need to install it:

```yaml
- name: Install staticcheck
  run: go install honnef.co/go/tools/cmd/staticcheck@latest
```

**Assignment**
- Add a new step to the same "style" job that currently only checks for `go fmt` issues. Run staticcheck after `go fmt` to make sure that formatting and linting issues are caught by our pipeline.
- Commit and push your changes with the unused function to your branch with the open pull request. You should see your CI workflow fail since staticcheck will catch the unused function.
- Once you've verified that your CI workflow fails, remove the unused function and commit and push your changes again. Your CI workflow should now pass!

Paste the URL of your GitHub repo into the box and run the GitHub checks.

> You can use the official Staticcheck GitHub Action instead.

### L3: Linting Review

"Linting" can be such a vague and confusing term. I want to go over a few of the most common and useful checks that staticcheck lints for, just so you can see a few more examples.

If you're curious, you can see the full list of checks here.

**Unused Variables (U1000):** This is the one we've been using so far. It checks for unused variables, functions, and types.

**Invalid Printf Call (SA5009):** This one is pretty self-explanatory. It checks for invalid Printf calls. For example, if you try to print a string with a `%d` format specifier, you'll get an error.

**"Defer" in Range Loops (SA9001):** Defers in range loops may not run when you expect them to, and in general you should just avoid them.

**Replace for Loops With Call to Copy (S1001):**

Before:

```go
for i, x := range src {
    dst[i] = x
}
```

After:

```go
copy(dst, src)
```

---

## CH5: Security

### L1: Security Checks

Another common use case for continuous integration is static security checks. These are checks that can be run on your code to find potential security vulnerabilities.

There are many products out there that do this sort of thing. One of the most popular open-source ones is Gosec.

**Install Gosec**

```
go install github.com/securego/gosec/v2/cmd/gosec@latest
```

**Run Gosec**

To run gosec on the entire Notely codebase, run this from the root of the project:

```
gosec ./...
```

You should see a few unhandled errors! That's okay. Don't fix them yet.

Run and submit the CLI tests from the root of your repo.

### L2: CI Security

Like staticcheck, gosec is not part of the Go toolchain. We'll need to install it in our remote runner before we can use it.

**Assignment**
- Security isn't a style concern, so let's add these next steps after `go test` in the "Tests" job.
- Add the install step.
  ```yaml
  - name: Install gosec
    run: go install github.com/securego/gosec/v2/cmd/gosec@latest
  ```
- Add another step to do a gosec check.

  We want to be sure gosec works, so the first time you push the new `ci.yml` file to your PR branch, we are intentionally making the check fail. Don't fix the errors in the Notely project code yet.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L3: Fix Security Issues

**Assignment**
- Update your code to fix the security issues found by gosec. Make sure you don't break any functionality when doing so.
- Push the new code up to your PR to trigger a new CI run. It should pass now.

  It's okay if a `.env` file doesn't exist, the server should simply print a warning and continue.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

**Tips:** Make sure the code is bug free and the other checks will pass.

To fix the gosec issues, open the GitHub Actions logs for the failed run, find the gosec step, and read each finding.

### L4: Security Review

Just because our codebase passes all of our tests, linters, and security checks doesn't mean it's "perfect" (or frankly even that it's "good").

These kinds of automated tests can help us eliminate ~80% of the most obvious bugs, stylistic anti-patterns, and security vulnerabilities, but they can't catch everything. We still need to take care to write good, secure code.

CI is an amazing tool to automate a lot of the stuff you'd manually check anyway. However, just because the checks pass doesn't mean your code is perfect. You still need to use your brain!

---

## CH6: Build

### L1: Docker

We'll be using Docker to deploy Notely to Google Cloud Run. If you're not familiar with Docker, you should take our Learn Docker course here first.

Make sure you have Docker installed by running `docker --version` in your terminal. You should be on at least version 20.10.21.

**Assignment**
- Run the `scripts/buildprod.sh` script found in the root of the Notely repository. This will produce a `notely` binary that's compiled for Linux, which is the OS our Docker image will run on.
  ```
  ./scripts/buildprod.sh
  ```
- Build the Docker image locally:
  ```
  docker build -t DOCKERHUB_NAMESPACE/notely:latest .
  ```
  `DOCKERHUB_NAMESPACE` should be replaced with your Docker Hub username.
- Run the Docker image locally:
  ```
  docker run -e PORT=8080 -p 8080:8080 DOCKERHUB_NAMESPACE/notely:latest
  ```
- Open the app in your browser at http://localhost:8080. You should see the Notely app running locally!
- Run and submit the CLI tests.

**Troubleshooting**

If you get an architecture or exec format error, that's probably because your machine is different than the intended Docker Container.

macOS:
- Set `GOOS` to `linux` and `GOARCH` to `arm64` in the build script or set them when building the binary
  ```
  GOOS=linux GOARCH=arm64 go build -o notely .
  ```
- Set the `--platform` flag to `linux/arm64` in the Dockerfile or when running the docker build command.
  ```
  docker build -t DOCKERHUB_NAMESPACE/notely:latest . --platform=linux/arm64
  ```

**Tips:** Use http, not https!

Creating a user on the Notely site is not expected to work yet, we haven't set up the database!

### L2: Continuous Deployment

Continuous Deployment (CD) is the process of automatically deploying code changes to a production environment after the code has been built and tested. Let's set up CD for Notely!

We'll be using GitHub Actions again, but we'll create a new workflow for CD: `cd.yml`

**Assignment**
- Create a new workflow `.github/workflows/cd.yml`
- It should trigger on push into the main branch.
  ```yaml
  on:
    push:
      branches: [main]
  ```
- It should have a single job called Deploy
- It should checkout the code
- It should set up the Go toolchain
- It should build the app using the `scripts/buildprod.sh` script

  To be clear, the ci workflow runs when a pull request is opened, but the cd workflow runs when a pull request is merged (or when code is pushed directly to the main branch).

- Run the cd workflow and ensure it works by merging your PR into main (or pushing directly to main).

Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L3: Google Cloud Platform

GCP is one of the "big three" cloud providers, along with AWS and Azure. We're going to use GCP to host our Notely application!

Everything we do in this course falls under the free tier of GCP, at the time of writing. That said, you will need to provide a credit card to sign up, and you should be careful to not exceed the free tier and free trial limits if you don't want to be charged.

**Create a GCP Account:** First, you'll need to create a GCP account.

**Create a Project:** Once you've created an account, you'll need to create a project.

Name the project `notely`.

One of my favorite aspects of GCP is how it groups resources by project. We'll keep everything for Notely in a single project, and when you're done with this course you can simply delete the project to clean everything up in one place.

**Create a Billing Account:** Next, you'll need to create a billing account. This is where you'll provide your credit card information. You can find the billing section in the GCP console by clicking the hamburger menu in the top left, then "Billing".

Ensure your billing account is linked to your project, and you are able to see the billing information for your project in the GCP console.

### L4: Google Cloud SDK

For some tasks, it makes sense to use the `gcloud` CLI instead of the GCP web console. For example, to run tasks from a GitHub Actions workflow, we'll need to use the `gcloud` CLI.

- Install the `gcloud` CLI tool.
- Initialize it by running `gcloud init` in your terminal. If you are using WSL, use `gcloud init --console-only` instead.
- It will prompt you to login by opening a browser window. Login with the same account you used to create your GCP project.
- Select your `notely` project.

**Assignment**
- Run the following command to verify your authenticated account and project settings:
  ```
  gcloud config list
  ```
- Submit the CLI tests from the root of your repo. There's no penalty on failure for this lesson.

**Troubleshooting Tips**

You should already be authenticated after running `gcloud init`. If not, run:

```
gcloud auth login
```

If you are using WSL, you may need to restart your WSL instance after installing the `gcloud` CLI for it to be recognized in your terminal's PATH.

### L5: Google Artifact Registry

We'll be using Google Artifact Registry to store our Docker images. It's similar to Docker Hub, but it's private and hosted on GCP.

Whenever we create a new version of Notely, we'll build it into a new Docker image version and push that to Artifact Registry.

**Assignment**
- In the GCP console, search for and enable the Cloud Build API.
- Within Artifact Registry in the GCP console, enable the Artifact Registry API.
- Click Create Repository:
  - Name: `notely-ar-repo`
  - Format: Docker
  - Mode: Standard
  - Location type: Region
  - Region for deployment: `us-central1`
  - Leave "Google-managed encryption key" selected
  - Disable vulnerability scanning if the option appears; we don't need paid scans for this course

  The image hosting region from earlier, and service deployment region we are targeting now, may not necessarily be the same region. Cloud providers provide flexibility with availability zones, so that engineers can pick and choose the most optimal regions for your system.

- Build and push the Docker image to Artifact Registry:
  ```
  gcloud builds submit --tag REGION-docker.pkg.dev/PROJECT_ID/REPOSITORY/IMAGE:TAG .
  ```
- Run and submit the CLI tests from the root of your repo.

  You can copy/paste your actual value for the tag from your Artifact Registry repo page in the GCP console.

  If the `gcloud` command responds with an error about permissions, simply check that the image was uploaded to the new registry. We will take care of permissions in a future step.

### L6: Automate Builds

Now that we've built the Docker image locally, let's build it automatically on every push to the main branch.

**Assignment**
- Use the `setup-gcloud` action to authenticate with GCP.

  I recommend using the simple service account key JSON setup.

**Creating a Service Account**
- Go to the IAM & Admin Service Accounts section of the GCP console.
- Create a service account and name it "Cloud Run Deployer" with these permissions:
  - Cloud Build Editor
  - Cloud Build Service Account
  - Cloud Run Admin
  - Service Account User
  - Viewer
- Create a JSON key for that service account and download it to your computer.

**Add the Key As a Secret in GitHub Actions**
- Go to your GitHub Repo > Repository Settings > Secrets and variables > Actions > New repository secret (not "environment secret" - use a repository secret)
- Name: `GCP_CREDENTIALS`
- Secret: Paste the entire JSON key from the file you downloaded from GCP
- Save the secret

**Update Your GitHub Action Workflow**
- After the buildprod script runs, add the setup-gcloud steps to setup the `gcloud` CLI and authenticate with GCP.
- Finally, add a step to build the Docker image and push it to Google Artifact Registry.
  ```
  gcloud builds submit --tag REGION-docker.pkg.dev/PROJECT_ID/REPOSITORY/IMAGE:TAG .
  ```
  You can copy/paste your actual value for the tag from your Artifact Registry repo page in the GCP console.

- Commit and push your changes to GitHub. You should see the GitHub Action run and successfully build and push the Docker image to Google Artifact Registry.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

---

## CH7: Deploy

### L1: Google Cloud Run

Cloud Run is a serverless container hosting service. It's a great fit for Notely because it has a generous free tier, scales automatically, and automagically configures pesky infrastructure like:
- Load balancing
- DNS
- HTTPS

In a nutshell, we give Cloud Run a Docker image, and it runs it for us.

**Cloud Run Services**

Cloud run has 2 types of applications:
- Services
- Jobs

A "service" is a Cloud Run application that listens and responds to web requests, as opposed to a "job" which is simply a task that runs to completion.

Notely makes sense as a service because it's a web application that needs to respond to web requests. It needs to serve the frontend, and it needs to respond to API requests.

**Assignment**

For now, let's create a test service. Once we're comfortable with the process, we'll deploy Notely.

- Navigate to the Cloud Run section of the GCP console.
- Click "Deploy Container" and select "Service"
- Select "Deploy one revision from an existing container image"
- Container image URL: `bootdotdev/getting-started`
- Service name: `test`
- Region: `us-central1`
- Under Authentication, choose "Allow public access". This will allow anyone to access the webpage the service serves without having to log in to GCP
- Service scaling, set maximum number of instances to 4.
- Ingress control: "All" checked (allow direct access to the service)
- Go to "Container(s), Volumes, Networking, Security". Under "Container(s)" change the "Container port" from 8080 to 80.
- Create the service
- Wait for the service to deploy
- Click the service's URL to see the webpage it serves
- Run and submit the CLI tests and use the URL of your service (e.g. `bootdev config base_url https://test-vo4kpyh36a-uc.a.run.app/`).

### L2: Cloud Run Review

What just happened was actually pretty amazing. A sysadmin from the early 2000s would be blown away by how easy it is to deploy a web application today. You started with a Docker Image that holds a lightweight, portable, and self-contained version of a web application, and in one click you deployed it to the public internet, complete with:
- Load balancing
- DNS (albeit a long, ugly URL)
- Auto-scaling (As more and more HTTP requests come in, Cloud Run will automatically spin up more instances of your app to handle the load)
- HTTPS

It's important to understand that many companies have a more complex setup and do a lot of that stuff manually. Levels of complexity vary dramatically in CI/CD automation from one company to the next, just like levels of complexity vary dramatically in applications from one company to the next.

Just to give you a taste of the variety of CI/CD flavors, I want you to know that some companies isolate CI from CD into two separate systems, e.g. Jenkins for CI vs Rundeck for CD. On the flip side, you will also find companies that bundle CI/CD together into a single job that builds and deploys the application. If there's a different way to do CI/CD, there's a company out there somewhere prototyping it. Now that's enterprise coding!

Regardless, the principle is the same everywhere. At its core, CI/CD enables us so that:
- When PRs are opened, run static analysis and tests
- When PRs are merged, build and deploy the app automatically

That "build and deploy the app automatically" can be 10 lines of yaml or it can be thousands of lines of custom bash scripts. It all depends on the complexity of the app and the needs of the company.

**What Makes a "Good" CI/CD Pipeline?**
- Deterministic builds. The same code should always produce the same build.
- Fast builds. The faster the better. This makes getting bug fixes and new features out to users faster.
- Portable. This is why I love when the majority of a CI/CD pipeline is just bash scripts. It's easy to run locally, and it's easy to run on any CI/CD platform.
- Fully automated. The fewer manual steps, the better. It's really annoying to manually run database migrations and click buttons. It's also error-prone.

### L3: Delete the Test

It's very bad practice to leave unused resources lying around in your cloud provider. It can be:
- Expensive
- Confusing
- A security risk

**Assignment**
- Delete the "Getting Started" test service you just created.

### L4: Cloud Run Custom

Let's run Notely on Google Cloud Run!

Create a new service, but this time instead of using a sample container, use our new image hosted in Artifact Registry that we built previously.

**Assignment**
- Go to the Cloud Run section of the GCP console.
- Go to "Services" and click "Deploy Container".
- Choose Deploy one revision from an existing container image
- Container Image URL: Select your container image from the Artifact Registry
- Name: `notely`
- Region: select `us-central1`
- Authentication: check Allow public access.
- Billing: check Request-based
- Service scaling: check Auto scaling and set the Maximum number of instances to 4.
- Ingress: check All (to allow direct access to the service)
- Create the service, and wait for it to deploy
- Click the service's URL to see it in action!
- Run and submit the CLI tests and use the URL of your service (e.g. `bootdev config base_url https://test-vo4kpyh36a-uc.a.run.app/`).

### L5: Cloud Run Updates

Now that we have a service configured and running, let's update our GitHub actions workflow to automatically deploy changes to the app when we push to the main branch.

**Assignment**
- Add a new step to the deploy job in the GitHub actions workflow to deploy the app to Cloud Run. Because we want to allow unauthenticated access to the app, we'll also need to add a new security setting to the Cloud Run service. I recommend simply adding this to the same step in CD:
  ```yaml
  - name: Deploy to Cloud Run
    run: gcloud run deploy notely --image REGION-docker.pkg.dev/PROJECT_ID/REPO_NAME/IMAGE:TAG --region REGION --allow-unauthenticated --project PROJECT_ID --max-instances=4
  ```
  Target the `us-central1` region for service deployment and the latest image.

**Make a Change and Push to GitHub**
- Change the `/static/index.html` file so that the h1 tag says "Welcome to Notely" instead of just "Notely".
- Commit and push your changes to GitHub. You should see the GitHub Action run and successfully deploy the new version of the app to Cloud Run.
- Open the URL in your browser and you should see the new version of the app.
- Submit the CLI tests and use the URL of your service (e.g. `bootdev config base_url https://test-vo4kpyh36a-uc.a.run.app/`). There's no penalty on failure for this lesson.

---

## CH8: Database

### L1: Turso

Turso is a cloud provider that specializes in hosting serverless SQLite-like databases (technically it's their fork, libsql). It's a great fit for Notely because it has a very generous free tier, and it's easy to use.

**Assignment**

**Create an Account**
- Create an account
- Complete the onboarding process
- Create your first database with the name: `notely-db`

**Install and Configure the Turso CLI**

Here's the full quickstart guide in the Turso docs, but in a nutshell:

Install the CLI:

```
# macOS
brew install tursodatabase/tap/turso

# Linux / WSL
curl -sSfL https://get.tur.so/install.sh | bash
```

Note that you may need to restart your shell to make sure the changes have taken effect.

Login:

```
turso auth login
```

Show your databases:

```
turso db list
```

Connect to your database:

```
turso db shell notely-db
```

Run a simple SQL query:

```sql
SELECT sqlite_version();
```

Create a test table:

```sql
CREATE TABLE test (
  id INT,
  name TEXT
);
```

Show the table:

```sql
SELECT name FROM sqlite_master WHERE type='table' ORDER BY name;
```

Delete the table:

```sql
DROP TABLE test;
```

Exit the shell with Ctrl+C.

This SQL shell is a great way to manually interact with your database for debugging purposes, but our server will of course interact with it programmatically.

Run and submit the CLI tests from the root of your repo.

### L2: Migrations

Notely uses goose to manage database migrations, that is, the process of updating the database schema so that the server can function properly.

**Assignment**
- Install goose if you don't already have it.
  ```
  go install github.com/pressly/goose/v3/cmd/goose@latest
  goose -version # should be at least v3.18.0
  ```
- On the Databases tab in Turso, click on your database and copy the URL. It should look something like this:
  ```
  libsql://notely-db-YOURNAME.turso.io
  ```
- Paste it into a `.env` file in the root of your repo under the key `DATABASE_URL`.

  The final connection configuration line will look something like this:
  ```
  DATABASE_URL=libsql://notely-db-YOURNAME.turso.io
  ```

  Don't forget to add your `.env` file to your `.gitignore`

- Use the Turso CLI to get an authentication token:
  ```
  turso db tokens create notely-db
  ```
- Add the token to your connection string in the `.env` file:
  ```
  DATABASE_URL=libsql://notely-db-YOURNAME.turso.io?authToken=YOURTOKENHERE
  ```
- Run the migrations, I created a nice little script for you:
  ```
  ./scripts/migrateup.sh
  ```
- Once successful, use the Turso CLI to ensure the tables are there:
  ```
  turso db shell notely-db

  PRAGMA table_info(users);

  PRAGMA table_info(notes);
  ```

If everything looks good, you're ready to move on to the next lesson!

### L3: Migrate Automatically

There are many ways that teams handle database migrations in continuous deployment environments.

Some teams prefer to run migrations as part of the deployment process, others run them manually, and some have more complete systems featuring downward migrations. Downward migrations provide one more tool for recovery in case of a problem with a recent database schema change. For simplicity, this course runs migrations via our GitHub Actions deployment script.

We'll simply migrate the DB to the latest version every time we deploy. That way, if the code we're deploying requires a new schema, we'll always have it. The only time this can be a problem is if a migration makes backward-incompatible changes to the schema (like dropping a table). If the currently running application needs a table that we drop, it will stop working until the new code is deployed.

That's bad, downtime is bad.

To avoid those scenarios, we could simply roll out the code that stops relying on the hypothetical table first, then in the next deployment remove the table in a migration.

**Assignment**
- Add a `DATABASE_URL` secret to your GitHub repo's Settings -> Secrets and variables -> Actions. Paste your full `DATABASE_URL` from your `.env` (including the `?authToken=...`). The migration will fail if you wrap it in quotes.
- Add a new section to the `cd.yml` deploy job, that grabs the secret and provides it to the rest of the job environment.
  ```yaml
  jobs:
    deploy:
      name: Deploy
      runs-on: ubuntu-latest

      # This part
      env:
        DATABASE_URL: ${{ secrets.DATABASE_URL }}
  ```
- Update the cd workflow in your GitHub Actions `cd.yml` file to run migrations using the `migrateup.sh` script before deploying the application.

  I'd recommend running it after the Docker image is built, but before the deployment of the image. That accomplishes two things:
  1. We won't run the migration if there is a problem building the image.
  2. The migration will be live before the new code is deployed.

  Remember to install goose in the runner, right after you set up Go (it's installed with `go install`).

  Finally, you can run `git diff` / `git diff HEAD` to check for any sensitive credentials like database connection strings, that may have slipped into your source code. We've taken the liberty of `.gitignore`'ing the `.env` file already, but `git diff` can point out credentials mistakes even before you commit code changes.

- Now commit the code. Push it to GitHub, then make sure the workflow runs as expected.

Paste the URL of your GitHub repo into the box and run the GitHub checks.

### L4: Using the DB

Migrations are done! Now let's configure our Cloud Run app to use the database.

**Assignment**
- Go to the secrets manager in GCP (enable the API if you have to) and create a new secret:
  - Name: `notely_db_password`
  - Paste your database URL into the value field
- Go back to the Cloud Run page and select your app. Click "Edit & Deploy New Revision" and then under "Container(s)" -> "Variables & Secrets" -> "Reference A Secret". Supply the name `DATABASE_URL`. Select the `notely_db_password` secret. Select latest version. Select Done.
- Select Deploy. (We expect this deployment to fail with a permission error.)
- Back in the Google Cloud IAM & Admin dashboard, edit the Cloud Run Deployer service account and assign it a new role, Secret Manager Secret Accessor.
- Return to the Cloud Run notely service and edit the service once more. Select the Security tab. We want to use the Cloud Run Deployer service account.
- Select Deploy again. (This deployment should work better.)

Let's test the application deployment.

You'll know the app is fully operational when:
- You can create new users
- You can create new notes
- Refreshing the page indicates that you are "logged in"
- You can view notes that you create

Run and submit the CLI tests and use the URL of your service (e.g. `bootdev config base_url https://test-vo4kpyh36a-uc.a.run.app/`).

### L5: CI/CD Review

Congratulations on deploying a full-stack web application to the public internet!

Remember to shut down your notely project in GCP when you're done playing with it, to reduce extra bank charges.

Remember to delete your Turso database as well.

**Recap of Your Accomplishments:**
- You set up a continuous integration pipeline with GitHub Actions that ensures new PRs pass certain checks before they are merged to main:
  - Unit tests pass
  - Formatting checks pass
  - Linting checks pass
  - Security checks pass
- You configured a cloud-based SQLite database hosted on Turso
- You set up a continuous deployment pipeline with GitHub Actions that does the following whenever changes are merged into main:
  - Builds a new server binary
  - Builds a new Docker image for the server
  - Pushes the Docker image to the Google Artifact Registry
  - Deploys a new Cloud Run revision with the new image and serves the app to the public internet

Pat yourself on the back! That's a pretty robust setup for our simple CRUD app.

**Some Things to Keep in Mind**
- Google Cloud Platform (GCP) is just one of the 3 major cloud providers. AWS and Azure are also popular choices. In many ways, their offerings are similar, but sometimes the differences matter.
- Google Cloud Run handles a lot of complexity for you. Managing DNS, SSL, load balancing, and auto-scaling are all things that many companies do manually, so those are still useful skills to have, but are outside the scope of this course.
- Turso is a fully-managed third-party database host. There are many options out there for databases and database hosting that are worth learning about, but again, outside the scope of this course.
- Essentially every technology/product we used in this course has viable alternatives. You don't need to know how to use all of them before your first job, but you should understand some of them.
