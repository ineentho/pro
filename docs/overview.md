# Renovate Pro Overview

## Introduction

Renovate Pro is a commercial offering of Renovate for self-hosted users, such as those running GitHub Enterprise or GitLab CE/EE.

Essentially, it is an alternative to running the `renovate` CLI tool, with the following benefits:

- Stateful job queue for prioritisation of job importance and resumption after interruption
- Embedded job scheduler to remove the need to set up and monitor `cron`
- Webhook listener to enable dynamic reactions to repository events

## Architecture

Logically, Renovate Pro consists of four components:

1.  In-memory DB
    - Used for keeping the job queue and a list of known installations and repositories
2.  Scheduler
    - Runs according to a `cron` schedule (defaults to hourly)
    - Retrieves a list of all installed repositories and adds them to the job queue with lowest priority
3.  Webhook Handler
    - Listens for webhook events from GitHub/GitLab
    - Adds higher priority jobs to the job queue if event conditions are met (e.g. a merged or closed Renovate PR, an update to an existing Renovate PR, a commit to `renovate.json` in `master` branch, etc)
4.  Worker
    - Runs non-stop, retrieving the highest priority job (repository) from the queue one at a time

All four components run within a shared container.

## Downloading

The Renovate Pro image is available via public Docker Hub using the namespace [renovate/pro](https://hub.docker.com/r/renovate/pro/).
Use of the image is as described on Docker Hub, i.e. in accordance with the [Renovate User Agreement](https://renovatebot.com/user-agreement).

## Versioning

Renovate Pro uses its own semantic versioning, separate from Renovate OSS versioning.

Additionally, it is intended that Renovate Pro will have a slower release cadence than Renovate OSS in order to provide greater stability for Enterprise use.

Specifically for Renovate Pro's use of SemVer:

**Major**: Used only for breaking changes

**Minor**: Used for feature additions and any bug fixes considered potentially unstable

**Patch**: Used only for bug fixes that are considered to be stabilizing

i.e. we do not want to ever "break" anyone with a patch release, or have behavior change.

Renovate OSS feature releases (i.e. minor version bumps in Renovate OSS) will therefore only be incorporated into minor releases of Renovate Pro.

Typically, multiple Renovate OSS feature releases will be rolled up into a single Renovate Pro release, and release notes will be embedded so that you do not need to look them up separately.

## Releasing and Upgrading

The release cadence of Renovate Pro is not fixed, as it will be determined largely by the importance and stability of new Renovate OSS features, which will typically be tested using the hosted Renovate GitHub App first.

When a new version of Renovate Pro is pushed to Docker Hub, Release Notes will be added to this [github.com/renovatebot/pro](https://github.com/renovatebot/pro) repository.

Meanwhile, we may publish unversioned "next" tags to Docker Hub between releases, e.g. incorporating bleeding edge updates of Renovate Pro features and/or Renovate OSS.

It is not recommended that you adopt "next" as your source tag for Renovate Pro, but there may be times when you wish to test a new Renovate OSS feature and that is the recommended option. It is best to pin your local Renovate Pro to a versioned release, e.g. `0.13.1`.

Naturally, it is recommended that you use Renovate itself for detecting and updating Renovate Pro versions if you are using a Docker Compose file internally for running Renovate Pro.

## Running Renovate Pro

[Examples using Docker Compose](https://github.com/renovatebot/pro/blob/master/examples/) can be found in the `examples/` directory of this repository.
