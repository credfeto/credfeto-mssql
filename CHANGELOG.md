# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

<!--
Please ADD ALL Changes to the UNRELEASED SECTION and not a specific release
-->

## [Unreleased]
### Security
- Host egress firewall in observation mode (`firewall/configure`, run by `install` and `update`): one nftables table, `inet sql_egress`, loaded atomically by `firewall/egress`, lists what the host (`host_out`) and the containers (`docker_fwd`) may connect to: the fleet DNS servers, NTP to the router, the reverse proxies, monitoring, GitHub, MCR, Docker Hub, Cloudflare and the Arch services. Resolved destinations are refreshed every 10 minutes by `sql-egress-refresh.timer` and kept for 7 days, `sql-firewall-sync.timer` re-applies the rules when `firewall/` changes, and the daily `Repo: Update egress ranges` workflow refreshes the GitHub and Cloudflare ranges. Unmatched connections are only logged (`sql-egress-unmatched:`, `sql-egress-docker-unmatched:`) until `MSSQL_FIREWALL_ENFORCE=true` is set in `.env`. Inbound connections to SQL Server on 1433 are not affected
### Added
- mssql_restore volume mounted at /var/opt/mssql/backup, backed by /data/restore on the host, for backup and restore operations
- `migrate-logs` script to move all existing user database `.ldf` files from the data volume to the dedicated log volume and update the SQL Server catalog
- `mssql.conf` bind-mounted from `/data/mssql/config/mssql.conf` to configure `filelocation.defaultlogdir` so new databases default to the log volume
- `install` now creates `/data/mssql/config/mssql.conf` with the log directory configuration

### Fixed
- Use `MSSQL_SA_PASSWORD` environment variable instead of `SA_PASSWORD` for SQL Server 2022 compatibility
- Set volume directory ownership to `mssql` process (UID 10001, GID 0) in `install` and `update` scripts so SQL Server can access its data directories
- Set `/data/restore` to sticky 1777 so any user can scp/rsync backup files into it while the mssql process retains read access
- `migrate-logs`: invoke `sqlcmd` via `docker exec` — the tool is inside the SQL Server container, not installed on the VM host
- `migrate-logs`: pass `-C` (TrustServerCertificate) to sqlcmd so ODBC Driver 18 accepts the container's self-signed certificate

### Changed
- watchtower now removes old images after updating and checks for new images on a cron schedule (WATCHTOWER_SCHEDULE, default daily at 06:30, overridable in .env) instead of every 60 seconds (WATCHTOWER_POLL_INTERVAL)
### Deprecated
### Removed
### Deployment Changes
- Optional `.env` setting `MSSQL_FIREWALL_ENFORCE`: unset (the default) leaves the new egress firewall in observation mode; `true` drops unmatched outbound connections. Review `journalctl -k | grep sql-egress` before enforcing

<!--
Releases that have at least been deployed to staging, BUT NOT necessarily released to live.  Changes should be moved from [Unreleased] into here as they are merged into the appropriate release branch
-->

## [0.0.0] - Project created