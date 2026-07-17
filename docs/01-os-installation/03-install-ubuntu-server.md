# Install Ubuntu Server

This section installs Ubuntu Server on the homelab. The goal is a clean server install that can later run Docker, Wazuh, vulnerable targets, VirtualBox, and log collection.

## Boot From The USB

1. Insert the Ubuntu Server USB into the homelab.
2. Power on the homelab.
3. Open the one-time boot menu.
4. Select the USB drive.
5. Choose the default Ubuntu Server installer option.

On many older laptops or small servers, the one-time boot menu is opened with `F12` during startup.

## Installer Choices

Use the installer options below.

| Installer Screen | Choice |
|---|---|
| Language | English, unless you prefer another language |
| Keyboard | Your keyboard layout |
| Network | Ethernet with DHCP |
| Proxy | Leave blank unless your network requires one |
| Mirror | Default Ubuntu mirror |
| Storage | Use the full disk |
| LVM | Disabled for this beginner build |
| Profile username | Use your own `<HOMELAB_USER>` |
| Server name | Use a simple hostname such as `<HOSTNAME>` |
| SSH | Install OpenSSH server |
| Featured snaps | Skip |

## Storage Warning

Using the full disk will erase the selected disk.

That is intentional for this lab because the homelab is dedicated to the project. If your machine has important files, stop and back them up before continuing.

## Why Enable OpenSSH During Install

OpenSSH lets you manage the homelab from your attack_station after the first boot. Without SSH, every command would need to be typed directly on the homelab keyboard.

The final remote access model will later move toward Tailscale-based SSH, but the SSH server should still be installed during OS setup.

## Finish Installation

When the installer completes:

1. Reboot when prompted.
2. Remove the USB drive when the system asks.
3. Let the homelab boot from its internal disk.
4. Log in with the user account created during installation.

## Expected Result

After the reboot, the homelab should show a terminal login prompt. You should be able to log in locally using your `<HOMELAB_USER>` account.

## Next Step

Continue to [First Login And Updates](./04-first-login-and-updates.md).
