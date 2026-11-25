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

## Host egress firewall

`install` and `update` run `firewall/configure`, which limits what the SQL Server host and its containers may connect to, the same way as the key server and the DNS servers:

| Filter                                | Traffic                     | Allowed                                                                                                                                                                                                                    |
|---------------------------------------|-----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `host_out` (nftables `sql_egress`)    | host -> anywhere            | DNS to the fleet's DNS servers, NTP to the router, HTTPS to the reverse proxies (pacman cache, docker-cache, metrics, key lookups), journald uploads and syslog to monitoring, GitHub, MCR, Docker Hub, Cloudflare, chaotic-aur, pkgstats, the Arch WKD, ICMP |
| `docker_fwd` (nftables `sql_egress`)  | containers -> outside world | DNS, HTTPS to the reverse proxies (docker-cache), MCR, Docker Hub and Cloudflare (Watchtower)                                                                                                                             |

Only new outbound connections are filtered. SQL Server's clients connecting in on 1433 (the Technitium DNS servers' query logs and other apps) and SSH are not affected: replies to them are established traffic, which the table always accepts. SQL Server itself is given no outside access; its telemetry is already blocked by the fleet's DNS block list.

The whole table, address sets included, is replaced in a single `nft -f` transaction, so a refresh never makes firewalld reload, and a ruleset that fails to load leaves the previous one in place.

Two timers keep it current:

* `sql-egress-refresh.timer` (30 seconds after boot, then every 10 minutes) rebuilds the table's address sets for destinations without a stable address: names resolved from DNS (each address stays allowed for 7 days after it was last seen), plus GitHub's and Cloudflare's published ranges (`firewall/github-egress-ranges` and `firewall/cloudflare-egress-ranges`). It only loads the table when it differs from the loaded one.
* `sql-firewall-sync.timer` (every 15 minutes) pulls the checkout and re-runs `firewall/configure` when anything under `firewall/` changed since the commit it last applied. Container changes still wait for `update`.

The `Repo: Update egress ranges` workflow regenerates the two range files daily and commits any change to main, refusing a list that has shrunk by more than a fifth.

`MSSQL_FIREWALL_ENFORCE` in `.env` is the only switch. Unset or anything other than `true` is observation mode: every rule is in place, but traffic that matches none of them still flows and is logged with the prefix `sql-egress-unmatched:` or `sql-egress-docker-unmatched:` (rate-limited to 30 a minute). Run in observation mode first and check the journal for anything legitimate that is not covered:

```sh
sudo journalctl -k --since -1d | grep -E 'sql-egress-[a-z-]*unmatched'
```

then set `MSSQL_FIREWALL_ENFORCE=true` and run `./update`. Straight after enforcing, check that the DNS servers are still writing query logs and that an image pull from MCR works. To see the live rules:

```sh
sudo nft list table inet sql_egress
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
