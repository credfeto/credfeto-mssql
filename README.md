# cs-template

C# Template

## Build Status

| Branch  | Status                                                                                                                                                                                                                      |
|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| main    | [![Build: Pre-Release](https://github.com/credfeto/cs-template/actions/workflows/build-and-publish-pre-release.yml/badge.svg)](https://github.com/credfeto/cs-template/actions/workflows/build-and-publish-pre-release.yml) |
| release | [![Build: Release](https://github.com/credfeto/cs-template/actions/workflows/build-and-publish-release.yml/badge.svg)](https://github.com/credfeto/cs-template/actions/workflows/build-and-publish-release.yml)             |

## Watchtower update schedule

The `watchtower` service checks for updated container images on a cron schedule rather than a fixed poll interval,
and removes the old images after updating (`WATCHTOWER_CLEANUP`).

The schedule is set by `WATCHTOWER_SCHEDULE`, which defaults to `0 30 6 * * *` (daily at 06:30).
It is a 6-field cron expression with seconds first: `seconds minutes hours day-of-month month day-of-week`.

To use a different schedule, set it in the project's `.env` file, for example to check daily at 03:00:

```dotenv
WATCHTOWER_SCHEDULE=0 0 3 * * *
```

## Changelog

View [changelog](CHANGELOG.md)

## Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->
