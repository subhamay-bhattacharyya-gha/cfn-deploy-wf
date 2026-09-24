# CloudFormation Deploy Workflow

![Built with Claude Code](https://img.shields.io/badge/Built_with-Claude_Code-D97757?logo=anthropic&logoColor=white)&nbsp;![Release](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/actions/workflows/release.yaml/badge.svg)&nbsp;![Commit Activity](https://img.shields.io/github/commit-activity/t/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;![Last Commit](https://img.shields.io/github/last-commit/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;![Release Date](https://img.shields.io/github/release-date/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;![Repo Size](https://img.shields.io/github/repo-size/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;![File Count](https://img.shields.io/github/directory-file-count/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;![Issues](https://img.shields.io/github/issues/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;![CloudFormation](https://img.shields.io/badge/IaC-CloudFormation-orange?style=flat)&nbsp;![Top Language](https://img.shields.io/github/languages/top/subhamay-bhattacharyya-gha/cfn-deploy-wf)&nbsp;![Custom Endpoint](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/bsubhamay/abbe62f55bd98d1fa39302fc416e99cb/raw/cfn-deploy-wf.json?)

A reusable GitHub Actions workflow for deploying CloudFormation stacks with validation, linting, S3 upload, and parameterized deployments.

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

## Inputs

| Name | Description | Required | Default |
| ------ | ------------- | -------- | --------- |
| `environment` | GitHub environment (ci, devl, test, prod) | No | `ci` |
| `concurrency-group` | Concurrency group name for workflow runs | No | `auto-generated` |
| `aws-region` | AWS region for CloudFormation deployment | Yes | — |
| `aws-account-id` | AWS account ID for OIDC role assumption | Yes | — |
| `oidc-role-name` | IAM role name for OIDC authentication | Yes | — |
| `cfn-templates-s3-bucket` | S3 bucket for CloudFormation templates | Yes | — |
| `stack-name` | CloudFormation stack name | No | `repository-name` |
| `template-file` | Path to CloudFormation template | No | `infra/template.yaml` |
| `parameters-file` | Path to CloudFormation parameters file | No | `infra/parameters.json` |

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

## Workflow Steps

1. **Environment Check** - Validates environment input and generates CI suffix if needed
2. **Validate** - Checks CloudFormation template syntax and runs linting
3. **Upload to S3** - Uploads template to S3 with proper key structure
4. **Deploy** - Deploys the CloudFormation stack with parameters and CI suffix

## License

MIT
