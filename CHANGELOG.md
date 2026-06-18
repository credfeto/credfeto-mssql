# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

<!--
Please ADD ALL Changes to the UNRELEASED SECTION and not a specific release
-->

## [Unreleased]
### Security
### Added
- mssql_restore volume mounted at /var/opt/mssql/backup, backed by /data/restore on the host, for backup and restore operations

### Fixed
- Use `MSSQL_SA_PASSWORD` environment variable instead of `SA_PASSWORD` for SQL Server 2022 compatibility
- Set volume directory ownership to `mssql` process (UID 10001, GID 0) in `install` and `update` scripts so SQL Server can access its data directories
- Set `/data/restore` to sticky 1777 so any user can scp/rsync backup files into it while the mssql process retains read access

### Changed
### Deprecated
### Removed
### Deployment Changes
<!--
Releases that have at least been deployed to staging, BUT NOT necessarily released to live.  Changes should be moved from [Unreleased] into here as they are merged into the appropriate release branch
-->
## [0.0.0] - Project created