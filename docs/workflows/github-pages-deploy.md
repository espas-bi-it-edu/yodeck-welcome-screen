# GitHub Pages Deploy

[Back to Overview](/.github/workflows/README.md) \
[To File](/.github/workflows/github-pages-deploy.yaml)

## Triggers

The workflow is set to trigger in the following instances:

* a `push` event, as long as:
  * it's on a branch **other** than `main`
  * it's on a branch whose name **does not** start with `feature/`
  * files inside the `src` folder were added, updated or deleted

* a `workflow-dispatch` event \
  This allows for **manual triggering** from the [GitHub Actions](https://github.com/espas-bi-it-edu/yodeck-welcome-screen/actions/workflows/github-pages-deploy.yaml) website and can be used to deploy specific `refs` without having to comit code.

```yaml
# Triggers
on:
  # Push event
  push:
    # Ignore pushes on main and feature branches
    branches-ignore:
      - main
      - feature/**

    # Only trigger when files under src were changed
    paths:
      - src/**

  # Allow manual triggering
  workflow_dispatch:
```

## Configuration

The flow runs on an Ubuntu runner. It is configured target the [`github-pages` environment on GitHub](https://github.com/espas-bi-it-edu/yodeck-welcome-screen/settings/environments). It receives **write access**, which is [required for it to be able to deploy to GitHub Pages](https://github.com/actions/deploy-pages#security-considerations). The environment itself is set to accept deployments from any branch.

```yaml
jobs:
  deploy:
    # Set runner OS
    runs-on: ubuntu-latest

    # Grant write permissions for GitHub Pages environment
    permissions:
      pages: write
      id-token: write

    # Set environment variables for runner
    environment:
      name: github-pages
      url: ${{ steps.deploy.outputs.page_url }}
```

## Execution

1. Checks out the repository at the `ref` that triggered the workflow (using [`actions/checkout`](https://github.com/actions/checkout)).

2. Creates an **artifact** (archive) of the files meant for deployment (using [`actions/upload-pages-artifact`](https://github.com/actions/upload-pages-artifact)). In this case it prepares all files under `src` for deployment.

3. Deploys the artifact on **GitHub Pages** (using [`actions/deploy-pages`](https://github.com/actions/deploy-pages)).

```yaml
jobs:
  deploy:
    # Steps to run
    steps:
      # Checkout push ref
      - name: Checkout Repository
        id: checkout
        uses: actions/checkout@v7

      # Create artifact (archive) of src-files
      - name: Upload Build Artifact
        id: build
        uses: actions/upload-pages-artifact@v5
        with:
          path: src

      # Deploy artifact on GitHub Pages
      - name: Deploy Page
        id: deploy
        uses: actions/deploy-pages@v5
```

## Links

* [GitHub Actions Official Documentation](https://github.com/features/actions)
* [GitHub Pages Official Documentation](https://docs.github.com/pages)
* [Current Deployment](https://espas-bi-it-edu.github.io/yodeck-welcome-screen)
* [Workflow Runs](https://github.com/espas-bi-it-edu/yodeck-welcome-screen/actions/workflows/github-pages-deploy.yaml)
