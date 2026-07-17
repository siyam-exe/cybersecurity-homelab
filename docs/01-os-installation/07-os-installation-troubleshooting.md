# OS Installation Troubleshooting

This page keeps install problems separate from the clean build path. If your installation worked normally, you do not need to apply every fix here.

## USB Does Not Boot

**Symptom**

The homelab does not show the Ubuntu installer, or it skips the USB drive.

**Likely cause**

The USB was written with settings that did not match the homelab boot mode.

**Working fix**

Recreate the USB with Rufus using:

| Rufus Setting | Value |
|---|---|
| Partition scheme | MBR |
| Target system | BIOS or UEFI-CSM |
| File system | FAT32 |
| Write mode | ISO image mode |

If Rufus asks to download GRUB files, allow it.

## Installer Freezes At Package Or Mirror Steps

**Symptom**

The installer appears to hang while reading package lists, selecting mirrors, or preparing installation packages.

**Likely cause**

Network mirror checks can behave badly on some installs.

**Working fix**

**Restart the installation with Ethernet disconnected**, complete the base install, then reconnect Ethernet after the first boot and run:

```bash
sudo apt update
sudo apt upgrade -y
```

## Installer Crashes Or Loops

**Symptom**

The installer fails and restarting from the menu does not cleanly recover.

**Working fix**

Fully power off the homelab, wait briefly, then boot from the USB again and restart the installation.

## Failed To Unmount Installer Media

**Symptom**

After removing the USB, the installer warns that it cannot unmount the installation media.

**Meaning**

This can happen at the end of installation and is usually harmless once the system has already been installed.

**Working fix**

Reboot and confirm the homelab starts from the internal disk.

## Network Does Not Come Up After Install

**Symptom**

The homelab boots, but remote SSH does not work and the network looks unavailable.

**Working fix**

1. Confirm Ethernet is connected.
2. Reboot the server:

```bash
sudo reboot
```

3. After login, check addresses:

```bash
ip addr
```

## Netplan Mistake During Static IP Testing

This lab ultimately prefers router DHCP reservation when possible. If you manually edit Netplan and lose network access, use the physical homelab keyboard and monitor.

Helpful commands:

```bash
ls /etc/netplan/
ip link show
sudo netplan try
sudo netplan apply
```

`sudo netplan try` is safer than immediately applying a remote-breaking configuration because it can roll back if you do not confirm it.

## Next Step

Continue to [Remote Access And Firewall](../02-remote-access-and-firewall/01-tailscale-setup.md).
