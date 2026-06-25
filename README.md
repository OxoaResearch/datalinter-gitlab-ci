# DataLinter GitLab CI

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

A GitLab CI to run **[DataLinter](https://github.com/zgornel/DataLinter)**—a contextual data and code linter for machine-learning and statistical-modelling workflows—inside a reproducible Docker container. Results are automatically posted as a comment on pull requests.

## Table of Contents

- [What is DataLinter?](#what-is-datalinter)
- [Features](#features)
- [Quick Start](#quick-start)
- [Inputs](#inputs)
- [Outputs](#outputs)
- [Workflow Examples](#workflow-examples)
- [How It Works](#how-it-works)
- [DataLinter Configuration](#datalinter-configuration)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## What is DataLinter?

[DataLinter](https://github.com/zgornel/DataLinter) detects potential issues in data files and associated code (R, Python, etc.) used in ML/statistical pipelines.

## Features

- Reproducible execution via a pre-built Docker image (`ghcr.io/zgornel/datalinter-compiled:latest`).
- Automatic mounting of data, code, and configuration directories.
- Full output capture (stdout + stderr) wrapped in a Markdown code block.
- **Automatic PR commenting** (edits previous comment if it exists).
- Composite action—works on any runner that supports Docker and is configured for Docker-in-Docker (dind).
- Flexible logging levels.

## Quick Start

- Add `gitlab-ci.yml` to your main branch before creating a PR.
- Create a `GITLAB_TOKEN` CI/CD variable (Project or Personal Access Token with api scope).
- Ensure the CI/CD runner is configured for Docker-in-Docker (dind).

## Inputs

`datalinter-gitlab-ci` has the following inputs:

- `DATA_PATH` (**required**), path to the data relative to the `gitlab-ci.yml`
- `CODE_PATH` (**required**), path to code, relative to `gitlab-ci.yml`
- `CONFIG_PATH` (**required**), path to the `datalinter` `.toml` file configuration, relative to `gitlab-ci.yml`
- `LOG_LEVEL` (**optional**, default is `'debug'`), logging level; can be `'debug'`, `'info'`, `'warning'` and `'error'`.

## Outputs

Complete DataLinter output (stdout + stderr) formatted as a Markdown code block.

### How it works

1. The supplied paths are split into directory and filename components.
2. It pulls `ghcr.io/zgornel/datalinter-compiled:latest`
3. Three Docker volumes are mounted:
   - Code directory → `/tmp`
   - Data directory → `/_data`
   - Config directory → `/_config`

4. `datalinter` binary is executed inside the container.
5. All output is captured and stored in the `datalinter_output.txt`.
6. On `merge_request_event` or `push` on the MR branch, the output is automatically posted (or edited) as a MR comment.

## DataLinter Configuration

Create a `datalinter.toml` file in your repository. Full documentation and example configurations are available in the [DataLinter](https://github.com/zgornel/DataLinter) repository.

## Troubleshooting

- **“No such file or directory”** → Verify the three paths exist relative to the checkout directory.
- **Output too verbose** → Set log-level: `'warning'` or `'error'`.

## Reporting Bugs

Please [file an issue](https://github.com/OxoaResearch/datalinter-gitlab-ci/issues/new) to report a bug or request a feature.

## License

This project is licensed under the MIT License. See LICENSE for details.
