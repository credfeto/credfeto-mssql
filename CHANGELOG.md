# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

<!--
Please ADD ALL Changes to the UNRELEASED SECTION and not a specific release
-->

## [Unreleased]

### Added

### Fixed

- Use `MSSQL_SA_PASSWORD` environment variable instead of `SA_PASSWORD` for SQL Server 2022 compatibility
- Set volume directory ownership to `mssql` process (UID 10001, GID 0) in `install` and `update` scripts so SQL Server can access its data directories

### Changed

### Deprecated

### Removed

### Security

### Deployment Changes

<!--
Releases that have at least been deployed to staging, BUT NOT necessarily released to live.  Changes should be moved from [Unreleased] into here as they are merged into the appropriate release branch
-->

## [0.0.0] - Project created
