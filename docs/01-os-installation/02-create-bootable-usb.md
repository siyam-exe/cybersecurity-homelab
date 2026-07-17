# Create Bootable USB

The first practical step is preparing the Ubuntu Server installation USB from the attack_station. This is the installer that will be used to wipe and install the homelab.

## What You Need

| Item | Purpose |
|---|---|
| homelab server | Target machine for the homelab. |
| attack_station laptop | Used to download the ISO and prepare the USB. |
| USB drive | Ubuntu Server installer media. |
| Ubuntu Server ISO | Operating system image. |
| Rufus | Tool used to write the ISO to the USB drive on Windows. |
| Ethernet cable | Recommended for stable installation and first updates. |

> Important: the USB drive will be erased when Rufus writes the installer.

## Download Ubuntu Server

Download the Ubuntu Server ISO from the official Ubuntu website:

[Ubuntu Server download](https://ubuntu.com/download/server)

Use the LTS release chosen for your lab. This guide follows Ubuntu Server 22.04 LTS because that is the tested base for this homelab.

## Write The ISO With Rufus

On the attack_station:

1. Open Rufus.
2. Select the USB drive.
3. Select the Ubuntu Server ISO.
4. Use these settings:

| Rufus Setting | Value |
|---|---|
| Boot selection | Ubuntu Server ISO |
| Partition scheme | MBR |
| Target system | BIOS or UEFI-CSM |
| File system | FAT32 |
| Write mode | ISO image mode |

If Rufus asks to download additional GRUB files, allow it. This helps older hardware boot the installer correctly.

## Why These Settings

The homelab hardware used for this lab behaved better with the legacy-friendly Rufus layout. Using MBR and BIOS/UEFI-CSM avoids a common issue where older systems do not boot a USB prepared only for newer UEFI layouts.

## Expected Result

At the end of this step, you should have:

- a bootable Ubuntu Server USB drive
- the homelab connected to Ethernet
- keyboard and monitor connected to the homelab for installation

## Next Step

Continue to [Install Ubuntu Server](./03-install-ubuntu-server.md).
