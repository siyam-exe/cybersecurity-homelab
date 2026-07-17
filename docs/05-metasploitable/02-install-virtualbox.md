# Install VirtualBox

VirtualBox runs the Metasploitable VM on the homelab. The homelab is still managed like a server, so the VM is controlled with `VBoxManage` commands instead of relying on a desktop GUI.

## Install VirtualBox

Run:

```bash
sudo apt update
sudo apt install -y virtualbox
```

## Verify VirtualBox

Run:

```bash
VBoxManage --version
VBoxManage list hostonlyifs
```

Expected result:

- VirtualBox prints a version
- `VBoxManage` commands work from the terminal
- host-only interfaces are listed if any already exist

## Install Helper Tools

If you need to extract downloaded VM archives:

```bash
sudo apt install -y unzip
```

## Folder Layout

Keep VM files in a predictable folder:

```bash
mkdir -p /home/<HOMELAB_USER>/vms/metasploitable2
```

This guide uses `/home/<HOMELAB_USER>/vms/metasploitable2` as the placeholder VM location.

## Next Step

Continue to [Create Metasploitable VM](./03-create-metasploitable-vm.md).
