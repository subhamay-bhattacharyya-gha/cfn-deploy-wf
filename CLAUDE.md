# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**cfn-deploy-wf** is a reusable GitHub Actions workflow for automated CloudFormation stack deployment. It's designed to be called from other repositories to provide a complete CI/CD pipeline with template validation, S3 upload, and stack deployment using AWS OIDC authentication.

**Key Type**: GitHub Actions reusable workflow + release management  
**Language**: YAML (workflows) + JavaScript (release configuration)  
**Version Management**: Semantic versioning via semantic-release

## Architecture

### Core Workflow: `.github/workflows/cfn-deploy.yaml`

The main reusable workflow has three sequential jobs:

1. **validate** — Validates CloudFormation templates and lints them
   - Runs template syntax validation via `cfn-validate-action@main`
   - Runs CloudFormation linting via `cfn-lint-action@v1.0.0`
   - Configures AWS credentials and generates CI suffix for CI environments
   - Generates random 4-character hex suffix (lowercase) for CI deployments
   - Can be skipped with `skip-template-validation: true`

2. **upload-to-s3** — Uploads validated templates to S3
   - Uses repo name + template filename as S3 key
   - Constructs HTTPS URL for CloudFormation consumption
   - Can be skipped with `skip-s3-upload: true` (uses existing template from S3)

3. **deploy** — Deploys the CloudFormation stack
   - Uses `cfn-deploy-action@v1.0.1` for actual stack deployment
   - Injects `CiSuffix` parameter into parameters file automatically (CI environment only)
   - For CI environment: deletes existing stack first, then creates fresh
   - For other environments: creates or updates existing stack
   - Treats "no updates needed" as success (not a failure)
   - Stores parameters as artifact for failure recovery (30-day retention)

### Supporting Workflows

- **release.yaml** — Runs semantic-release on main branch merges
- **claude-code-review.yaml** — Automated PR reviews via Claude Code
- **create-branch.yaml** — Helper for branch creation
- **notify.yaml** — Notification workflow

### Release Configuration

Located in `scripts/plugins/`:
- Uses semantic-release with standard plugins
- Generates CHANGELOG.md from conventional commits
- Supports custom analyzers, note generators, and prepare/publish hooks
- Configuration in `.releaserc.json` and `scripts/plugins/release.config.js`

## Inputs to the Workflow

The reusable workflow accepts these inputs (from `.github/workflows/cfn-deploy.yaml`):

| Input | Type | Default | Notes |
|-------|------|---------|-------|
| `environment` | string | `ci` | Must be: ci, devl, test, prod |
| `concurrency-group` | string | auto-generated | Defaults to `cfn-deploy-{environment}-{ref}` |
| `stack-name` | string | repository name | Final CloudFormation stack name |
| `template-file` | string | `infra/template.yaml` | Path to CloudFormation template |
| `parameters-file` | string | `infra/parameters.json` | Path to parameters JSON file |
| `skip-template-validation` | boolean | false | Skip validation for faster iterations |
| `skip-s3-upload` | boolean | false | Use existing template already in S3 |

## Environment Variables

These are read from GitHub environment variables (configured per environment):
- `AWS_REGION` — AWS region for deployment
- `AWS_ACCOUNT_ID` — AWS account ID for OIDC
- `AWS_OIDC_ROLE_NAME` — IAM role name for OIDC authentication
- `CFN_TEMPLATES_S3_BUCKET` — S3 bucket for template storage
- `PRINT_DEBUG_INFO` — Optional: set to 'true' for debug output

## Development & Release

### Setup

```bash
# Install dependencies
npm install

# Verify semantic-release configuration
npm run release -- --dry-run
```

### Making Changes

1. Work on feature branch (e.g., `feature/GHA-XXXX-description`)
2. Commit using conventional commits (enforced by commitizen):
   ```bash
   npm run commit  # or git cz (if commitizen installed globally)
   ```
   
   Commit types:
   - `feat:` — New feature (triggers minor version bump)
   - `fix:` — Bug fix (triggers patch version bump)
   - `docs:` — Documentation only
   - `refactor:` — Code refactoring (no version bump)
   - `perf:` — Performance improvement (triggers patch)
   - `chore:` — Maintenance tasks

3. Create PR and ensure Claude Code Review passes

### Release Process

Releases are automated via semantic-release on merge to main:

```bash
# This happens automatically on push to main, but can be triggered locally:
npm run release
```

Semantic-release will:
1. Analyze commits to determine version bump (major.minor.patch)
2. Generate release notes and update CHANGELOG.md
3. Create git tag and push it
4. Create GitHub Release with changelog

## Key Files & Responsibilities

| File | Purpose |
|------|---------|
| `.github/workflows/cfn-deploy.yaml` | Main reusable workflow definition |
| `scripts/plugins/release.config.js` | Semantic-release configuration |
| `.releaserc.json` | Alternative release config format |
| `.github/workflows/release.yaml` | Trigger for semantic-release on main merges |
| `CHANGELOG.md` | Auto-generated release notes (do not edit directly) |
| `package.json` | Node.js dependencies and release config |

## CI Suffix Behavior

The CI suffix is a 4-character random hex string (lowercase) injected as the `CiSuffix` parameter:
- **Generated** only in `ci` environment
- **Persisted** across retry attempts using GitHub artifacts
- **Injected** into parameters file automatically during deploy job
- **Purpose**: Allows multiple CI deployments to same stack with unique suffixes

Example: If template has parameter `StackSuffix`, calling with `CiSuffix=a1b2` allows stack naming like `my-stack-a1b2`.

## Common Tasks

### Updating the Workflow

1. Edit `.github/workflows/cfn-deploy.yaml`
2. Test locally by understanding the YAML structure
3. Submit PR; Claude Code Review will check it
4. Merge to main; semantic-release handles versioning

### Debugging Failed Deployments

1. Check GitHub Actions run logs for the specific job
2. Look at CloudFormation stack events in AWS console
3. Review parameters passed (printed in "Display Input Parameters Summary" step)
4. Check artifact storage for recovered parameters on retry

### Testing Before Release

For local testing of semantic-release behavior:
```bash
npm run release -- --dry-run --verbose
```

This shows what version would be created and what changelog would be generated, without actually creating a release.

## Dependencies

- **semantic-release** ^25.0.9 — Automated versioning
- **@semantic-release/* plugins** — Changelog generation, git integration, GitHub releases
- **commitizen** ^4.3.2 — Interactive commit message formatter
- **cz-conventional-changelog** ^3.3.0 — Commit format conventions

All dev dependencies; this repo has no production dependencies.

## Notes for Future Work

- Template validation can be skipped for faster development iterations using `skip-template-validation: true`
- S3 upload can be skipped to reuse existing templates with `skip-s3-upload: true`
- GitHub environment variables control AWS configuration per environment (ci, devl, test, prod)
- The workflow uses OIDC for keyless AWS authentication (no static credentials stored)
- Parameters are validated as JSON and formatted for CloudFormation consumption
