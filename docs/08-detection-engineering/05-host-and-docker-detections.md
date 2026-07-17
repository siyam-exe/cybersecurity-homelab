# Host And Docker Detections

Not every useful detection in the lab is a web attack or network scan.

A realistic SIEM also needs to show what is happening on the server itself:

- SSH access
- sudo/admin activity
- Docker container lifecycle
- Docker exec commands
- FIM changes
- package/system changes
- vulnerability posture

Most of this comes from built-in Wazuh modules and rules.

## Host Authentication

| Activity | Source | Wazuh Visibility |
|---|---|---|
| SSH success | `/var/log/auth.log` | SSH/authentication success rules |
| SSH failure | `/var/log/auth.log` | SSH/authentication failure rules |
| Invalid user | `/var/log/auth.log` | SSH invalid user rules |
| PAM session open/close | `/var/log/auth.log` | PAM session rules |

Useful dashboard fields:

```text
rule.id
rule.description
rule.groups
data.srcip
data.srcuser
```

## Sudo And Admin Activity

| Activity | Source | Wazuh Visibility |
|---|---|---|
| Successful sudo | Ubuntu auth logs | Built-in sudo rules |
| Failed sudo | Ubuntu auth logs | Built-in sudo failure rules |
| First-time sudo | Ubuntu auth logs | Built-in sudo event rules |

This is useful because the homelab is administered over SSH. A beginner-friendly SOC dashboard should show whether admin actions happened during a test session.

## Docker Monitoring

Docker monitoring was enabled through the Wazuh Docker listener.

Useful built-in Docker rules:

| Rule ID | Meaning |
|---:|---|
| `87903` | Container started |
| `87904` | Container stopped |
| `87907` | Docker exec command launched |
| `87924` | Container killed/died |
| `87928` | Docker network connected |
| `87929` | Docker network disconnected |

Why this matters:

- vulnerable targets should be stopped unless testing
- unexpected container starts should be visible
- `docker exec` is important because it can indicate hands-on container access
- dashboard panels can prove whether Juice Shop, DVWA, and Caddy were running during a test

Important dashboard field:

```text
data.docker.Actor.Attributes.name
```

That is the field used for container names in the final dashboard.

## File Integrity Monitoring

FIM watches meaningful lab paths instead of only noisy default paths.

Important examples:

| Path Type | Why It Matters |
|---|---|
| Caddy config path | Web routing and logging behavior lives here. |
| Wazuh Docker config path | SIEM stack config changes should be visible. |
| rsyslog config path | Metasploitable log forwarding depends on it. |
| logrotate config path | Log retention and rotation behavior depends on it. |
| UFW config path | Firewall changes matter for lab exposure. |

Useful Wazuh FIM rules:

| Rule ID | Meaning |
|---:|---|
| `550` | File added |
| `553` | File modified |
| `554` | File deleted |

Dashboard field:

```text
syscheck.path
```

## Vulnerability Detection

Wazuh Vulnerability Detection shows package/CVE posture.

Use it as:

```text
posture visibility
```

Do not use it as:

```text
proof that an exploit happened
```

Exploit or attack evidence should come from logs, IDS alerts, FIM, Docker events, auth events, or application logs.

## Dashboard Placement

These detections appear mainly in:

```text
Host & Infrastructure Security
```

Useful panels:

| Panel | Purpose |
|---|---|
| SSH success/failure | Shows remote access activity. |
| Sudo activity | Shows admin actions. |
| Docker lifecycle | Shows lab targets and Wazuh containers starting/stopping. |
| Docker exec | Shows hands-on container commands. |
| FIM changes | Shows important config file changes. |
| Package/system changes | Shows host maintenance activity. |

## Next Step

Continue to [Detection Test Plan](./06-detection-test-plan.md).
