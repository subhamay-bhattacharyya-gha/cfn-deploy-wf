## [1.3.1](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/compare/v1.3.0...v1.3.1) (2026-09-24)


### Bug Fixes

* update workflow configurations and README badges for clarity ([9d8d321](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commit/9d8d321e894998879ee3c82a4f9d5a66020b16bf))

# [1.3.0](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/compare/v1.2.0...v1.3.0) (2026-09-24)


### Bug Fixes

* comment out cfn-templates-s3-bucket input in CloudFormation deploy workflow ([cb841c8](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commit/cb841c8f97470eaedd08b0c91afc4853969c4b86))
* make aws-region input optional and update cfn-deploy-action usage ([6b7cfe1](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commit/6b7cfe1ef93c71bdaa04bbfb6ed59aa91968fa03))
* normalize CI suffix to lowercase and update stack name handling ([da29bb3](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commit/da29bb3650c5e61aed0fd81655e652eb29c1465f))
* update repository URLs after repository rename to cfn-deploy-wf ([22f1041](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commit/22f10410a71f0bda3e9a3c532d4cfec635f04de2)), closes [#18](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/issues/18)


### Features

* add CLAUDE.md for project guidance, update LICENSE and CONTRIBUTING.md links, and remove obsolete parameters.json ([e0ff24b](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commit/e0ff24b704a627f0370a6c2d8a1c9b68db0c0c7f))
* add debug info summary step to CloudFormation deploy workflow ([5d8c426](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commit/5d8c4260a4e8e401690765a0260a7611ad8a5a9b))
* update CODEOWNERS and add Claude Code Review workflow ([5c772f6](https://github.com/subhamay-bhattacharyya-gha/cfn-deploy-wf/commit/5c772f6304dde8d4131c960abef7b17b06dc48ba))

# [1.2.0](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/compare/v1.1.0...v1.2.0) (2026-09-15)


### Features

* add options to skip CloudFormation template validation and S3 upload for faster development ([b4313a7](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/b4313a7ae919f95dfeb135b2225826a22c65d3fa))

# [1.1.0](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/compare/v1.0.0...v1.1.0) (2026-09-10)


### Bug Fixes

* update badge to reflect correct branding for Claude Code ([41e5fd2](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/41e5fd23e089181910f7ce1a5f71e91647c55c49))


### Features

* add step to extract parameters file directory path ([e0e44ae](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/e0e44aecc771b3b09c2ce9fe989a17fe8e1f2a4d))
* expose parameters-file-path as job output ([5f74a98](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/5f74a98f26c64b04b1fa2c4411246b4ecd1b4ef5))

## [1.1.2](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/compare/v1.1.1...v1.1.2) (2025-05-21)


### Bug Fixes

* update devcontainer configuration to include Node.js feature ([a6d9d47](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/a6d9d478096bf3f7a94f5fd44e26c3deb6e2611c))

## [1.1.1](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/compare/v1.1.0...v1.1.1) (2025-05-19)


### Bug Fixes

* add missing permissions for issue assignment workflow ([6c1c99c](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/6c1c99cb15f3df2cda6f7e8ea385447d43011bd7))

# [1.1.0](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/compare/v1.0.0...v1.1.0) (2025-05-19)


### Features

* add workflow to auto create branches on issue assignment ([c598e00](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/c598e002938006d48354017d1131d1f43e378393))

# 1.0.0 (2025-05-18)


### Bug Fixes

* update release configuration path in package.json ([d94b611](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/d94b61152ef216a98f5303e2ed2d78dbe309dd0e))


### Features

* add plugins for analyzing commits, generating release notes, preparing release, publishing, and verifying conditions ([60bfe70](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/60bfe70c3415559965a970971676f25a960d884f))
* add release configuration for semantic release ([bc81687](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/bc81687811d3ba20fb35a813afd29d222b37dbe0))
* initialize semantic release setup with custom plugins ([3a795e3](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/3a795e3f38397cceb825de4380c5d88907a3b744))
* migrate release configuration from release.config.js to .releaserc.json ([1351e55](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/1351e55fedf58fa9fb9bba217eecf1dba18c5a5c))
* reorder badges in README for better visibility ([f594ac6](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/f594ac67198dddcb460c3e8f14b06ecc05dc7c36))
* update actions/checkout and actions/setup-node to v4 ([ea8ec0d](https://github.com/subhamay-bhattacharyya-gha/cloudformation-deploy-wf/commit/ea8ec0d94afac30900f7d7229330ad4e6cc00a3d))

# Changelog

All notable changes to this project will be documented in this file.
