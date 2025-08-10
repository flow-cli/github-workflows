# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows for the flow-cli organization that can be used across multiple repositories.

## Available Workflows

### 1. Claude Code Review (`claude-code-review-reusable.yml`)

Automated code review using Claude AI for pull requests.

#### Usage

Create a workflow file in your repository (`.github/workflows/claude-review.yml`):

```yaml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    uses: flow-cli/github-workflows/.github/workflows/claude-code-review-reusable.yml@main
    with:
      model: 'claude-opus-4-1-20250805'  # Optional, defaults to opus
      use_sticky_comment: true           # Optional, defaults to true
      review_prompt: |                   # Optional, custom review prompt
        Your custom review instructions here
    secrets:
      CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

#### Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `model` | string | No | `claude-opus-4-1-20250805` | Claude model to use |
| `review_prompt` | string | No | Standard review prompt | Custom review instructions |
| `use_sticky_comment` | boolean | No | `true` | Reuse the same comment on updates |
| `allowed_tools` | string | No | `''` | Additional tools for Claude |
| `skip_conditions` | string | No | `[skip-review],[WIP]` | PR title patterns to skip review |

#### Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `CLAUDE_CODE_OAUTH_TOKEN` | Yes | OAuth token for Claude Code |

### 2. Claude PR Assistant (`claude-pr-assistant-reusable.yml`)

Interactive Claude assistant for issues and pull requests.

#### Usage

```yaml
name: Claude Code

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
  issues:
    types: [opened, assigned]
  pull_request_review:
    types: [submitted]

jobs:
  claude:
    uses: flow-cli/github-workflows/.github/workflows/claude-pr-assistant-reusable.yml@main
    with:
      trigger_phrase: '@claude'
      fetch_depth: 0
      custom_instructions: |
        Your project-specific instructions
    secrets:
      CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

#### Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `model` | string | No | `claude-sonnet-4` | Claude model to use |
| `trigger_phrase` | string | No | `@claude` | Phrase to trigger Claude |
| `allowed_tools` | string | No | `''` | Tools Claude can use |
| `custom_instructions` | string | No | `''` | Custom instructions |
| `claude_env` | string | No | `''` | Environment variables |
| `fetch_depth` | number | No | `0` | Git fetch depth |

#### Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `CLAUDE_CODE_OAUTH_TOKEN` | Yes | OAuth token for Claude Code |

### 3. Claude Issue Triage (`claude-issue-triage-reusable.yml`)

Automatic issue labeling using Claude AI.

#### Usage

```yaml
name: Claude Issue Triage

on:
  issues:
    types: [opened]

jobs:
  triage:
    uses: flow-cli/github-workflows/.github/workflows/claude-issue-triage-reusable.yml@main
    with:
      timeout_minutes: '5'
    secrets:
      CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

#### Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `model` | string | No | `claude-sonnet-4` | Claude model to use |
| `timeout_minutes` | string | No | `'5'` | Timeout in minutes |
| `triage_prompt` | string | No | Standard triage prompt | Custom triage instructions |
| `mcp_server_image` | string | No | `ghcr.io/github/github-mcp-server:sha-efef8ae` | GitHub MCP server image |

#### Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `CLAUDE_CODE_OAUTH_TOKEN` | Yes | OAuth token for Claude Code |
| `GITHUB_TOKEN` | Yes | GitHub token for MCP server |

## Setup Instructions

### 1. Set Up Secrets

#### Organization-level (Recommended)
1. Go to your organization settings
2. Navigate to Secrets and variables > Actions
3. Add `CLAUDE_CODE_OAUTH_TOKEN` as an organization secret
4. Choose which repositories can access the secret

#### Repository-level
1. Go to each repository's Settings
2. Navigate to Secrets and variables > Actions
3. Add `CLAUDE_CODE_OAUTH_TOKEN` as a repository secret

### 2. Use in Your Repositories

In any repository where you want to use these workflows:

1. Create `.github/workflows/` directory
2. Add a caller workflow file (see usage examples above)
3. Commit and push

## Benefits of Reusable Workflows

1. **Centralized Management**: Update workflows in one place
2. **Consistency**: Ensure all repos use the same workflow logic
3. **Version Control**: Pin to specific versions or branches
4. **Security**: Manage secrets at organization level
5. **Customization**: Pass different inputs per repository

## Versioning

You can reference workflows using:
- `@main` - Always use latest version
- `@v1.0.0` - Pin to specific release tag
- `@sha` - Pin to specific commit

## Contributing

To update these workflows:
1. Create a feature branch
2. Make your changes
3. Test in a sample repository
4. Create a pull request
5. Tag a new release after merging

## License

MIT License - see LICENSE file for details