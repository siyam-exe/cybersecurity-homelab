# First Login And Updates

After Ubuntu Server boots for the first time, update the operating system before installing anything else. This gives the lab a clean base and avoids chasing old package issues later.

## Log In Locally

At the homelab terminal, log in with the account created during installation.

You will not see password characters while typing the password. That is normal for Linux terminals.

## Update Package Lists

Run:

```bash
sudo apt update
```

Command breakdown:

| Part | Meaning |
|---|---|
| `sudo` | Run the command with administrator privileges. |
| `apt` | Ubuntu package manager. |
| `update` | Refresh the list of available packages and versions. |

This command does not upgrade packages yet. It only updates Ubuntu's package index.

## Install Available Upgrades

Run:

```bash
sudo apt upgrade -y
```

Command breakdown:

| Part | Meaning |
|---|---|
| `upgrade` | Install newer versions of installed packages. |
| `-y` | Automatically answer yes to the upgrade prompt. |

## Reboot

Run:

```bash
sudo reboot
```

The SSH server, kernel updates, and system services are cleaner after a reboot.

## Verify The System Came Back

After the homelab reboots, log in again and run:

```bash
uptime
```

Expected result:

- the command prints the current uptime
- the server is responsive after the update
- you are ready to configure SSH and remote access

## Next Step

Continue to [SSH Access](./05-ssh-access.md).
