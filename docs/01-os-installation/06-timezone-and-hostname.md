# Timezone And Hostname

Log timestamps matter in a SIEM lab. If the server timezone is wrong, Wazuh alerts, Suricata events, Caddy access logs, and authentication logs become harder to compare.

This section sets the timezone and confirms the hostname.

## Check The Current Time Settings

Run:

```bash
timedatectl
```

Look at:

- local time
- timezone
- system clock synchronized

## List Timezones

Run:

```bash
timedatectl list-timezones
```

If the list is long, search visually for your region. Use the exact timezone name in the next command.

## Set The Timezone

Run:

```bash
sudo timedatectl set-timezone <LOCAL_TIMEZONE>
```

Use your real timezone value when running the command on your server.

## Verify

Run:

```bash
timedatectl
```

Expected result:

- timezone shows the value you selected
- local time is correct
- NTP synchronization is active or available

## Check The Hostname

Run:

```bash
hostnamectl
```

If you want to change the hostname:

```bash
sudo hostnamectl set-hostname <HOSTNAME>
```

Use a simple name. The lab documentation uses `<HOSTNAME>` as a placeholder.

## Why This Matters Later

Wazuh dashboards and alerts identify the homelab by agent name and hostname. A clean hostname makes the dashboard easier to read and helps keep future notes consistent.

## Next Step

Continue to [OS Installation Troubleshooting](./07-os-installation-troubleshooting.md).
