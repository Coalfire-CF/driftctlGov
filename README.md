# driftctl
This repository is forked from upstream driftctl project with some minor changes to allow it to work correctly for GovCloud

## Installation
Official docs direct you to only download the binary from releases.

- Install "go" on your computer.
  - Use whichever method is appropriate for your OS.  I use WSL2 => Ubuntu on Windows, so I installed from apt.
- cd into the root of this repository
- Run `make simple-build`
  - You can attempt to run `make build`, but it didn't work for me.  This may have a lot to do with how go is installed on your system and the version.
- cd into "bin"
- chmod on driftctl binary to allow execute permissions.
- Copy binary into your PATH, I used `/usr/local/bin`

## Usage
https://docs.driftctl.com/0.40.0/usage

### Example
This is an example command I ran against a sandbox account (GovCloud), my user has AdministratorAccess:
```bash
AWS_PROFILE=pak-test-mgmt cpulimit -f -l 10 -- driftctl scan --from tfstate+s3://coalforge-us-gov-west-1-tf-state/**/*.tfstate --from tfstate+s3://pak-test-infra-us-gov-west-1-tf-state/**/*.tfstate --filter "!(Type=='aws_cloudfront_distribution')" --tf-provider-version 5.96.0
```

- I use `cpulimit` to limit the number of threads used by driftctl to avoid hitting AWS API limits.
  - https://stackoverflow.com/questions/75441891/driftctl-throttlingexception-rate-exceeded-on-aws
- Running against GovCloud leads to errors because driftctl tries to scan against non-existent resources like CloudFront.
  - I use `--filter "!(Type=='aws_cloudfront_distribution')"` to have it ignore CloudFront.
  - **This is the primary change made to this fork.**  The upstream appears to completely ignore this filtering.
- driftctl uses a default old AWS Provider version by default, I override with the latest version using `--tf-provider-version 5.96.0`
- You can use a glob pattern to simply provide the bucket name where your Terraform state is kept.
  - You can provide multiple bucket names if your state is stored in different places.

## This project is now in maintenance mode. We cannot promise to review contributions. Please feel free to fork the project to apply any changes you might want to make.

<p align="center">
  <img width="200" src="https://docs.driftctl.com/img/driftctl_dark.svg" alt="driftctl">
</p>

<p align="center">
  <img src="https://circleci.com/gh/snyk/driftctl.svg?style=shield"/>
  <img src="https://goreportcard.com/badge/github.com/snyk/driftctl"/>
  <img src="https://img.shields.io/github/license/snyk/driftctl">
  <img src="https://img.shields.io/github/v/release/snyk/driftctl">
  <img src="https://img.shields.io/github/go-mod/go-version/snyk/driftctl">
  <img src="https://img.shields.io/github/downloads/snyk/driftctl/total.svg"/>
  <img src="https://img.shields.io/docker/pulls/snyk/driftctl"/>
  <img src="https://img.shields.io/docker/image-size/snyk/driftctl"/>
  <a href="https://discord.gg/NMCBxtD7Nd">
    <img src="https://img.shields.io/discord/783720783469871124?color=%237289da&label=discord&logo=discord"/>
  </a>
</p>

<p align="center">
  Measures infrastructure as code coverage, and tracks infrastructure drift.<br>
  <strong>IaC:</strong> Terraform. <strong>Cloud providers:</strong> AWS, GitHub, Azure, GCP.<br>
  :warning: <strong>This tool is still in beta state and will evolve in the future with potential breaking changes</strong> :warning:
</p>

<details>
  <summary>Packaging status</summary>
  <a href="https://repology.org/project/driftctl/versions">
    <img src="https://repology.org/badge/vertical-allrepos/driftctl.svg" alt="Packaging status">
  </a>
</details>

## Why driftctl ?

Infrastructure drift is a blind spot and a source of potential security issues.
Drift can have multiple causes: from team members creating or updating infrastructure through the web console without backporting changes to Terraform, to unexpected actions from authenticated apps and services.

You can't efficiently improve what you don't track. We track coverage for unit tests, why not infrastructure as code coverage?

Spot discrepancies as they happen: driftctl is a free and open-source CLI that warns of infrastructure drifts and fills in the missing piece in your DevSecOps toolbox.


## Features

- **Scan** cloud provider and map resources with IaC code
- Analyze diffs, and warn about drift and unwanted unmanaged resources
- Allow users to **ignore** resources
- Multiple output formats

## Links

**[Documentation](https://docs.driftctl.com)**

**[Installation](https://docs.driftctl.com/installation)**

**[Discord](https://discord.gg/7zHQ8r2PgP)**

## Contribute

To learn more about compiling driftctl and contributing, please refer to the [contribution guidelines](.github/CONTRIBUTING.md) and the [contributing guide](docs/README.md) for technical details.

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification and is brought to you by these [awesome contributors](CONTRIBUTORS.md).

Build with ❤️️ from 🇫🇷 🇬🇧 🇯🇵 🇬🇷 🇸🇪 🇺🇸 🇷🇪 🇨🇦 🇮🇱 🇩🇪

## Security notice

All Terraform state and Terraform files in this repository are for unit test
purposes only. No running code attempts to access these resources (except to
create and destroy them, in the case of acceptance tests). They are just opaque
strings.
