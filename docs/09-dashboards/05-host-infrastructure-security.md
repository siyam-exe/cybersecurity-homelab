# Host & Infrastructure Security

The Host & Infrastructure Security dashboard focuses on the homelab server and Docker environment.

It answers:

```text
What happened on the host and containers during the lab session?
```

![Host and Infrastructure Security dashboard](../../assets/dashboards/host-infrastructure-security.png)

## Dashboard Purpose

| Purpose | Explanation |
|---|---|
| Host security | Show SSH, PAM, sudo, package, and system events. |
| Docker visibility | Show container start, stop, exec, die, and network activity. |
| FIM visibility | Show changes to important lab config paths. |
| Lab state awareness | Confirm whether vulnerable targets were started or stopped. |

## Panel Layout

| Section | Panels |
|---|---|
| KPI row | SSH auth, sudo, Docker events, Docker exec, FIM changes, package/system changes |
| Charts | Host security events over time, auth event types, Docker start/stop/exec, container lifecycle by name, critical config/FIM paths, auth source IPs |
| Bottom table | Raw infrastructure logs |

## Detection Coverage

| Area | Examples |
|---|---|
| SSH/auth | SSH success/failure, invalid users, PAM sessions |
| Sudo | successful sudo, failed sudo, first-time sudo |
| Docker lifecycle | container started/stopped/died, network connected/disconnected |
| Docker exec | command launched inside a container |
| FIM | file added, modified, deleted |
| Package/system changes | install/remove/update and system events |

## Important Fields

| Panel | Fields |
|---|---|
| SSH/auth | `rule.groups`, `data.srcip`, `data.srcuser`, `data.dstuser` |
| Sudo | `rule.groups`, `rule.description`, `data.dstuser` |
| Docker events | `rule.id`, `rule.description` |
| Container lifecycle | `data.docker.Actor.Attributes.name` |
| FIM | `syscheck.path`, `rule.id`, `rule.description` |
| Raw logs | `timestamp`, `location`, `rule.description`, `rule.level`, `rule.id` |

## Docker Field Fix

One visual issue was caused by using:

```text
docker.Actor.Attributes.name
```

The correct indexed field is:

```text
data.docker.Actor.Attributes.name
```

The final dashboard uses the correct field.

## How To Use It

For a lab session:

1. Start the services needed for testing.
2. Run web or scan tests.
3. Open Host & Infrastructure Security.
4. Confirm which containers started.
5. Check Docker exec activity.
6. Check SSH/sudo activity.
7. Check FIM changes if configs were edited.

Example:

```text
Web test session
-> Docker start events for juice-shop, dvwa, caddy-juice
-> Caddy logs in Web Attack Lab
-> Docker stop events after cleanup
```

## What Not To Put Here

Do not turn this dashboard into a full vulnerability inventory.

Vulnerability Detection is useful, but the main Host & Infrastructure dashboard should focus on operational and security activity during lab sessions.

## Next Step

Continue to [Dashboard Import Export](./06-dashboard-import-export.md).
