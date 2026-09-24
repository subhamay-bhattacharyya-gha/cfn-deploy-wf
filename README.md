# GitHub Reusable Workflow: CloudFormation Deploy

<!-- Row 1: Status - Most Important -->
[![Release](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/actions/workflows/release.yaml/badge.svg)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;[![GitHub Action](https://img.shields.io/badge/GitHub-Action-blue?logo=github)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;[![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/cfn-deploy-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/issues)&nbsp;[![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/cfn-deploy-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commits)

<!-- Row 2: Code Quality -->
[![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/cfn-deploy-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;[![Commits](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/cfn-deploy-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commits)

<!-- Row 3: Tech Stack -->
[![CloudFormation](https://img.shields.io/badge/CloudFormation-IaC-FF9900?logo=amazon&logoColor=white)](https://aws.amazon.com/cloudformation/)&nbsp;[![Built with Claude Code](https://img.shields.io/badge/Built_with-Claude_Code-D97757?logo=anthropic&logoColor=white)](https://claude.ai/)

<!-- Row 4: Repository Info -->
[![Files](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/cfn-deploy-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;[![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/cfn-deploy-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;[![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/cfn-deploy-wf)](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/releases)

<!-- Row 5: Custom Metrics -->
[![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/797e6561f94e91cec4262102715b2aa3/raw/cfn-deploy-wf.json)](https://gist.github.com/bsubhamay/797e6561f94e91cec4262102715b2aa3)

A reusable GitHub Actions workflow for deploying CloudFormation stacks with validation, linting, S3 upload, and parameterized deployments.

---

## Overview

This reusable GitHub Actions workflow provides a complete CI/CD pipeline for deploying AWS CloudFormation stacks. It includes:

- **CloudFormation Template Validation** - Validates template syntax and structure
- **CFN Lint** - Runs linting checks on CloudFormation templates
- **S3 Template Upload** - Uploads templates to a designated S3 bucket
- **CloudFormation Stack Deployment** - Deploys the stack with parameterized inputs
- **Environment-based CI Suffix** - Automatically generates unique suffixes for CI environments
- **Parameter Management** - Supports custom parameter files with automatic CiSuffix injection
- **AWS OIDC Authentication** - Uses OIDC for secure, keyless AWS authentication

---

## Prerequisites

- AWS OIDC authentication configured in GitHub (keyless authentication via OIDC)
- GitHub Environments configured (`ci`, `devl`, `test`, `prod`) with appropriate environment variables
- CloudFormation template and parameters file in your repository
- Proper IAM role with CloudFormation permissions configured for OIDC

---

## Inputs

| Name | Description | Required | Default |
| ------ | ------------- | -------- | --------- |
| `environment` | GitHub environment (ci, devl, test, prod) | No | `ci` |
| `concurrency-group` | Concurrency group name for workflow runs | No | `auto-generated` (format: `cfn-deploy-{environment}-{ref}`) |
| `stack-name` | CloudFormation stack name | No | `repository-name` |
| `template-file` | Path to CloudFormation template | No | `infra/template.yaml` |
| `parameters-file` | Path to CloudFormation parameters file | No | `infra/parameters.json` |
| `skip-template-validation` | Skip CloudFormation validation and linting | No | `false` |
| `skip-s3-upload` | Use existing template already in S3 | No | `false` |

---

## Example Usage

```yaml
name: Deploy CloudFormation Stack

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy to'
        required: true
        type: choice
        options:
          - ci
          - devl
          - test
          - prod

jobs:
  deploy:
    uses: subhamay-bhattacharyya-gha/cfn-deploy-wf/.github/workflows/cfn-deploy.yaml@main
    with:
      environment: ${{ github.event.inputs.environment }}
      aws-region: us-east-1
      aws-account-id: ${{ secrets.AWS_ACCOUNT_ID }}
      oidc-role-name: github-actions-role
      cfn-templates-s3-bucket: my-cfn-templates
      stack-name: my-application-stack
      template-file: infra/template.yaml
      parameters-file: infra/parameters.json
```

---

## Environment Variables

Configure these variables in your GitHub Environments (Settings → Environments → [environment-name]):

| Variable | Description | Required | Example |
| -------- | ----------- | -------- | ------- |
| `AWS_REGION` | AWS region for CloudFormation deployment | Yes | `us-east-1` |
| `AWS_ACCOUNT_ID` | AWS account ID for OIDC role assumption | Yes | `123456789012` |
| `AWS_OIDC_ROLE_NAME` | IAM role name for OIDC authentication | Yes | `github-actions-role` |
| `CFN_TEMPLATES_S3_BUCKET` | S3 bucket for storing CloudFormation templates | Yes | `my-cfn-templates` |
| `PRINT_DEBUG_INFO` | Enable debug output (optional) | No | `true` |

### Setting Up GitHub Environments

1. Go to repository Settings → Environments
2. Create environments: `ci`, `devl`, `test`, `prod`
3. For each environment, add the required variables listed above
4. (Optional) Configure deployment branches per environment for protection

---

## Workflow Steps

The workflow executes three sequential jobs:

### 1. Validate Job

- **Purpose**: Validates CloudFormation templates and runs linting checks
- **Actions**:
  - Validates template syntax using `cfn-validate-action@main`
  - Runs CloudFormation linting via `cfn-lint-action@v1.0.0`
  - Configures AWS OIDC credentials for template validation
  - Generates random 4-character hex CI suffix (lowercase) for CI environments only
  - Persists CI suffix as artifact for retry attempts
- **Skip Option**: `skip-template-validation: true` (useful for fast iterations)

### 2. Upload to S3 Job

- **Purpose**: Uploads validated templates to S3 for CloudFormation consumption
- **Actions**:
  - Uses `{repo-name}/{template-filename}` as S3 key
  - Constructs HTTPS URL for CloudFormation to access template
  - Stores template URL as output for deploy job
- **Skip Option**: `skip-s3-upload: true` (reuses template already in S3)

### 3. Deploy Job

- **Purpose**: Deploys the CloudFormation stack
- **Actions**:
  - Uses `cfn-deploy-action@v1.0.1` for stack deployment
  - Automatically injects `CiSuffix` parameter in CI environment
  - **CI environment**: Deletes existing stack, then creates fresh deployment
  - **Other environments**: Creates or updates existing stack
  - Treats "no updates needed" as success (not a failure)
  - Stores parameters as artifact (30-day retention) for failure recovery

---

## CI Suffix Behavior

The CI suffix is a 4-character random hex string (lowercase) injected as the `CiSuffix` parameter:

- **Generated**: Only in `ci` environment
- **Format**: Random hex digits (e.g., `a1b2`, `f3d5`)
- **Persisted**: Across retry attempts using GitHub artifacts
- **Injected**: Automatically into parameters file during deploy job
- **Purpose**: Allows multiple CI deployments to use unique resource suffixes (e.g., stack name like `my-stack-a1b2`)

---

## Common Use Cases

### Fast Iteration During Development

Skip validation and upload steps for faster feedback:

```yaml
jobs:
  deploy:
    uses: subhamay-bhattacharyya-gha/cfn-deploy-wf/.github/workflows/cfn-deploy.yaml@main
    with:
      environment: ci
      stack-name: my-dev-stack
      skip-template-validation: true
      skip-s3-upload: true
```

### Production Deployment with Full Validation

Ensure all checks pass before deploying to production:

```yaml
jobs:
  deploy:
    uses: subhamay-bhattacharyya-gha/cfn-deploy-wf/.github/workflows/cfn-deploy.yaml@main
    with:
      environment: prod
      stack-name: my-prod-stack
```

---

## Troubleshooting

### Template Validation Failures

- Review the validation output in the workflow logs
- Check template syntax with: `cfn-lint infra/template.yaml`
- Ensure all CloudFormation resource types are valid

### S3 Upload Issues

- Verify the S3 bucket exists and has proper permissions
- Check that the bucket name is correctly specified in environment variables
- Ensure the IAM role has `s3:PutObject` permissions

### Deployment Failures

- Check CloudFormation stack events in the AWS console
- Review the parameters file format (must be valid JSON)
- Verify the IAM role has permissions for the resources in the template
- Check artifacts for parameter details used during deployment

## License

MIT
