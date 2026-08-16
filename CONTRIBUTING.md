# Contributing to this project

Thanks for wanting to improve this reference project. Contributions are
possible even without write access to the original repository: you work in
your own fork and then propose your change with a pull request.

## The easy way via the GitHub website

For small fixes you don't need to install Git locally:

1. Open the file you want to change on GitHub.
2. Click the pencil icon **Edit this file**.
3. GitHub automatically creates a fork under your account if needed.
4. Change the file and briefly describe what you improved.
5. Click **Propose changes** and then create the pull request.

## Working locally with a fork

With the GitHub CLI, you can create and clone a fork in one step:

```bash
gh repo fork FriggemannMichael/gh-actions-workflow --clone
cd gh-actions-workflow
git switch -c docs/short-description
```

Without the GitHub CLI, you can first fork the repository via the GitHub
website and then clone your fork:

```bash
git clone https://github.com/YOUR-NAME/gh-actions-workflow.git
cd gh-actions-workflow
git remote add upstream https://github.com/FriggemannMichael/gh-actions-workflow.git
git switch -c docs/short-description
```

Here, `origin` refers to your own fork and `upstream` to this original
repository.

## Submitting a change

1. Create your own branch, for example `docs/explain-ftp`,
   `feature/new-example`, or `fix/yaml-error`.
2. Make a small, clearly scoped change.
3. Check indentation, comments, and links. YAML files use spaces,
   not tabs.
4. Create a clear commit and push the branch to your fork:

   ```bash
   git add PATH/TO/FILE
   git commit -m "Short description of the change"
   git push -u origin HEAD
   ```

5. Open a pull request on GitHub from your fork to
   `FriggemannMichael/gh-actions-workflow:main`.
6. Answer follow-up questions or suggestions directly in the pull request.

Pull requests automatically run the safe example workflow. Runs from new
external contributors may first need to be approved by a maintainer.

## What fits well with this project

- clearer explanations for beginners
- corrected or modernized GitHub Actions examples
- new, small examples with clear comments
- bug fixes in YAML, Markdown, or links
- improvements to the security and accessibility of the documentation

Please open an issue first for larger restructurings. This allows us to
jointly clarify whether the idea fits the project's learning goal and
structure.

## Rules for examples

- Examples should have a clear learning purpose and stay as small as
  possible.
- Credentials, tokens, private URLs, and other secrets never belong in a
  commit.
- External actions should be clearly explained with provider, name, and
  version.
- New runnable workflows only belong in `.github/workflows/`. Pure
  reference examples stay under `examples/` so they aren't run
  automatically.
- By contributing, you accept that your contribution will be published
  under the project's MIT license.

Please also follow the [Code of Conduct](CODE_OF_CONDUCT.md) and report
security issues according to the [Security Policy](SECURITY.md).
