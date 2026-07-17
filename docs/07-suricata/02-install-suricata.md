# Install Suricata

This section installs Suricata on `homelab` as a host service.

The official Suricata quickstart recommends the OISF stable PPA on Ubuntu, then installing Suricata and `jq`. `jq` is useful because Suricata writes JSON events to `eve.json`, and `jq` lets you inspect those events without guessing.

## Install Packages

Run on `homelab`:

```bash
sudo apt-get update
sudo apt-get install -y software-properties-common
sudo add-apt-repository -y ppa:oisf/suricata-stable
sudo apt-get update
sudo apt-get install -y suricata jq
```

Command breakdown:

| Part | Meaning |
|---|---|
| `software-properties-common` | Provides `add-apt-repository`. |
| `ppa:oisf/suricata-stable` | Adds the official OISF stable Suricata package source for Ubuntu. |
| `suricata` | Installs the IDS engine. |
| `jq` | Parses Suricata EVE JSON output during testing. |

## Confirm Build Features

Run:

```bash
suricata --build-info
```

Look for:

```text
AF_PACKET support: yes
Detection enabled: yes
```

Why this matters:

- `AF_PACKET` is the Linux packet capture mode used in this lab.
- Detection support confirms Suricata can load rules and generate alerts.

## Disable Auto-Start

Suricata should not automatically start after every reboot.

Run:

```bash
sudo systemctl disable --now suricata
systemctl is-enabled suricata
systemctl is-active suricata
```

Expected result:

```text
disabled
inactive
```

Command breakdown:

| Part | Meaning |
|---|---|
| `disable` | Prevents Suricata from starting automatically at boot. |
| `--now` | Also stops it immediately if it is currently running. |
| `is-enabled` | Confirms the boot policy. |
| `is-active` | Confirms the current runtime state. |

## Create A Backup Before Editing

Before changing Suricata configuration, create a timestamped backup:

```bash
STAMP=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/home/<HOMELAB_USER>/homelab-change-backups/suricata-$STAMP"
mkdir -p "$BACKUP_DIR"
sudo cp /etc/suricata/suricata.yaml "$BACKUP_DIR/suricata.yaml.before"
```

Command breakdown:

| Part | Meaning |
|---|---|
| `STAMP=$(date +%Y%m%d-%H%M%S)` | Creates a unique timestamp for the backup folder. |
| `BACKUP_DIR=...` | Stores the backup path in one variable. |
| `mkdir -p` | Creates the backup directory if it does not already exist. |
| `cp ... suricata.yaml.before` | Saves the original Suricata configuration before changes. |

## Update Suricata Rules

Run:

```bash
sudo suricata-update
```

This fetches the default ET Open ruleset and writes the combined rule file used by Suricata.

## Validate The Base Config

Run:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Command breakdown:

| Part | Meaning |
|---|---|
| `-T` | Test mode. Suricata validates configuration and rules without starting packet capture. |
| `-c /etc/suricata/suricata.yaml` | Uses the installed Suricata config file. |

Expected result:

```text
Configuration provided was successfully loaded.
```

The exact wording may vary by Suricata version, but the important part is that the test exits cleanly and reports no failed rule load.

## Start And Stop Manually

Start Suricata only during IDS testing:

```bash
sudo systemctl start suricata
systemctl is-active suricata
```

Stop it after testing:

```bash
sudo systemctl stop suricata
systemctl is-active suricata
```

Expected final idle state:

```text
inactive
```

## Next Step

Continue to [Monitor vboxnet0](./03-monitor-vboxnet0.md).
