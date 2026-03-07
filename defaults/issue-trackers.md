# Issue Tracker Configuration

This file configures issue tracker integration for the task system. When
starting a new task, the agent can fetch issue details from a configured
tracker to seed the task definition.

Projects can override this file entirely by placing an `issue-trackers.md`
in their project config directory (e.g., `.task-system/issue-trackers.md`).

## Environment Variable Names

These are the default environment variable names used to read API credentials.
Override them in your project's `issue-trackers.md` if the defaults conflict
with existing environment variables in your project.

### Linear

- **API Key Variable**: LINEAR_API_KEY

The value should be a Linear personal API key. Generate one at
https://linear.app/settings/api (select "Personal API keys").

### Gitea

- **URL Variable**: GITEA_URL
- **API Token Variable**: GITEA_API_TOKEN

The URL should be the base URL of your Gitea instance (e.g.,
`https://gitea.example.com`). Do not include a trailing slash or API path.

The token should be a personal access token with `read:issue` scope.
Generate one at `<your-gitea-url>/user/settings/applications`.

## Gitea Settings

<!-- If using Gitea, uncomment and configure the default repo so you can
     reference issues with just #N instead of owner/repo#N: -->

<!-- - **Default Repo**: owner/repo -->
