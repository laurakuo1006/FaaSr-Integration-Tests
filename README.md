# FaaSr-Integration-Tests

The FaaSr Integration Tests repository can be used for running end-to-end tests of changes to the [FaaSr-Backend](https://github.com/FaaSr/FaaSr-Backend) library, [FaaSr-Workflow](https://github.com/FaaSr/FaaSr-workflow) repository, or [FaaSr-Docker](https://github.com/FaaSr/FaaSr-Docker) containers.

## Overview

This repository contains the following main folders:

- **`docker`:** Dockerfiles for custom Docker containers.
- **`faasr_workflow`:** The FaaSr-workflow repository, included as a git subtree.
- **`framework`:** The integration testing framework.
- **`functions`:** Integration test functions.
- **`integration_tests`:** Integration tests written with `pytest`.
- **`workflows`:** Integration test workflows

## Getting Started

To contribute workflows for integration testing, it is recommended to fork this repository and create a pull request with your integration test.

After forking the repository, initialize your Python environment with `uv`:

```bash
uv sync
source .venv/bin/activate
```

Next, make a copy of [`.env.template`](./.env.template) named `.env` and initialize the following variables:

| Variable | Description |
| - | - |
| `GH_PAT` | Your GitHub personal access token. |
| `GITHUB_REPOSITORY` | The name of your forked repository. |
| `GITHUB_REF_NAME` | The branch containing your workflow file. |
| `S3_ACCESSKEY`, `S3_SECRETKEY` | Your S3 credentials. |
| `AWS_AccessKey`, `AWS_SecretKey` | Your AWS credentials, if needed. |
| `OW_APIkey` | Your OpenWhisk credentials, if needed. |
| `GCP_SecretKey` | You Google Cloud Platform credentials, if needed. |
| `SLURM_Token` | Your Slurm credentials, if needed. |

### Optional VSCode Setup

If using VSCode, this repo contains a [`settings.template.json`](./.vscode/settings.template.json) file with some pre-configured settings:

- Automatic Python formatting with Ruff.
- `pytest` configuration that is required to use the VSCode testing UI.
- Additional configuration for Python, the editor, and cSpell.

This repo also includes recommended VSCode extensions:

- **Ruff:** A Python linter and formatter.
- **markdownlint:** A Markdown linter and formatter.
- **cSpell:** A code spell checker.

## Creating an Integration Test

When creating your own integration test, it is recommended to follow this pattern:

1. Commit the Dockerfiles of any custom containers to the `docker` directory.
2. Commit the workflow's functions to the `functions` directory.
3. Commit the workflow's schema to the `workflows` directory.
4. Commit your tests written with `pytest` to the `integration_tests` directory.

When finished, create a pull request. Then, when you contribute your proposed changes, you can link to your pull request on this repo.

## Updating the `FaaSr-workflow` Subtree

The `FaaSr-workflow` repository is included as a git submodule. Changes to the upstream repository can be done automatically with `pull_faasr_workflow.sh`.

If you are testing changes that you made to a fork or branch of the `FaaSr-workflow` repository, you will have to pull them manually with the following `git subtree` command:

```bash
git subtree pull \
    --prefix faasr_workflow git@github.com:<Username>/<Repo Name>.git \
    --squash \
    -m "Pull Faasr-workflow subtree" \
    <Branch Name>
```

## Script Reference

### `register_workflow.sh`

Register a workflow on your repository. This calls the FaaSr-workflow `register_workflow.py` script and immediately pull the latest changes to the remote branch.

**Options:**

- **`-f|--workflow-file`:** The file of the workflow to register.
- **`-c|--custom-container`:** Allow custom containers.
- **`-h|--help`:** Show a help message.

**Example usage:**

```bash
./register-workflow.sh -f workflows/IntegrationTestWorkflow.json

# Register a workflow with custom containers enabled
./register-workflow.sh -f workflows/IntegrationTestWorkflow.json -c
```

### `invoke_workflow.sh`

Invoke a workflow and monitor its progress. This calls the testing framework's Workflow Runner directly.

**Options:**

- **`-f|--workflow-file`:** The file of the workflow to invoke.
- **`-h|--help`:** Show a help message.

**Example usage:**

```bash
./invoke-workflow.sh -f workflows/IntegrationTestWorkflow.json
```

### `pull_faasr_workflow.sh`

Pull the latest changes from the upstream FaaSr-workflow repo to the FaaSr-workflow subtree. See [Updating the `FaaSr-workflow` Subtree](#updating-the-faasr-workflow-subtree).
